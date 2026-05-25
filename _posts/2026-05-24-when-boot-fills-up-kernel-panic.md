---
title: "How a Full /boot Partition Rendered an Encrypted MX Linux System Unbootable"
date: 2026-05-24 23:00:00 +0200
categories: [Linux, Troubleshooting]
tags: [MX Linux, Debian, LUKS, Kernel Panic, initramfs, GRUB, Linux Recovery, /boot]
---

Linux systems have an interesting habit of continuing to operate normally even when something important underneath has already started falling apart.

A machine may boot, update packages, play media, browse the web, and behave completely normally while some hidden inconsistency quietly accumulates in the background. The actual failure often arrives later, usually after a reboot, when the system is forced to rely on boot artifacts that were generated days or weeks earlier.

That is essentially what happened here.

What initially looked like a catastrophic Linux failure eventually turned out to be a surprisingly mundane issue: a small dedicated `/boot` partition that had quietly filled up over time with old kernels and initramfs images.

The result, however, was anything but mundane.

The system eventually reached a state where:
- initramfs generation failed,
- kernel updates became partially incomplete,
- the machine panicked during boot,
- and recovery required manually unlocking encrypted partitions from a live environment.

Fortunately, the encrypted filesystem itself remained healthy the entire time. No reinstall was necessary and no data was lost.

Still, the recovery process exposed several interesting aspects of Linux system administration that are easy to ignore until something breaks badly enough to force attention.

---

## The Moment the System Failed

After a reboot, the machine immediately failed with:

```text
Kernel panic - not syncing:
VFS: Unable to mount root fs on unknown-block(0,0)
```

That error looks severe enough to make many people assume the disk itself has failed.

In this case, though, the clues pointed elsewhere.

The EFI partition was still accessible. The SSD passed diagnostics. The encrypted LUKS container remained detectable. Even the Linux bootloader files were still present.

The deeper issue was not hardware failure or filesystem corruption. The problem existed much earlier in the boot process.

---

## A Short Detour Through the Linux Boot Process

Encrypted Linux systems involve several layers before the actual operating system becomes usable:

```text
UEFI
 → GRUB
 → kernel
 → initramfs
 → cryptsetup/LUKS unlock
 → root filesystem mount
 → userspace
```

When one layer becomes inconsistent, the entire chain can collapse.

The interesting part is that Linux can sometimes continue running normally despite one of these layers already being broken on disk.

That is exactly what happened here.

---

## The Real Culprit: `/boot` Slowly Filled Up

The system used a dedicated `/boot` partition of roughly 1 GB.

At some point, that stopped being enough.

Over time, multiple kernels accumulated:

- 6.12.63
- 6.12.69
- 6.12.73
- 6.12.74
- 6.12.85
- 6.12.86
- 6.12.88
- 6.12.90

Each kernel also carried:
- initramfs images,
- System.map files,
- config files,
- bootloader references.

The machine eventually reached roughly 95% usage on `/boot`.

Checking it manually later revealed:

```bash
df -h /boot
```

At some point during package upgrades, the system attempted to generate a newer initramfs image and simply ran out of space.

The update process partially failed, but because the currently running kernel remained loaded in RAM, the machine continued operating normally.

Nothing looked obviously broken.

Until the reboot.

That reboot was essentially the first moment the system tried to rely on the newly generated — and incomplete — boot state.

---

## Recovery Begins

At that point, the machine was no longer bootable through the normal path.

Recovery required booting from an MX Linux live USB environment and manually reconstructing enough of the boot chain to regain access to the installed system.

The encrypted root partition first had to be opened manually:

```bash
cryptsetup luksOpen /dev/nvme0n1p3 cryptroot
```

After that, the root filesystem, `/boot`, and EFI partition were mounted:

```bash
mount /dev/mapper/cryptroot /mnt
mount /dev/nvme0n1p2 /mnt/boot
mount /dev/nvme0n1p1 /mnt/boot/efi
```

Pseudo-filesystems were bind-mounted into the recovery environment:

```bash
mount --bind /dev /mnt/dev
mount --bind /proc /mnt/proc
mount --bind /sys /mnt/sys
mount --bind /run /mnt/run
```

Then the installed system was entered using:

```bash
chroot /mnt
```

At that point, the real state of the machine became much clearer.

---

## Cleaning Up the Kernel Graveyard

The first genuinely important discovery was simply how many old kernels had accumulated.

On larger systems this might not matter much. On a tiny `/boot` partition, however, it becomes dangerous surprisingly quickly.

Older kernels were removed while keeping:
- the currently intended kernel,
- and one fallback kernel.

For example:

```bash
apt purge linux-image-6.12.63+deb13-amd64
```

Once several obsolete kernels disappeared, `/boot` finally had breathing room again.

That allowed initramfs generation to complete properly:

```bash
update-initramfs -u -k all
update-grub
```

At this point the machine actually advanced much further during boot.

Which is when the second issue appeared.

---

## The More Subtle Failure

Originally, the encrypted root device had been mapped using an automatically generated LUKS mapper name:

```text
/dev/mapper/luks-<UUID>
```

During manual recovery, however, the encrypted device had been opened as:

```text
cryptroot
```

That small difference created a surprisingly annoying inconsistency.

Parts of the system expected:

```text
/dev/mapper/luks-<UUID>
```

while others expected:

```text
/dev/mapper/cryptroot
```

The result was that filesystem checks during boot targeted a nonexistent device.

At that point the machine was no longer crashing with kernel panic, but it still could not complete the boot process.

---

## Repairing `/etc/crypttab` and `/etc/fstab`

The fix itself was simple once the inconsistency became visible.

The encrypted mapper name was standardized inside `/etc/crypttab`:

```text
cryptroot UUID=<uuid> none luks
```

Then `/etc/fstab` was updated so that the root filesystem consistently referenced:

```text
/dev/mapper/cryptroot
```

instead of the old autogenerated LUKS mapper path.

After regenerating initramfs once more:

```bash
update-initramfs -u -k all
update-grub
```

the machine finally booted normally again.

No reinstall. No filesystem corruption. No data loss.

Just a surprisingly long chain of consequences caused by a full `/boot` partition.

---

## What Actually Failed?

The interesting part about this entire incident is that the encrypted root filesystem itself never became damaged.

The SSD was healthy.

The EFI partition was healthy.

The Linux installation itself was mostly healthy.

The real problem was operational neglect accumulating quietly in the background:
- too many old kernels,
- too little free space in `/boot`,
- failed initramfs generation,
- and inconsistent mapper naming during manual recovery.

Linux simply delayed revealing the consequences until reboot time.

---

## Keeping This from Happening Again

The most important preventive measure is surprisingly simple:

Check `/boot` occasionally.

```bash
df -h /boot
```

On systems with small dedicated boot partitions, modern initramfs images consume space much faster than many people expect.

Keeping only:
- the current kernel,
- and one fallback kernel,

is usually enough for ordinary desktop systems.

It is also worth checking installed kernels periodically:

```bash
dpkg -l | grep linux-image
```

and verifying which kernel is actually running:

```bash
uname -r
```

Another useful habit is checking `/boot` usage before rebooting after major updates.

Linux can continue operating normally even while:
- initramfs generation has failed,
- kernel packages remain partially configured,
- or boot artifacts are inconsistent.

The reboot is often the first time those failures become visible.

One additional lesson from this incident was the value of a visible GRUB menu.

A hidden GRUB menu may look cleaner during normal operation, but it removes one of the simplest recovery paths available during kernel problems. Even a short timeout makes selecting an older fallback kernel dramatically easier.

---

## Final Thoughts

At first glance, the situation looked catastrophic.

An encrypted Linux system had dropped into kernel panic and become completely unbootable.

In reality, the underlying issue was much less dramatic:
a small partition quietly filled up over time until a routine kernel update could no longer complete successfully.

The interesting part is not that Linux failed.

The interesting part is how long Linux continued functioning despite the failure already existing underneath.

The reboot did not create the problem.

It merely revealed it.

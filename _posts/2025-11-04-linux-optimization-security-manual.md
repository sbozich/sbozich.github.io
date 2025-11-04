---
title: "Linux Optimization & Security Manual (Zorin & Ubuntu-Based Systems)"
date: 2025-11-04 22:45:00 +0100
categories: [Linux, ZorinOS]
tags: [Zorin, Ubuntu, Linux, Optimization, Security, Systemd, Flatpak]
author: "Sinisa Bozic"
author_url: "https://sbozich.github.io"
description: "Field-tested, reversible tweaks for Zorin OS 17.3 / 18 and Ubuntu-based systems: performance, power, and security."
image: /assets/gear.png
toc: true
toc_label: "Contents"
toc_icon: "list"
---

![Manual icon](https://sbozich.github.io/assets/gear.png)


In late 2025, with Windows 10 reaching end-of-support and millions of otherwise capable computers being declared “obsolete” by design, many users are looking for sustainable, secure ways to keep their machines productive.

Zorin OS — a polished, Ubuntu-based Linux distribution — provides a bridge between familiarity and freedom. It allows users to escape vendor-locked ecosystems while continuing to use existing hardware with a supported, modern operating system.

This manual was written to help anyone — whether migrating from Windows or refining an existing Linux setup — understand and apply **safe, meaningful optimizations**. It reflects real-world experience with **Zorin 17.3 and 18** (built on different Ubuntu bases), and most of the tweaks also apply to **Ubuntu 22.04 / 24.04 LTS, Linux Mint, and other Ubuntu-based systems**.

The goal is not to chase artificial benchmarks, but to balance **performance, stability, and community values** — so your system stays fast, reliable, secure, and transparent for years to come.

— **Sinisa Bozic** · [sbozich.github.io](https://sbozich.github.io)


---

## Contents

1. [System Overview](#️-1-system-overview)
2. [Core System Maintenance](#️-2-core-system-maintenance)
3. [Performance & Responsiveness](#️-3-performance--responsiveness)
4. [Power & Device Management](#️-4-power--device-management)
5. [GNOME / Wayland UX](#️-5-gnome--wayland-ux)
6. [Connectivity & Peripherals](#️-6-connectivity--peripherals)
7. [Security & Access](#️-7-security--access)
8. [Automation & Scheduling](#️-8-automation--scheduling)
9. [Firmware & Boot Integrity](#️-9-firmware--boot-integrity)
10. [Optional Reverts](#️-10-optional-reverts)
11. [Summary & Philosophy](#️-11-summary--philosophy)

---

<!--more-->




> **Updated for 2025 — Zorin 17.3 & Ubuntu 24.04 LTS**  
> This practical manual covers safe, reversible system optimizations for Zorin OS and other Ubuntu-based distros.  
> <!--more-->


## ⚙️ 1. System Overview

Target environments:  
• Zorin OS 17.3 and 18  
• Ubuntu 22.04 / 24.04 LTS  
• Linux Mint and derivatives  

**Guiding principles**  
1. Every change must be safe to undo.  
2. Optimizations must not break printing, Bluetooth, or Wi-Fi.  
3. Use upstream tools (systemd, UFW, APT).  
4. Keep automation transparent and reversible.

---

## 🧰 2. Core System Maintenance

### 2.1 Preserve APT auto-updates

Zorin inherits Ubuntu’s `unattended-upgrades` and Software Updater. Keep them enabled.

**Why:** maintains signed kernel and package updates.  
**Risk / Benefit:** Low / High — ensures secure patching.

---

### 2.2 Daily Flatpak Auto-Update (user level)

Ensures Flatpak apps stay current even without GUI open.

**✅ Apply**
```bash
mkdir -p ~/bin
cat <<'EOF' > ~/bin/zorin-flatpak-auto-update.sh
#!/usr/bin/env bash
LOGFILE="$HOME/.local/share/flatpak-auto.log"
mkdir -p "$(dirname "$LOGFILE")"
{ echo; echo "=== $(date '+%Y-%m-%d %H:%M:%S') ==="; flatpak update -y; } >> "$LOGFILE" 2>&1
EOF
chmod +x ~/bin/zorin-flatpak-auto-update.sh
```

Create systemd user timer:

```bash
mkdir -p ~/.config/systemd/user
cat <<'EOF' > ~/.config/systemd/user/zorin-flatpak-auto-update.service
[Unit]
Description=Automatic Flatpak updates (Zorin)
[Service]
Type=oneshot
ExecStart=%h/bin/zorin-flatpak-auto-update.sh
EOF

cat <<'EOF' > ~/.config/systemd/user/zorin-flatpak-auto-update.timer
[Unit]
Description=Run automatic Flatpak updates daily
[Timer]
OnCalendar=daily
Persistent=true
[Install]
WantedBy=default.target
EOF

systemctl --user daemon-reload
systemctl --user enable --now zorin-flatpak-auto-update.timer
```

**🔍 Verify**
```bash
systemctl --user list-timers | grep zorin-flatpak-auto-update
```

**↩️ Revert**
```bash
systemctl --user disable --now zorin-flatpak-auto-update.timer
rm ~/.config/systemd/user/zorin-flatpak-auto-update.*
rm ~/bin/zorin-flatpak-auto-update.sh
```

| Risk | Benefit |
|:----:|:---------|
| Minimal | Apps stay current automatically |

---

## ⚡ 3. Performance & Responsiveness

### 3.1 Lower swappiness

```bash
echo 'vm.swappiness=10' | sudo tee /etc/sysctl.d/90-swappiness.conf
sudo sysctl -p /etc/sysctl.d/90-swappiness.conf
```

**Revert:**  
`sudo rm /etc/sysctl.d/90-swappiness.conf && sudo sysctl -p`

| Risk | Benefit |
|:----:|:---------|
| Low | Snappier desktop on 8 GB+ RAM |

---

### 3.2 Enable weekly SSD TRIM
```bash
sudo systemctl enable --now fstrim.timer
```

---

### 3.3 Limit systemd journal size
```bash
sudo mkdir -p /etc/systemd/journald.conf.d
printf '%s\n' '[Journal]' 'SystemMaxUse=200M' | sudo tee /etc/systemd/journald.conf.d/size-limit.conf
sudo systemctl restart systemd-journald
```

---

### 3.4 Disable non-essential services (desktop)
```bash
sudo systemctl disable --now man-db.timer smartmontools.service
sudo systemctl mask NetworkManager-wait-online.service
```

---

## 🔋 4. Power & Device Management

### 4.1 TLP power optimizer
```bash
sudo apt install -y tlp tlp-rdw
sudo systemctl enable --now tlp
```

---

### 4.2 Lid switch policies

Desktop (ignore lid):  
`HandleLidSwitch=ignore`  
Laptop (suspend):  
`HandleLidSwitch=suspend`

Edit `/etc/systemd/logind.conf`, restart logind.

---

## 🖥️ 5. GNOME / Wayland UX

### 5.1 Refine screen-lock timing
```bash
gsettings set org.gnome.desktop.session idle-delay 900
gsettings set org.gnome.desktop.screensaver lock-delay 30
```

---

### 5.2 Hide “Activities” button
```bash
gsettings set org.gnome.shell.extensions.dash-to-dock show-show-apps-button false
```

---

### 5.3 Middle-click Show Desktop
```bash
sudo apt install -y xdotool
xdotool key Super+d
```

Bind via Settings → Keyboard → Custom Shortcuts.

---

## 🌐 6. Connectivity & Peripherals

### 6.1 Bluetooth resume
```bash
sudo systemctl enable --now bluetooth.service
```

### 6.2 Wi-Fi powersave fix
```bash
echo -e '[connection]\nwifi.powersave=2' | sudo tee /etc/NetworkManager/conf.d/wifi-powersave.conf
sudo systemctl restart NetworkManager
```

---

## 🔒 7. Security & Access

### 7.1 UFW Firewall
```bash
sudo ufw enable
sudo ufw allow ssh
sudo ufw allow 445/tcp
```

### 7.2 ClamAV scanner
```bash
sudo apt install -y clamav clamtk
sudo freshclam
```

### 7.3 rkhunter
```bash
sudo apt install -y rkhunter
sudo rkhunter --update
sudo rkhunter --check
```

### 7.4 Password managers
Bitwarden (cloud) or KeePassXC (offline).  
```bash
flatpak install flathub org.keepassxc.KeePassXC
```

---

## ⏱️ 8. Automation & Scheduling

List timers: `systemctl list-timers --all`  
APT cleanup: `sudo apt autoremove --purge && sudo apt clean`

Flatpak log:  
```bash
tail -20 ~/.local/share/flatpak-auto.log
```

---

## 🧬 9. Firmware & Boot Integrity

```bash
[ -d /sys/firmware/efi ] && echo "UEFI mode" || echo "Legacy BIOS mode"
mokutil --sb-state
sudo fwupdmgr get-updates
```

---

## 🧹 10. Optional Reverts

```bash
dconf reset -f /
sudo systemctl unmask NetworkManager-wait-online.service
sudo systemctl enable --now man-db.timer smartmontools.service
```

---

## 🧭 11. Summary & Philosophy

> Optimize because you understand — not because “some list said so.”

**Core takeaways**
1. Keep APT updates and UFW active.  
2. Automate Flatpak updates daily.  
3. Lower swappiness.  
4. Enable SSD TRIM weekly.  
5. Keep Secure Boot verified.  

---

**Author:** [Sinisa Bozic](https://sbozich.github.io) · 2025  
*Linux Optimization & Security Manual — Zorin & Ubuntu-based Systems*



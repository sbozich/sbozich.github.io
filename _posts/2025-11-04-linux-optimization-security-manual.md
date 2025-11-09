---
title: "Linux Optimization & Security Manual (Zorin & Ubuntu-Based Systems)"
date: 2025-11-04 
categories: [Linux, ZorinOS]
tags: [Zorin, Ubuntu, Linux, Optimization, Security, Systemd, Flatpak]
description: "Field-tested, reversible tweaks for Zorin OS 17.3 / 18 and Ubuntu-based systems: performance, power, and security."
---

![Manual icon](https://sbozich.github.io/assets/gear.png)


In late 2025, with Windows 10 reaching end-of-support and millions of otherwise capable computers being declared “obsolete” by design, many users are looking for sustainable, secure ways to keep their machines productive.

Zorin OS — a polished, Ubuntu-based Linux distribution — provides a bridge between familiarity and freedom. It allows users to escape vendor-locked ecosystems while continuing to use existing hardware with a supported, modern operating system.

This manual was written to help anyone — whether migrating from Windows or refining an existing Linux setup — understand and apply **safe, meaningful optimizations**. It reflects real-world experience with **Zorin 17.3 and 18** (built on different Ubuntu bases), and most of the tweaks also apply to **Ubuntu 22.04 / 24.04 LTS, Linux Mint, and other Ubuntu-based systems**.

The goal is not to chase artificial benchmarks, but to balance **performance, stability, and community values** — so your system stays fast, reliable, secure, and transparent for years to come.

— **Sinisa Bozic** · [sbozich.github.io](https://sbozich.github.io)


---

## Contents

- [System Overview](#️-1-system-overview--scope--safety-rules)
- [Core System Maintenance](#-2-core-system-maintenance--keep-base-layer-healthy)
- [Performance & Responsiveness](#-3-performance--responsiveness--reduce-io-and-background-load)
- [Power & Device Management](#-4-power--device-management--efficiency--longevity)
- [GNOME / Wayland UX and Session Behavior](#️-5-gnome--wayland-ux--minimalist-comfort)
- [Connectivity & Peripheral Stability](#-6-connectivity--peripherals--stable-wireless-and-bluetooth)
- [Security & Access](#-7-security--access--harden-without-bloat)
- [Automation & Scheduling](#️-8-automation--scheduling)
- [Firmware & Boot Integrity](#-9-firmware--boot-integrity)
- [Optional Cleanup / Reverts](#-10-optional-reverts--return-to-stock-state)
- [Summary & Philosophy](#-11-summary--philosophy)


---


<!--more-->




> **Updated for 2025 — Zorin 17.3 & Ubuntu 24.04 LTS**  
> This practical manual covers safe, reversible system optimizations for Zorin OS and other Ubuntu-based distros.  
> <!--more-->

## ⚙️ 1. System Overview — (Scope & Safety Rules)

**Target Environments**  
- Zorin OS 17.3 / 18  
- Ubuntu 22.04 / 24.04 LTS  
- Linux Mint and derivatives  

**Guiding Principles**  
1. Every tweak must be **fully reversible**.  
2. Never break **printing, Bluetooth, or Wi-Fi**.  
3. Use **upstream-supported mechanisms** only (`systemd`, `UFW`, `APT`).  
4. Keep **automation transparent** — no opaque cron jobs or hidden scripts.

---

## 🧰 2. Core System Maintenance — (Keep Base Layer Healthy)

### 2.1 Preserve APT Auto-Updates — (Kernel & Security Patching)
```bash
systemctl status unattended-upgrades
sudo systemctl enable --now unattended-upgrades
```
**Why:** ensures signed kernel and package updates remain in sync with Secure Boot.  
**Revert:** `sudo systemctl disable --now unattended-upgrades`  

| Risk | Benefit |
|:----:|:---------|
| Very Low | Ensures automatic kernel and security patching |

---

### 2.2 Daily Flatpak Auto-Update — (User-Level, GUI-Independent)
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
Create service and timer:
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
**Verify:**  
```bash
systemctl --user list-timers | grep flatpak
tail -n 10 ~/.local/share/flatpak-auto.log
```
**Revert:**  
```bash
systemctl --user disable --now zorin-flatpak-auto-update.timer
rm ~/.config/systemd/user/zorin-flatpak-auto-update.*
rm ~/bin/zorin-flatpak-auto-update.sh
```

| Risk | Benefit |
|:----:|:---------|
| Minimal | Keeps Flatpak apps current without GUI |

---

## ⚡ 3. Performance & Responsiveness — (Reduce I/O and Background Load)

### 3.1 Lower Swappiness — (Reduce Disk Thrashing)
```bash
cat /proc/sys/vm/swappiness
echo 'vm.swappiness=10' | sudo tee /etc/sysctl.d/90-swappiness.conf
sudo sysctl -p /etc/sysctl.d/90-swappiness.conf
```
**Why:** with ≥8 GB RAM, reduces swapping and improves responsiveness.  
**Revert:** `sudo rm /etc/sysctl.d/90-swappiness.conf && sudo sysctl -p`  

| Risk | Benefit |
|:----:|:---------|
| Low | Faster multitasking and reduced SSD wear |

---

### 3.2 Enable Weekly SSD TRIM — (Maintain Write Performance)
```bash
sudo systemctl enable --now fstrim.timer
systemctl status fstrim.timer
```
**Why:** trims unused blocks weekly, preserving SSD speed.  
**Revert:** `sudo systemctl disable --now fstrim.timer`  

| Risk | Benefit |
|:----:|:---------|
| None | Sustains SSD performance automatically |

---

### 3.3 Limit Journal Size — (Prevent Log Bloat)
```bash
sudo mkdir -p /etc/systemd/journald.conf.d
printf '%s\n' '[Journal]' 'SystemMaxUse=200M' | sudo tee /etc/systemd/journald.conf.d/size-limit.conf
sudo systemctl restart systemd-journald
```
**Why:** prevents logs from consuming disk space.  
**Revert:** `sudo rm /etc/systemd/journald.conf.d/size-limit.conf && sudo systemctl restart systemd-journald`  

| Risk | Benefit |
|:----:|:---------|
| Low | Prevents storage bloat without losing important logs |

---

### 3.4 Disable Non-Essential Services — (Reduce Boot Latency)
```bash
sudo systemctl disable --now man-db.timer smartmontools.service
sudo systemctl mask NetworkManager-wait-online.service
```
**Why:** disables slow or redundant background services.  
**Revert:**  
```bash
sudo systemctl unmask NetworkManager-wait-online.service
sudo systemctl enable --now man-db.timer smartmontools.service
```

| Risk | Benefit |
|:----:|:---------|
| Medium (loss of SMART alerts, slower manual man updates) | Shorter boot and lower background I/O |

---

## 🔋 4. Power & Device Management — (Efficiency & Longevity)

### 4.1 TLP Power Optimizer
```bash
sudo apt install -y tlp tlp-rdw
sudo systemctl enable --now tlp
sudo tlp-stat -s
```
**Why:** replaces multiple vendor daemons with one policy manager.  
**Revert:** `sudo systemctl disable --now tlp`  

| Risk | Benefit |
|:----:|:---------|
| Low | Longer battery life, cooler operation |

---

### 4.2 Lid Switch Policy
```bash
sudo nano /etc/systemd/logind.conf
```
Set desired behavior:  
`HandleLidSwitch=suspend` or `ignore` for desktops.  
Restart logind:  
```bash
sudo systemctl restart systemd-logind
```

| Risk | Benefit |
|:----:|:---------|
| Low | Predictable lid behavior; avoids accidental suspends |

---

## 🖥️ 5. GNOME / Wayland UX — (Minimalist Comfort)

### 5.1 Refine Screen Lock Timing
```bash
gsettings set org.gnome.desktop.session idle-delay 900
gsettings set org.gnome.desktop.screensaver lock-delay 30
```

| Risk | Benefit |
|:----:|:---------|
| None | Prevents frequent lock interruptions |

---

### 5.2 Hide Activities Button
```bash
gsettings set org.gnome.shell.extensions.dash-to-dock show-show-apps-button false
```

| Risk | Benefit |
|:----:|:---------|
| None | Cleaner dock layout and visual focus |

---

### 5.3 Middle-Click Show Desktop
```bash
sudo apt install -y xdotool
xdotool key Super+d
```
Bind via Settings → Keyboard → Custom Shortcuts.  

| Risk | Benefit |
|:----:|:---------|
| None | Faster workspace visibility and navigation |

---

### 5.4 — Set Up and Manage PIN Login on Zorin OS (GDM)

This section explains how to enable a secure **PIN-based login and unlock** on Zorin OS (GNOME / GDM) using `libpam-pwdfile`.  
The PIN works alongside your regular password — you can use either at any time.

#### 🧱 1. Install Required Packages

```bash
sudo apt update
sudo apt install -y libpam-pwdfile whois
```
#### 🗂️ 2. Back Up the Original PAM Configuration

Always back up before editing PAM files.

```
sudo cp /etc/pam.d/gdm-password /etc/pam.d/gdm-password.bak
sudo chown root:root /etc/pam.d/gdm-password.bak
sudo chmod 600 /etc/pam.d/gdm-password.bak
```

#### 🧾 3. Create the PIN Storage File
```
sudo touch /etc/custompinfile
sudo chown root:root /etc/custompinfile
sudo chmod 600 /etc/custompinfile
```

#### 🔑 4. Add User + PIN Securely

Enter your PIN interactively so it isn’t exposed in shell history.

```
read -s -p "Enter PIN for current user: " PIN; echo
HASH=$(mkpasswd -m sha-512 "$PIN")
sudo bash -c "echo \"$(whoami):$HASH\" >> /etc/custompinfile"
sudo chown root:root /etc/custompinfile
sudo chmod 600 /etc/custompinfile
```

View it (optional) — only the hashed value is shown:

```
sudo cat /etc/custompinfile
```

#### ⚙️ 5. Link the PIN to GNOME Login (PAM Rule)

Add this line to the top of /etc/pam.d/gdm-password:

```
sudo sed -i '1i auth sufficient pam_pwdfile.so pwdfile=/etc/custompinfile' /etc/pam.d/gdm-password
```

Confirm:

```
sudo sed -n '1,10p' /etc/pam.d/gdm-password
```

You should see:

```
auth sufficient pam_pwdfile.so pwdfile=/etc/custompinfile
#%PAM-1.0
auth    requisite       pam_nologin.so
...
```

#### 🧪 6. Test the PIN

Lock the screen (Super + L) or log out.

Enter your PIN and press Enter.

If it fails once, log in with your normal password, then lock again and retry (PAM caches update after first login).

#### 🩺 7. Verify Security

```
ls -l /etc/custompinfile
```


Expected:

```
-rw------- 1 root root ...
```


If needed:

```
sudo chown root:root /etc/custompinfile
sudo chmod 600 /etc/custompinfile
```

#### 🧹 Disable or Remove PIN Login

You can temporarily disable or fully remove the feature.

##### A. Temporarily Disable PIN Login

Comment out the line in /etc/pam.d/gdm-password:

```
sudo sed -i 's|^auth sufficient pam_pwdfile.so pwdfile=/etc/custompinfile|#auth sufficient pam_pwdfile.so pwdfile=/etc/custompinfile|' /etc/pam.d/gdm-password
```

To re-enable:

```
sudo sed -i 's|^#auth sufficient pam_pwdfile.so pwdfile=/etc/custompinfile|auth sufficient pam_pwdfile.so pwdfile=/etc/custompinfile|' /etc/pam.d/gdm-password
```

##### B. Completely Remove PIN Login

Restore the original configuration and delete the PIN file:

```
sudo cp /etc/pam.d/gdm-password.bak /etc/pam.d/gdm-password
sudo chmod 644 /etc/pam.d/gdm-password
sudo rm -f /etc/custompinfile
```

#### 🧯 Recovery if Locked Out

Boot into Advanced → Recovery Mode → root shell.

Remount root as writable:

```
mount -o remount,rw /
```

Restore backup:

```
cp /etc/pam.d/gdm-password.bak /etc/pam.d/gdm-password
```

Reboot:

```
reboot
```

#### 🔄 Update PIN

```
read -s -p "Enter NEW PIN: " PIN; echo
HASH=$(mkpasswd -m sha-512 "$PIN")
sudo sed -i "/^$(whoami):/d" /etc/custompinfile
sudo bash -c "echo \"$(whoami):$HASH\" >> /etc/custompinfile"
```

#### ✅ Summary
Step	Purpose	Example Command
1	Install dependencies	sudo apt install libpam-pwdfile whois
2	Backup PAM file	sudo cp /etc/pam.d/gdm-password ...
3	Create PIN file	sudo touch /etc/custompinfile
4	Add PIN interactively	read -s -p "Enter PIN" ...
5	Insert PAM rule	sudo sed -i '1i auth sufficient pam_pwdfile.so pwdfile=/etc/custompinfile' ...
6	Test	Lock → Enter PIN
Disable	Comment rule	`sudo sed -i 's
Remove	Restore backup + delete file	see Section B

Note: This method uses standard PAM modules and doesn’t modify GDM’s graphical interface. It’s secure when /etc/custompinfile is root-owned and chmod 600. Always keep your password login active as fallback.
whois provides the mkpasswd command used to hash the PIN.

---

## 🌐 6. Connectivity & Peripherals — (Stable Wireless and Bluetooth)

### 6.1 Bluetooth Resume
```bash
sudo systemctl enable --now bluetooth.service
```

| Risk | Benefit |
|:----:|:---------|
| None | Ensures Bluetooth auto-starts after suspend |

---

### 6.2 Wi-Fi Powersave Fix
```bash
echo -e '[connection]\nwifi.powersave=2' | sudo tee /etc/NetworkManager/conf.d/wifi-powersave.conf
sudo systemctl restart NetworkManager
```

| Risk | Benefit |
|:----:|:---------|
| None | Prevents Wi-Fi dropouts and improves stability |

---

## 🔒 7. Security & Access — (Harden Without Bloat)

### 7.1 Firewall
```bash
sudo ufw enable
sudo ufw allow ssh
sudo ufw allow 445/tcp
sudo ufw status
```

| Risk | Benefit |
|:----:|:---------|
| Low | Protects network services with minimal overhead |

---

### 7.2 ClamAV
```bash
sudo apt install -y clamav clamtk
sudo freshclam
```

| Risk | Benefit |
|:----:|:---------|
| Low (CPU use during scans) | Detects common malware and infected USBs |

---

### 7.3 Rootkit Hunter
```bash
sudo apt install -y rkhunter
sudo rkhunter --update
sudo rkhunter --check
```

| Risk | Benefit |
|:----:|:---------|
| Low | Detects potential rootkits and hidden binaries |

---

### 7.4 Password Managers
Bitwarden (cloud) or KeePassXC (offline).  
```bash
flatpak install flathub org.keepassxc.KeePassXC
```

| Risk | Benefit |
|:----:|:---------|
| None | Secure credential storage and autofill |

---

## ⏱️ 8. Automation & Scheduling
```bash
systemctl list-timers --all
sudo apt autoremove --purge && sudo apt clean
tail -20 ~/.local/share/flatpak-auto.log
```

| Risk | Benefit |
|:----:|:---------|
| Very Low | Keeps system clean and maintenance automated |

---

## 🧬 9. Firmware & Boot Integrity
```bash
[ -d /sys/firmware/efi ] && echo "UEFI mode" || echo "Legacy BIOS mode"
mokutil --sb-state
sudo fwupdmgr get-updates
```

| Risk | Benefit |
|:----:|:---------|
| None | Confirms Secure Boot and firmware health |

---

## 🧹 10. Optional Reverts — (Return to Stock State)
```bash
dconf reset -f /
sudo systemctl unmask NetworkManager-wait-online.service
sudo systemctl enable --now man-db.timer smartmontools.service
```

| Risk | Benefit |
|:----:|:---------|
| Medium (resets all GNOME tweaks) | Full restoration of Zorin default behavior |

---

## 🧭 11. Summary & Philosophy

> Tune your system because you **understand** it — not because “a list said so.”  
> The best optimization is one you can confidently undo.

| Principle | Goal |
|:--|:--|
| Keep APT updates and UFW active | Maintain secure base |
| Automate Flatpak updates | Ensure app freshness |
| Lower swappiness | Boost responsiveness |
| Enable SSD TRIM | Preserve SSD longevity |
| Verify Secure Boot | Maintain integrity |

---
**Author:** [Sinisa Bozic](https://sbozich.github.io) · 2025  
*Linux Optimization & Security Manual — Zorin & Ubuntu-based Systems*



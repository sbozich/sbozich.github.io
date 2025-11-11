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

---

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

| Step | Purpose                             | Example command / action |
|------|-------------------------------------|---------------------------|
| 1    | Install dependencies                | `sudo apt install libpam-pwdfile whois` |
| 2    | Backup PAM file                     | `sudo cp /etc/pam.d/gdm-password /etc/pam.d/gdm-password.bak` |
| 3    | Create PIN file                     | `sudo touch /etc/custompinfile && sudo chmod 600 /etc/custompinfile` |
| 4    | Add PIN interactively               | `sudo sh -c 'mkpasswd -m sha-512 >> /etc/custompinfile'` *(you’ll be asked for the PIN)* |
| 5    | Insert PAM rule at top of file      | `sudo sed -i '1iauth sufficient pam_pwdfile.so pwdfile=/etc/custompinfile' /etc/pam.d/gdm-password` |
| 6    | Test                                | Lock the screen → enter PIN |
| —    | **Disable** (temporarily)           | `sudo sed -i 's/^auth sufficient pam_pwdfile.so pwdfile=\\/etc\\/custompinfile/# &/' /etc/pam.d/gdm-password` |
| —    | **Remove** (restore original state) | `sudo mv /etc/pam.d/gdm-password.bak /etc/pam.d/gdm-password && sudo rm /etc/custompinfile` |


Note: This method uses standard PAM modules and doesn’t modify GDM’s graphical interface. It’s secure when /etc/custompinfile is root-owned and chmod 600. Always keep your password login active as fallback.
whois provides the mkpasswd command used to hash the PIN.



---
### 5.5 Listing All Installed Programs (APT, Flatpak, AppImage & GNOME Extensions)

This utility script provides a **comprehensive overview** of your installed applications and GNOME extensions on Zorin OS or any Ubuntu-based Linux distribution.  

It detects:
- **APT** packages you manually installed (excluding system defaults)  
- **Flatpak** apps, both **user** and **system** scope  
- **AppImages** in common directories (e.g. `~/Applications`, `~/Downloads`)  
- **AppImageLauncher**-integrated desktop entries  
- **GNOME extensions**, including name, description, and source URL  

At the end of execution, the script **prints results directly to terminal** and **asks if you want to save** them to your home folder.  
If confirmed, it writes a timestamped `.txt` file and opens it automatically in your default text editor.

---

## 🧩 How to Use

1. Create the script file:
   ```bash
   nano ~/bin/list-installed.sh
   ```

2. Paste the full code below into the file.

3. Save with `Ctrl + O`, exit with `Ctrl + X`, and make it executable:
   ```bash
   chmod +x ~/bin/list-installed.sh
   ```

4. Run anytime with:
   ```bash
   list-installed.sh
   ```

---

## 📜 The Script

```bash
#!/bin/bash
# ===============================================================
#  List installed software on Zorin / Ubuntu-like systems
#  - APT (manual/user-installed)
#  - Flatpak (user + system)
#  - AppImages (common locations)
#  - AppImageLauncher desktop entries
#  - GNOME extensions (user + system) with metadata
#  Shows in terminal, then asks to save to $HOME
# ===============================================================

TMPFILE=$(mktemp)

teeout() {
  tee -a "$TMPFILE"
}

echo "===== USER-INSTALLED APT PACKAGES =====" | teeout
comm -23 \
  <(apt-mark showmanual | sort) \
  <(gzip -dc /var/log/installer/initial-status.gz 2>/dev/null | awk '/Package: / {print $2}' | sort) | teeout

echo | teeout
echo "===== FLATPAK (USER SCOPE) =====" | teeout
flatpak list --user --app --columns=application,version,origin 2>/dev/null | teeout || echo "No user-scope flatpaks or flatpak not installed." | teeout

echo | teeout
echo "===== FLATPAK (SYSTEM SCOPE) =====" | teeout
flatpak list --system --app --columns=application,version,origin 2>/dev/null | teeout || echo "No system-scope flatpaks." | teeout

echo | teeout
echo "===== APPIMAGE PROGRAMS (detected in common locations) =====" | teeout
SEARCH_DIRS=(
  "$HOME/Applications"
  "$HOME/.local/bin"
  "$HOME/Downloads"
  "$HOME/.local/share/applications"
)
found_any=false
for dir in "${SEARCH_DIRS[@]}"; do
  if [ -d "$dir" ]; then
    matches=$(find "$dir" -maxdepth 2 -type f -iname "*.AppImage" 2>/dev/null)
    if [ -n "$matches" ]; then
      echo "In: $dir" | teeout
      echo "$matches" | teeout
      echo | teeout
      found_any=true
    fi
  fi
done
if [ "$found_any" = false ]; then
  echo "No AppImages found in common locations." | teeout
fi

echo | teeout
echo "===== APPIMAGELAUNCHER-INTEGRATED ENTRIES (.desktop) =====" | teeout
launcher_entries=$(ls "$HOME/.local/share/applications/"*.desktop 2>/dev/null | grep -i appimage || true)
if [ -n "$launcher_entries" ]; then
  echo "$launcher_entries" | teeout
else
  echo "No AppImageLauncher desktop entries found." | teeout
fi

print_extensions_from_dir() {
  local DIR="$1"
  local LABEL="$2"

  echo | teeout
  echo "----- $LABEL -----" | teeout

  if [ ! -d "$DIR" ]; then
    echo "Directory not found: $DIR" | teeout
    return
  fi

  for extdir in "$DIR"/*; do
    [ -d "$extdir" ] || continue
    metadata="$extdir/metadata.json"
    if [ -f "$metadata" ]; then
      python3 - <<'PY' | teeout
import json, pathlib, os
md_path = pathlib.Path(os.environ.get('METADATA'))
try:
    data = json.loads(md_path.read_text(encoding="utf-8"))
except Exception:
    print(f"* {md_path.parent.name} (could not parse metadata.json)")
else:
    uuid = data.get("uuid", md_path.parent.name)
    name = data.get("name", "")
    desc = data.get("description", "")
    url = data.get("url", "")
    print(f"* {uuid}")
    if name:
        print(f"    name: {name}")
    if desc:
        print(f"    desc: {desc}")
    if url:
        print(f"    url:  {url}")
PY
    else
      echo "* $(basename "$extdir") (no metadata.json)" | teeout
    fi
  done
}

echo | teeout
echo "===== GNOME EXTENSIONS (USER, with metadata) =====" | teeout
print_extensions_from_dir "$HOME/.local/share/gnome-shell/extensions" "User extensions"

echo | teeout
echo "===== GNOME EXTENSIONS (SYSTEM, with metadata) =====" | teeout
print_extensions_from_dir "/usr/share/gnome-shell/extensions" "System extensions"

echo | teeout
echo "===== ENABLED GNOME EXTENSIONS =====" | teeout
if command -v gnome-extensions &>/dev/null; then
  gnome-extensions list --enabled 2>/dev/null | teeout || echo "No enabled extensions detected." | teeout
else
  echo "gnome-extensions command not found." | teeout
fi

echo | teeout
echo "===== SUMMARY =====" | teeout
echo "Run date: $(date)" | teeout

echo
read -rp "Save this report to your home folder? (y/n): " confirm
if [[ "$confirm" =~ ^[Yy]$ ]]; then
  OUTFILE="$HOME/installed_$(date +%Y-%m-%d_%H-%M).txt"
  mv "$TMPFILE" "$OUTFILE"
  echo "Saved to: $OUTFILE"
  if command -v xdg-open >/dev/null 2>&1; then
    xdg-open "$OUTFILE" >/dev/null 2>&1 &
  fi
else
  rm -f "$TMPFILE"
  echo "Report not saved."
fi
```

---

## 💡 Tips

- Run this after setting up your Linux system — it gives a complete inventory useful for backup or reinstallation.  
- You can version-control your `installed_*.txt` reports in GitHub for reference.
- Add an alias for quick access:
  ```bash
  alias listapps='~/bin/list-installed.sh'
  ```

---

### 🧾 Output Example

```
===== USER-INSTALLED APT PACKAGES =====
gimp
curl
htop

===== FLATPAK (USER SCOPE) =====
com.github.tchx84.Flatseal   1.10.0   flathub
org.videolan.VLC             3.0.21   flathub
...
```

---

### 🏁 Result

Running this script gives you:
- A live overview of all installed software  
- A clean, optional export file in your home directory  
- A reproducible record of your environment for migrations or audits

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



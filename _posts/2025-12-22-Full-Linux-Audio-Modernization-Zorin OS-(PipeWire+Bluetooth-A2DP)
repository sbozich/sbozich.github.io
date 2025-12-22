---
title: "Full Linux Audio Modernization on Zorin OS (PipeWire + Bluetooth A2DP)"
date: 2025-12-22
categories: [Linux, Audio, PipeWire, Zorin]
tags: [pipewire, zorin, bluetooth, a2dp, hdmi, wireplumber, linux audio]
---

Modern Ubuntu-based distributions (including **Zorin OS 17**) ship both **PipeWire** and **PulseAudio**, creating a hybrid audio stack.  
That stack may work acceptably until you introduce: **HDMI displays, Bluetooth devices, GNOME Wayland sessions, and profile switching.**  
The result can be instability and degraded audio.

This article documents a real-world modernization on Zorin OS where two major issues were resolved:

- HDMI audio distortion caused by sample-rate instability
- Bluetooth refusing A2DP connections, producing `org.bluez.Error.*` failures

Once corrected, the system delivered:

- **48 kHz stable HDMI**
- **PipeWire-only routing**
- **WirePlumber as session manager**
- **Bluetooth A2DP SBC-XQ high-fidelity**
- **Zero distortions, zero reboots**

---

## 1. Detect whether PulseAudio or PipeWire owns the server

You want the PulseAudio server shim **running on PipeWire**, not the PulseAudio daemon itself.

```
pactl info | grep "Server Name"
```

Expected:

```
Server Name: PulseAudio (on PipeWire 0.3.48)
```

If instead:

```
Server Name: pulseaudio
```

then the legacy daemon is interfering. Check processes:

```
ps aux | grep -i pulse
```

---

## 2. Disable and mask native PulseAudio

Prevent it from respawning:

```bash
systemctl --user --now disable pulseaudio.service pulseaudio.socket
systemctl --user --now mask pulseaudio.service pulseaudio.socket
```

Start PipeWire’s PulseAudio layer:

```bash
systemctl --user start pipewire-pulse.service
```

Re-check:

```
pactl info | grep "Server Name"
```

---

## 3. Replace pipewire-media-session with WirePlumber

Legacy configurations still ship **pipewire-media-session**, which is deprecated.

Check state:

```
systemctl --user status wireplumber
```

If inactive, enable it:

```bash
sudo apt install wireplumber
systemctl --user enable --now wireplumber
```

Disable the old session manager:

```
systemctl --user status pipewire-media-session.service
```

It should be masked.

---

## 4. Fix HDMI distortion by enforcing 48 kHz

HDMI devices expect **48 kHz audio**.  
Linux may default to 44.1 kHz, causing glitching, resampling artifacts, and unstable timing.

Create:

```
~/.config/pipewire/pipewire.conf.d/10-force-48k.conf
```

Contents:

```ini
context.properties = {
    default.clock.rate          = 48000
    default.clock.allowed-rates = [ 48000 ]
    default.clock.min-quantum   = 16
    default.clock.quantum       = 1024
}
```

Restart:

```bash
systemctl --user restart pipewire
systemctl --user restart wireplumber
```

Confirm:

```bash
pactl list sinks | grep -E 'Sample'
```

Expected:

```
s32le 2ch 48000Hz
```

This eliminated HDMI rattling and static when connecting a TV.

---

## 5. Identify Bluetooth failure modes

Typical Bluetooth problems include:

- Device pairs but never connects
- `org.bluez.Error.Failed br-connection-profile-unavailable`
- No A2DP profiles in PulseAudio
- Device shows only Serial Port
- Audio sink never appears
- HSP/HFP fallback breaks sound quality
- No pairing agent under Wayland

Example state:

```
bluetoothctl connect <MAC>
Failed to connect: org.bluez.Error.Failed br-connection-profile-unavailable
```

This means the application layer has **no A2DP backend**.

---

## 6. Install PipeWire Bluetooth support

Check packages:

```bash
apt list --installed | grep libspa
```

You must have:

```
libspa-0.2-bluetooth
libspa-0.2-modules
```

If missing:

```bash
sudo apt install libspa-0.2-bluetooth libspa-0.2-modules -y
```

Restart entire audio and Bluetooth stack:

```bash
systemctl --user restart wireplumber
systemctl --user restart pipewire
sudo systemctl restart bluetooth
```

---

## 7. Install Blueman for pairing on Wayland

Wayland GNOME often does not spawn a proper pairing agent.  
Blueman fills the gap.

```bash
sudo apt install blueman
```

Autostart:

```bash
mkdir -p ~/.config/autostart
cp /etc/xdg/autostart/blueman.desktop ~/.config/autostart/
```

Start once:

```bash
blueman-applet &
```

---

## 8. Pair with bluetoothctl when needed

```
bluetoothctl
agent on
default-agent
power on
pair XX:XX:XX:XX:XX:XX
trust XX:XX:XX:XX:XX:XX
connect XX:XX:XX:XX:XX:XX
```

Now profiles should appear.

---

## 9. Validate A2DP profiles

```
pactl list cards | grep -i bluez -A20
```

Expected:

```
a2dp-sink
a2dp-sink-sbc
a2dp-sink-sbc_xq
headset-head-unit
```

The highest‑quality SBC is:

```
a2dp-sink-sbc_xq
```

Set it using Blueman’s Audio Profile UI.

---

## 10. Validate the sink and codec

```
pactl list sinks | grep -E 'bluez|Sample'
```

Expected:

```
Sample Specification: s16le 2ch 48000Hz
api.bluez5.codec = "sbc_xq"
```

This confirms:

- 48 kHz
- A2DP mode
- high-bandwidth SBC-XQ

---

## 11. Validate PipeWire timing with pw-top

```
pw-top
```

Look for:

- `QUANT≈256` or `1024`
- `RATE=48000`
- `ERR=0`

Example:

```
bluez_output.<MAC>  QUANT=256  RATE=48000  ERR=0
```

That indicates perfect stability.

---

## 12. Final Result

After applying these steps:

- HDMI distortion vanished
- PulseAudio was removed from the signal path
- WirePlumber enforced stable sample clocks
- Bluetooth paired reliably
- High‑fidelity A2DP SBC‑XQ streamed flawlessly
- Zero reboots required
- Audio survived HDMI hot‑plugging

This is what Linux audio should be in 2025.

---

## 13. Looking ahead

- Zorin OS 18 (Ubuntu 24.04 base) ships newer PipeWire stacks (0.3.6x+)
- Most of these issues disappear automatically
- SBC‑XQ becomes default on many devices
- Some platforms begin exposing AAC and LDAC

---

## Conclusion

Linux audio modernization means:
- PipeWire in control
- WirePlumber policy active
- No PulseAudio daemon
- Forced 48 kHz for HDMI
- Proper Bluetooth SPA
- High‑quality A2DP

When configured this way, HDMI and Bluetooth become **zero‑maintenance subsystems**, fully Wayland‑compatible and stable.

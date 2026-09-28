# Raspberry Pi AI HAT+ — Installation Guide

This guide covers the **Raspberry Pi 5 AI HAT+ (26 TOPS)** with the **Hailo-8 NPU**.

## 1. Setup / Installation

### 1.1 Update Raspberry Pi OS

```bash
sudo apt update
sudo apt full-upgrade -y
```

### 1.2 Update Raspberry Pi Firmware

```bash
sudo rpi-eeprom-update -a
```

### 1.3 Reboot

```bash
sudo reboot
```

### 1.4 Install DKMS

```bash
sudo apt install dkms -y
```

### 1.5 Install AI HAT+ Software

```bash
sudo apt install hailo-all -y
```

### 1.6 Reboot

```bash
sudo reboot
```

> **Note:** The Raspberry Pi AI HAT+ uses the `hailo-all` software package. The AI HAT+ 2 uses a different package, `hailo-h10-all`. Do not confuse the two. citeturn0search6

---

## 2. Verification

### 2.1 Check PCIe Detection

```bash
lspci
```

Expected:

```text
Hailo Technologies Ltd. Hailo-8 AI Processor
```

### 2.2 Check Hailo Device

```bash
ls -l /dev/hailo*
```

Expected:

```text
/dev/hailo0
```

### 2.3 Scan Hailo Device

```bash
hailortcli scan
```

Expected:

```text
Hailo Devices:
[-] Device: 0001:01:00.0
```

### 2.4 Identify Hailo-8

```bash
hailortcli fw-control identify
```

Expected:

```text
Board Name: Hailo-8
Device Architecture: HAILO8
```

### 2.5 Check Hailo Driver

```bash
sudo dmesg | grep -Ei "hailo|pcie"
```

### 2.6 Check Installed Hailo Packages

```bash
dpkg -l | grep -i hailo
```

### 2.7 Check Installed Models

```bash
ls -lh /usr/share/hailo-models/
```

### 2.8 Check HailoRT Version

```bash
hailortcli --version
```

### 2.9 Test Hailo-8 NPU — 5 Seconds

```bash
hailortcli run /usr/share/hailo-models/yolov8s_h8.hef \
  --time-to-run 5 \
  --measure-latency \
  --measure-temp
```

### 2.10 Test Hailo-8 NPU — 60 Seconds

```bash
hailortcli run /usr/share/hailo-models/yolov8s_h8.hef \
  --time-to-run 60 \
  --measure-latency \
  --measure-temp
```

### 2.11 Check Raspberry Pi Throttling

```bash
vcgencmd get_throttled
```

Expected:

```text
throttled=0x0
```

### 2.12 Check Raspberry Pi Temperature

```bash
vcgencmd measure_temp
```

### 2.13 Monitor Hailo and PCIe Messages

```bash
sudo dmesg -wH
```

### 2.14 Enable Persistent Logs

```bash
sudo mkdir -p /var/log/journal
sudo systemctl restart systemd-journald
sudo journalctl --flush
```

### 2.15 Check Previous Boot After a Crash

```bash
sudo journalctl -b -1 -k | \
grep -Ei "hailo|pcie|aer|error|fatal|panic|watchdog|oom|thermal|voltage"
```

### 2.16 Final Verification

Run:

```bash
lspci

ls -l /dev/hailo*

hailortcli scan

hailortcli fw-control identify

hailortcli run /usr/share/hailo-models/yolov8s_h8.hef \
  --time-to-run 5 \
  --measure-latency \
  --measure-temp
```

---

## 3. Error / Fix

### Error 1 — Hailo-8 does not appear in `lspci`

Check:

```bash
lspci
```

Then inspect PCIe and Hailo kernel messages:

```bash
sudo dmesg | grep -Ei "hailo|pcie"
```

Also verify that the AI HAT+ ribbon cable is correctly connected and that the Raspberry Pi 5 was powered off while connecting the hardware.

---

### Error 2 — `/dev/hailo0` does not exist

Check:

```bash
ls -l /dev/hailo*
```

Then check the installed packages:

```bash
dpkg -l | grep -i hailo
```

Check the kernel messages:

```bash
sudo dmesg | grep -Ei "hailo|pcie"
```

If the driver/package installation was incomplete, reinstall the required packages:

```bash
sudo apt update
sudo apt install dkms hailo-all -y
sudo reboot
```

---

### Error 3 — `hailortcli scan` does not detect the device

Run:

```bash
lspci
ls -l /dev/hailo*
```

Then:

```bash
hailortcli scan
hailortcli fw-control identify
```

If the PCIe device is missing as well, investigate the hardware connection and PCIe messages first.

---

### Error 4 — `hailortcli fw-control identify` fails

Check the HailoRT installation:

```bash
hailortcli --version
dpkg -l | grep -i hailo
```

Then inspect:

```bash
sudo dmesg | grep -Ei "hailo|pcie|error"
```

The Hailo software components and device driver need compatible versions. Raspberry Pi notes that mismatched Hailo software/driver versions can cause problems when using models built with a specific Hailo toolchain version. citeturn0search6

---

### Error 5 — Raspberry Pi becomes unresponsive during an NPU test

After rebooting, check the previous boot:

```bash
sudo journalctl -b -1 -k | \
grep -Ei "hailo|pcie|aer|error|fatal|panic|watchdog|oom|thermal|voltage"
```

Also check:

```bash
vcgencmd get_throttled
vcgencmd measure_temp
```

For continuous kernel monitoring:

```bash
sudo dmesg -wH
```

---

### Error 6 — Throttling is reported

Run:

```bash
vcgencmd get_throttled
```

Expected:

```text
throttled=0x0
```

If the value is not `0x0`, investigate power, thermal conditions, and the Raspberry Pi cooling setup.

Raspberry Pi recommends using an Active Cooler with AI HAT+ for best performance. citeturn0search2

---

### Error 7 — Temperature is high

Check:

```bash
vcgencmd measure_temp
```

The AI HAT+ product brief specifies an ambient operating temperature range of **0°C to 50°C**. citeturn0search12

Make sure the Raspberry Pi has adequate cooling and airflow.

---

## Quick Diagnostic Command Set

If the AI HAT+ is not working, run these commands and inspect the output:

```bash
lspci

ls -l /dev/hailo*

hailortcli scan

hailortcli fw-control identify

hailortcli --version

dpkg -l | grep -i hailo

sudo dmesg | grep -Ei "hailo|pcie|aer|error"

vcgencmd get_throttled

vcgencmd measure_temp
```

For crash investigation:

```bash
sudo journalctl -b -1 -k | \
grep -Ei "hailo|pcie|aer|error|fatal|panic|watchdog|oom|thermal|voltage"
```

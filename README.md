# Raspberry Pi 5 AI HAT+ — Hailo-8

A setup and verification guide for the **Raspberry Pi 5 AI HAT+ (26 TOPS)** with the **Hailo-8 AI accelerator**.

The guide is organized into:

- **Setup / Installation** — install and configure the required software.
- **Verification** — confirm that PCIe, the Hailo device, drivers, models, and NPU execution are working.
- **Error / Fix** — diagnostic commands and common checks when the Hailo device or Raspberry Pi becomes unstable.

---

## 1. Setup / Installation

### 1.1 Update Raspberry Pi OS

Update the package lists and upgrade installed packages:

```bash
sudo apt update
sudo apt full-upgrade -y
```

### 1.2 Update Raspberry Pi Firmware

Update the Raspberry Pi EEPROM firmware:

```bash
sudo rpi-eeprom-update -a
```

### 1.3 Reboot

Reboot the Raspberry Pi to apply the updates:

```bash
sudo reboot
```

### 1.4 Install DKMS

Install DKMS for kernel module management:

```bash
sudo apt install dkms -y
```

### 1.5 Install AI HAT+ Software

Install the Hailo software package:

```bash
sudo apt install hailo-all -y
```

### 1.6 Reboot

Reboot after installing the Hailo software:

```bash
sudo reboot
```

---

## 2. Verification

### 2.1 Check PCIe Detection

Check whether the Raspberry Pi detects the Hailo-8 through PCIe:

```bash
lspci
```

Expected:

```text
Hailo Technologies Ltd. Hailo-8 AI Processor
```

### 2.2 Check Hailo Device

Check whether the Hailo device node exists:

```bash
ls -l /dev/hailo*
```

Expected:

```text
/dev/hailo0
```

### 2.3 Scan for Hailo Devices

```bash
hailortcli scan
```

Expected:

```text
Hailo Devices:
[-] Device: 0001:01:00.0
```

### 2.4 Identify Hailo-8

Check the Hailo board and device architecture:

```bash
hailortcli fw-control identify
```

Expected:

```text
Board Name: Hailo-8
Device Architecture: HAILO8
```

### 2.5 Check Hailo Driver Messages

```bash
sudo dmesg | grep -Ei "hailo|pcie"
```

### 2.6 Check Installed Hailo Packages

```bash
dpkg -l | grep -i hailo
```

### 2.7 Check Installed Hailo Models

```bash
ls -lh /usr/share/hailo-models/
```

### 2.8 Check HailoRT Version

```bash
hailortcli --version
```

### 2.9 Test the Hailo-8 NPU

Run the YOLOv8s Hailo model for 5 seconds:

```bash
hailortcli run /usr/share/hailo-models/yolov8s_h8.hef \
  --time-to-run 5 \
  --measure-latency \
  --measure-temp
```

### 2.10 Run a 60-Second NPU Test

For a longer stability test:

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

---

## 3. Error / Fix

Use this section when the Hailo device is not detected, the driver reports errors, or the Raspberry Pi becomes unstable during NPU testing.

### 3.1 Monitor Hailo and PCIe Messages

Run the following command while reproducing the problem:

```bash
sudo dmesg -wH
```

Look for messages containing:

- `hailo`
- `pcie`
- `aer`
- `error`
- `fatal`

### 3.2 Enable Persistent System Logs

Create the persistent journal directory and flush the journal:

```bash
sudo mkdir -p /var/log/journal
sudo systemctl restart systemd-journald
sudo journalctl --flush
```

### 3.3 Check the Previous Boot After a Crash

If the Raspberry Pi rebooted or became unresponsive after an NPU test, inspect the previous kernel log:

```bash
sudo journalctl -b -1 -k | \
grep -Ei "hailo|pcie|aer|error|fatal|panic|watchdog|oom|thermal|voltage"
```

This can help identify Hailo, PCIe, kernel, thermal, voltage, OOM, or watchdog-related messages.

### 3.4 Hailo Device Not Detected

If `lspci` does not show the Hailo-8 device, check:

```bash
lspci
sudo dmesg | grep -Ei "hailo|pcie"
ls -l /dev/hailo*
```

Then verify that the AI HAT+ software is installed:

```bash
dpkg -l | grep -i hailo
```

If required, reinstall the Hailo package:

```bash
sudo apt update
sudo apt install hailo-all -y
sudo reboot
```

### 3.5 HailoRT or Model Problems

Check the installed HailoRT version and available models:

```bash
hailortcli --version
ls -lh /usr/share/hailo-models/
```

Confirm that the model used by the test exists:

```bash
ls -lh /usr/share/hailo-models/yolov8s_h8.hef
```

### 3.6 Raspberry Pi Becomes Unstable During NPU Test

If the Raspberry Pi becomes unstable or reboots while running the Hailo test, check:

```bash
vcgencmd get_throttled
vcgencmd measure_temp
```

Then inspect the previous boot:

```bash
sudo journalctl -b -1 -k | \
grep -Ei "hailo|pcie|aer|error|fatal|panic|watchdog|oom|thermal|voltage"
```

---

## 4. Final Verification

Run the following commands after completing the installation:

```bash
lspci
```

```bash
ls -l /dev/hailo*
```

```bash
hailortcli scan
```

```bash
hailortcli fw-control identify
```

```bash
hailortcli --version
```

Finally, run the NPU test:

```bash
hailortcli run /usr/share/hailo-models/yolov8s_h8.hef \
  --time-to-run 5 \
  --measure-latency \
  --measure-temp
```

If these checks succeed, the Raspberry Pi 5 should be able to detect the **Hailo-8 AI accelerator** and execute the supplied Hailo model.

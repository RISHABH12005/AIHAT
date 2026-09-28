# Raspberry Pi 5 AI HAT+ — Hailo-8 Setup

This guide documents the Raspberry Pi 5 AI HAT+ setup and verification steps.

## 1. Update Raspberry Pi OS

```bash
sudo apt update
sudo apt full-upgrade -y
```

## 2. Update Raspberry Pi Firmware

```bash
sudo rpi-eeprom-update -a
```

## 3. Reboot

```bash
sudo reboot
```

## 4. Install DKMS

```bash
sudo apt install dkms -y
```

## 5. Install AI HAT+ Software

```bash
sudo apt install hailo-all -y
```

## 6. Reboot

```bash
sudo reboot
```

## 7. Check PCIe Detection

```bash
lspci
```

Expected:

```text
Hailo Technologies Ltd. Hailo-8 AI Processor
```

## 8. Check Hailo Device

```bash
ls -l /dev/hailo*
```

Expected:

```text
/dev/hailo0
```

## 9. Scan Hailo Device

```bash
hailortcli scan
```

Expected:

```text
Hailo Devices:
[-] Device: 0001:01:00.0
```

## 10. Identify Hailo-8

```bash
hailortcli fw-control identify
```

Expected:

```text
Board Name: Hailo-8
Device Architecture: HAILO8
```

## 11. Check Hailo Driver

```bash
sudo dmesg | grep -Ei "hailo|pcie"
```

## 12. Check Installed Hailo Packages

```bash
dpkg -l | grep -i hailo
```

## 13. Check Installed Models

```bash
ls -lh /usr/share/hailo-models/
```

## 14. Test Hailo-8 NPU

```bash
hailortcli run /usr/share/hailo-models/yolov8s_h8.hef \
  --time-to-run 5 \
  --measure-latency \
  --measure-temp
```

## 15. 60-Second NPU Test

```bash
hailortcli run /usr/share/hailo-models/yolov8s_h8.hef \
  --time-to-run 60 \
  --measure-latency \
  --measure-temp
```

## 16. Check Raspberry Pi Throttling

```bash
vcgencmd get_throttled
```

Expected:

```text
throttled=0x0
```

## 17. Check Raspberry Pi Temperature

```bash
vcgencmd measure_temp
```

## 18. Monitor Hailo and PCIe Messages

```bash
sudo dmesg -wH
```

## 19. Enable Persistent Logs

```bash
sudo mkdir -p /var/log/journal
sudo systemctl restart systemd-journald
sudo journalctl --flush
```

## 20. Check Previous Boot After Crash

```bash
sudo journalctl -b -1 -k | \
grep -Ei "hailo|pcie|aer|error|fatal|panic|watchdog|oom|thermal|voltage"
```

## 21. Check HailoRT Version

```bash
hailortcli --version
```

## 22. Final Verification

Run the following commands:

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

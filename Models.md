# Hailo Models

## 1. List All Models

List all installed HEF models:

```bash
find /usr/share/hailo-models/ -type f -name "*.hef" -printf "%f\n" | sort
```

## 2. Find All / specific Model

### Find all HEF models

```bash
find /usr/share/hailo-models/ -type f -name "*.hef"
```

### Find a specific model

Example: YOLOv8

```bash
find /usr/share/hailo-models/ -type f -iname "*yolov8*"
```

## 3. Run specific Model

Replace `MODEL.hef` with the model you want to run:

```bash
hailortcli run /usr/share/hailo-models/MODEL.hef
```

## 4. Test specific Model

Test a model for 5 seconds:

```bash
hailortcli run /usr/share/hailo-models/MODEL.hef --time-to-run 5
```

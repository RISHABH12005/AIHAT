# Raspberry Pi AI HAT+ (26-TOPS)

The **Raspberry Pi AI HAT+ (26 TOPS)** is an AI accelerator add-on for the Raspberry Pi 5, built around the **Hailo-8 NPU**. It provides hardware-accelerated neural-network inference for edge-AI workloads. Raspberry Pi documents the 26 TOPS variant as the Hailo-8 version of AI HAT+. citeturn0search0turn0search12

## 1. Define

The Raspberry Pi AI HAT+ 26-TOPS is an add-on board designed for **Raspberry Pi 5**. It integrates a **Hailo-8 neural processing unit (NPU)** and communicates with the Raspberry Pi 5 through its PCIe interface.

The NPU is designed to accelerate supported neural-network inference workloads locally on the Raspberry Pi, reducing the amount of AI computation that needs to be performed by the CPU. Raspberry Pi lists applications such as object detection, camera post-processing, robotics, security, and other edge-AI workloads. citeturn0search0turn0search2

## 2. Specifications

| Specification | Details |
|---|---|
| Product | Raspberry Pi AI HAT+ |
| Variant | 26 TOPS |
| AI Accelerator | Hailo-8 NPU |
| Inference Performance | 26 TOPS |
| Precision | INT8 |
| Host Platform | Raspberry Pi 5 |
| Interface | PCIe |
| AI Workloads | Neural-network inference |
| Applications | Object detection, camera processing, robotics, security |
| Dimensions | Approximately 66 mm × 56.5 mm |
| Operating Temperature | 0°C to 50°C ambient |

The 26 TOPS AI HAT+ uses the Hailo-8 accelerator, while the 13 TOPS variant uses Hailo-8L. citeturn0search0turn0search12

## 3. Use Case

The Raspberry Pi AI HAT+ can be used for:

- Real-time object detection
- Image and video processing
- Camera-based computer vision
- Robotics
- Security and surveillance systems
- Edge AI inference
- Multiple neural-network workloads
- Embedded AI applications

The 26 TOPS variant is intended for larger neural networks, higher inference throughput, and more effective simultaneous execution of multiple networks compared with the 13 TOPS variant. citeturn0search0turn0search1

## 4. Installation

The complete setup, installation, verification, and troubleshooting instructions are available in:

**[INSTALL.md](INSTALL.md)**

The installation guide contains:

1. **Setup / Installation**
2. **Verification**
3. **Error / Fix**

### Quick Start

For the full procedure, see **[INSTALL.md](INSTALL.md)**.

The main software packages used for the AI HAT+ include **DKMS** and **hailo-all**. citeturn0search6

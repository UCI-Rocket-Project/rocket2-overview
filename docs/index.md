# 🚀 **Rocket 2 Avionics System**

## Overview
> **WHATSUP ROCKET NATION.** ITS YA BOI, PASTOR ROBERT. WELCOME TO AVIONICS.

This is the central hub for the **Avionics team at UCI Rocket Project (Liquids)**. We specialize in designing and manufacturing custom PCBAs and Software Suites for our liquid methalox rocket.

All designed hardware is made using **KiCad** as the ECAD software, and Firmware is made with **STM32CubeMX**.

### Current Rocket: 🚀🚀 MOCH4 🚀🚀  

---

## 🧩 **System Diagram**
!!! info "Status: Work in Progress"
    We are currently updating the system-level block diagram. Check back soon for the full hardware interconnect map.

---

## 🛠️ **Subsystems**

| Subsystem | Hardware Repository | Firmware Repository |
| :--- | :--- | :--- |
| 🔥**Engine Control Unit (ECU v2.0)**🔥 | [Hardware](https://github.com/UCI-Rocket-Project/rocket2-ecu-hardware) | [Firmware](https://github.com/UCI-Rocket-Project/rocket2-ecu-firmware) |
| 🌍**Ground Support Electronics (GSE v2.1)**🌍 | [Hardware](https://github.com/UCI-Rocket-Project/rocket2-gse-hardware) | [Firmware](https://github.com/UCI-Rocket-Project/rocket2-gse-firmware) |
| 🛰️**Telemetry Radio System (TRS)**🛰️ | [Hardware](https://github.com/UCI-Rocket-Project/rocket2-trs-hardware) | [Firmware](https://github.com/UCI-Rocket-Project/rocket2-trs-firmware) |

### 📊 Ground Control & GUI
* **Primary GUI (VIVIIan):** [UCI-Rocket-Project/VIVIIan](https://github.com/UCI-Rocket-Project/VIVIIan)
* **Legacy System:** [rocket2-webservice-gui (Deprecated)](https://github.com/UCI-Rocket-Project/rocket2-webservice-gui)

---

## 📖 Technical Documentation
Detailed specifications for networking and hardware standards:

* [**Data Packets & Networking**](data_packets.md): Protocol definitions and telemetry structures.
* [**Standardized Connectors**](standardized_connectors.md): Pinout mappings and connector selection.

---

## 📝 Standards & Onboarding
* [**Hardware ECAD Setup & Styling Guide**](hardware_setup_&_guidelines.md)
* [**Software Convention & Styling**](software_guidelines.md)
* [**Firmware VS Code Extension Guide**](stm32_vscode_guide.md.md)
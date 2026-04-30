# 🚀 **Rocket 2 Avionics System**

## Overview
> **WHATSUP ROCKET NATION.** ITS YA BOI, PASTOR ROBERT. WELCOME TO AVIONICS.

This is the central hub for the **Avionics team at UCI Rocket Project (Liquids)**. We specialize in designing and manufacturing custom PCBAs and Software Suites for our liquid methalox rocket.

All designed hardware is made using **KiCad** as the ECAD software, and Firmware is made with **STM32CubeMX**.

&nbsp;

#### **Current Rocket:** 🚀🚀 MOCH4 🚀🚀  

&nbsp;

### **AV team 25-26**
![Team Photo](/media/AV_Team1.jpg){: style="width:800px; display:block; margin: 0 auto;"}

![Team Photo](/media/AV_Team2.jpg){: style="width:800px; display:block; margin: 0 auto;"}

![Team Photo](/media/AV_Team3.jpg){: style="width:800px; display:block; margin: 0 auto;"}


## 🧩 **System Diagram**
<iframe style="border: 1px solid rgba(0, 0, 0, 0.1);" width="1000" height="700" src="https://embed.figma.com/board/hxCFRs9DTTg6R1RPPCGrtq/AV-System-Diagram?node-id=0-1&embed-host=share" allowfullscreen></iframe>
---

## 🛠️ **Subsystems**

&nbsp;

### 🖥️ **Custom PCBs we make**
| Subsystem | Hardware Repository | Firmware Repository |
| :--- | :--- | :--- |
| 🔥 **Engine Control Unit (ECU v2.0)** 🔥 | [Hardware](https://github.com/UCI-Rocket-Project/rocket2-ecu-hardware) | [Firmware](https://github.com/UCI-Rocket-Project/rocket2-ecu-firmware) |
| 🌍 **Ground Support Electronics (GSE v2.1)** 🌍 | [Hardware](https://github.com/UCI-Rocket-Project/rocket2-gse2.0-hardware) | [Firmware](https://github.com/UCI-Rocket-Project/rocket2-gse-firmware) |
| 🛰️ **Telemetry Radio System (TRS)** 🛰️ | [Hardware](https://github.com/UCI-Rocket-Project/rocket2-trs-hardware) | [Firmware](https://github.com/UCI-Rocket-Project/rocket2-trs-firmware) |
| 🔌 **Universal Adapter Board (UAB)** 🔌 | [Hardware](https://github.com/UCI-Rocket-Project/rocket2-universal-adapter-board) | N/A |

&nbsp;

#### **COTS Flight computers for parachute deployment**
| Flight Computer | Role |
| :--- | :--- |
| [**Silicdyne Fluctus**](https://silicdyne.net/shop/fluctus-bundle/) | Primary Flight Computer |
| [**Altus Metrum EasyMini**](https://altusmetrum.org/EasyMini/) | Secondary/Backup Flight Computer |

&nbsp;

### 📊 Ground Control & GUI
* **Primary GUI (VIVIIan):** [**UCI-Rocket-Project/VIVIIan**](https://github.com/UCI-Rocket-Project/VIVIIan)
* **Legacy System:** [**rocket2-webservice-gui (Deprecated)**](https://github.com/UCI-Rocket-Project/rocket2-webservice-gui)

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
# Rocket 2 Avionics System

## Overview
![SystemArchitecture](/media/system_architecture.svg)


## Subsystems

### Engine Control Unit (ECU v2.0)
Hardware: https://github.com/UCI-Rocket-Project/rocket2-ecu-hardware \
Firmware: https://github.com/UCI-Rocket-Project/rocket2-ecu-firmware

### Ground Support Electronics (GSE v2.1)
Hardware: https://github.com/UCI-Rocket-Project/rocket2-gse-hardware \
Firmware: https://github.com/UCI-Rocket-Project/rocket2-gse-firmware

### Telemetry Radio System (TRS)
Hardware: https://github.com/UCI-Rocket-Project/rocket2-trs-hardware \
Firmware: https://github.com/UCI-Rocket-Project/rocket2-trs-firmware

### Ground Web Service & Graphical User Interface
New GUI/Backend: https://github.com/UCI-Rocket-Project/VIVIIan \
Deprecated GUI/Backend: https://github.com/UCI-Rocket-Project/rocket2-webservice-gui


## Connectors

### GSE
| Key | Short Key | Connector | Description |
| --- | --- | --- | --- |
| solenoidGn2Fill | GC-GN2-F | GX-16 | Nitrogen vehicle fill, normally closed |
| solenoidGn2Vent | GC-GN2-V | GX-16 | Nitrogen GSE panel vent, normally closed |
| solenoidGn2Disconnect | GC-GN2-Q | GX-16 | Actuate nitrogen quick disconnect |
| solenoidMvasFill | GC-MVS-F | GX-16 | MVAS line fill, normally closed |
| solenoidMvasVent | GC-MVS-V | GX-16 | MVAS line vent, normally closed |
| solenoidMvasOpen | GC-MVS-O | GX-16 | Open MVAS, normally closed |
| solenoidMvasClose | GC-MVS-C | GX-16 | Close MVAS, normally closed |
| solenoidLoxVent | GC-LOX-V | GX-16 | Liquid oxygen GSE panel vent, normally open |
| solenoidLngVent | GC-LNG-V | GX-16 | Liquid methane GSE panel vent, normally open |
| pressureGn2 | GS-GN2 | GX-12 | Nitrogen bottle pressure, max range 5000 psi |
| pressureChamber | GS-GN2 | GX-12 | Combustion champer pressure, max range 1000 psi |

### ECU
| Key | Short Key | Connector | Description |
| --- | --- | --- | --- |
| solenoidCopvVent | EC-CPV-V | GX-16 | COPV vent, normally closed |
| solenoidPv1 | EC-PV1 | GX-16 | DLPR Dome fill, normally closed |
| solenoidPv2 | EC-PV2 | GX-16 | DLPR Dome vent, normally open |
| solenoidVent | EC-VNT | GX-16 | LOX & LNG vent, normally closed |
| pressureCopv | ES-CPV | GX-12 | COPV pressure, max range 5000 psi |
| pressureLox | ES-LOX-T | GX-12 | Liquid oxygen tank pressure, max range 1000 psi |
| pressureLng | ES-LNG-T | GX-12 | Liquid methane tank pressure, max range 1000 psi |
| pressureInjectorLox | ES-LOX-I | GX-12 | Injector liquid oxygen pressure, max range 1000 psi |
| pressureInjectorLng | ES-LNG-I | GX-12 | Injector methane pressure, max range 1000 psi |
> Note: solenoidVent is normally closed, while the vents for the tanks itself is normally open.



## Hardware Contributing Guidelines
1. Use `main` instead of `master`.
2. Use [KiCAD 8.02](https://www.kicad.org/).
3. Use provided [.gitignore](/hardware/.gitignore). Do not include fabrication files (Gerber, BOM, etc).
4. Use KiCAD standard component and footprint library for simple components (passives, transistors, standard footprints, etc). Use [UCIRP-KiCAD-Lib](https://github.com/UCI-Rocket-Project/UCIRP-KiCAD-Lib) as Git submodule for complex and non-standard components. **Do not import and use any other online library such as Adafruit, create new symbols and footprints if required and contribute to the shared library.**
5. Make changes directly on the `main` branch. Ensure local files are up to date before making changes. Merge conflicts are practically irreconcilable, do not attempt to merge.
6. Hierarchical sheets may be used for repeated schematics.


## Software Contributing Guidelines
1. Use `main` instead of `master`.
2. Use [pre-commit](https://pre-commit.com/). Copy provided [.pre-commit-config.yaml](/software/.pre-commit-config.yaml) to project repository root and install.
    - **Python** files are formatted with [Black](https://github.com/psf/black). Uncomment relevant section in `.pre-commit-config.yaml`.
    - **C/C++** files are formatted with [clang-format](https://clang.llvm.org/docs/ClangFormat.html). Copy provided [.clang-format](/software/.clang-format) to project repository root and uncomment relevant section in `.pre-commit-config.yaml`.
    - **Javascript, Typescript, and web-related** files are formatted with [Prettier](https://prettier.io/). Copy provided [.prettierrc](/software/.prettierrc) to repository root and uncomment relevant section in `.pre-commit-config.yaml`.
3. Each software module (each repository) shall have a singular command or script that enables the whole module.
4. Naming conventions are as follows:
    - Files: `snake_case.c`
    - Variables: `camelCase`
    - Constant variables: `UPPER_SNAKE_CASE`
    - Functions: `PascalCase`
    - Types: `PascalCase`
    - Private variables within classes: `_camelCaseWithUnderscorePrefix`


## Embedded Firmware Contributing Guidelines
1. Follow all items in Software Contributing Guidelines.
2. Use [PlatformIO](https://platformio.org/).


## Ground Network
All ground communication will be over TCP/IP Ethernet and use the standardized packet format sent via raw bytes. All packets are packed little-endian.\
Checksums are CRC-32 (polynomial `0x04C11DB7`). See files for CRC implementation in C++, this CRC is consistent with Python `binascii.crc32()`. This additional layer of checksum is added to ensure data integrity over embedded serial busses.\
Subnet `10.0.0.1/16`. DHCP Range `10.0.255.0/24`. All other IPs reserved.

### GSE
IP: `10.0.2.0`.\
Open ports `10001-10002` for commands and data. See below for packet format.

### ECU
IP: `10.0.2.1`.\
Open ports `10001-10002` for commands and data. See below for packet format.

### Notes on Packing and Transmission
- Data types are organized little-endian in memory.
- Packets are sent as raw bytes, least significant *byte* first, most significant *bit* first (unless superseded by protocol convention).

A sample transmission sequence is as follows:
```c
void SendData(DataType *data) {
    for (int i = 0; i < sizeof(DataType); i++) {
        SendByte(*((uint8_t *)data + i));
    }
}

void SendByte(uint8_t byte) {
    for (int i = 7; i >= 0; i--) {
        SendBit((byte & (1 << i)) >> i);
    }
}
```

A sample struct like so,
```c
struct DataType {
    uint8_t a = 0xAA;
    uint16_t b = 0x00BB;
};
```
would be sent as follows (leftmost bit first, spaces added for readability): `1010 1010 1011 1011 0000 0000`

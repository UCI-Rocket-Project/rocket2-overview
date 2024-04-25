# Rocket 2 Avionics System

## Overview
![SystemArchitecture](/media/system_architecture.svg)


## Subsystems
### Avionics Flight Sensor (AFS)
Hardware: https://github.com/UCI-Rocket-Project/rocket2-afs-hardware \
Firmware: https://github.com/UCI-Rocket-Project/rocket2-afs-firmware

### Engine Control Unit (ECU)
Hardware: https://github.com/UCI-Rocket-Project/rocket2-ecu-hardware \
Firmware: https://github.com/UCI-Rocket-Project/rocket2-ecu-firmware

### Ground Support Electronics (GSE)
Hardware: https://github.com/UCI-Rocket-Project/rocket2-gse-hardware \
Firmware: https://github.com/UCI-Rocket-Project/rocket2-gse-firmware

### Telemetry Radio System (TRS)
Hardware: https://github.com/UCI-Rocket-Project/rocket2-trs-hardware \
Firmware: https://github.com/UCI-Rocket-Project/rocket2-trs-firmware

### Ground Web Service & Graphical User Interface
https://github.com/UCI-Rocket-Project/rocket2-webservice-gui

### Database
https://github.com/UCI-Rocket-Project/rocket2-database

### Flight Tracker
https://github.com/UCI-Rocket-Project/rocket-tracker

### Backup Command Line Interface
https://github.com/UCI-Rocket-Project/rocket2-cli


## Connectors

### GSE
| Key | Short Key | Connector | Description |
| --- | --- | --- | --- |
| solenoidGn2Fill | GC-GN2-F | GX-16 | Nitrogen vehicle fill, normally closed |
| solenoidGn2Vent | GC-GN2-V | GX-16 | Nitrogen GSE panel vent, normally closed |
| solenoidMvasFill | GC-MVS-F | GX-16 | MVAS line fill, normally closed |
| solenoidMvasVent | GC-MVS-V | GX-16 | MVAS line vent, normally closed |
| solenoidMvas | GC-MVS-A | GX-16 | Actuate MVAS, normally closed |
| solenoidLoxFill | GC-LOX-F | GX-16 | Liquid oxygen fill, normally closed |
| solenoidLoxVent | GC-LOX-V | GX-16 | Liquid oxygen GSE panel vent, normally open |
| solenoidLngFill | GC-LNG-F | GX-16 | Liquid methane fill, normally closed |
| solenoidLngVent | GC-LNG-V | GX-16 | Liquid methane GSE panel vent, normally open |
| pressureGn2 | GS-GN2 | GX-12 | Nitrogen bottle pressure, max range 5000 psi |

### ECU
| Key | Short Key | Connector | Description |
| --- | --- | --- | --- |
| solenoidCopvVent | EC-CPV-V | GX-16 | COPV vent, normally closed |
| solenoidPv1 | EC-PV1 | GX-16 | DLPR Dome fill, normally closed |
| solenoidPv2 | EC-PV2 | GX-16 | DLPR Dome vent, normally open |
| solenoidVent | EC-VNT | GX-16 | LOX & LNG vent, normally open |
| pressureCopv | ES-CPV | GX-12 | COPV pressure, max range 5000 psi |
| pressureLox | ES-LOX-T | GX-12 | Liquid oxygen tank pressure, max range 1000 psi |
| pressureLng | ES-LNG-T | GX-12 | Liquid methane tank pressure, max range 1000 psi |
| pressureInjectorLox | ES-LOX-I | GX-12 | Injector liquid oxygen pressure, max range 1000 psi |
| pressureInjectorLng | ES-LNG-I | GX-12 | Injector methane pressure, max range 1000 psi |




## Hardware Contributing Guidelines
1. Use `main` instead of `master`.
2. Use [KiCAD 7](https://www.kicad.org/).
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

### GSE Command Packet
```c
struct gseCommand {
    bool igniter0Fire;
    bool igniter1Fire;
    bool alarm;
    bool solenoidStateGn2Fill;
    bool solenoidStateGn2Vent;
    bool solenoidStateMvasFill;
    bool solenoidStateMvasVent;
    bool solenoidStateMvas;
    bool solenoidStateLoxFill;
    bool solenoidStateLoxVent;
    bool solenoidStateLngFill;
    bool solenoidStateLngVent;
    uint32_t crc;
};
```
| Key | Data Type | Description |
| --- | --- | --- |
| igniter0Fire | `bool` | Fire igniter 0 |
| igniter1Fire | `bool` | Fire igniter 1 |
| alarm | `bool` | Sound alarm |
| solenoidStateGn2Fill | `bool` | Nitrogen vehicle fill, normally closed |
| solenoidStateGn2Vent | `bool` |  Nitrogen GSE panel vent, normally closed |
| solenoidStateMvasFill | `bool` | MVAS line fill, normally closed |
| solenoidStateMvasVent | `bool` | MVAS line vent, normally closed |
| solenoidStateMvas | `bool` | Actuate MVAS, normally closed |
| solenoidStateLoxFill | `bool` | Liquid oxygen fill, normally closed |
| solenoidStateLoxVent | `bool` | Liquid oxygen GSE panel vent, normally open |
| solenoidStateLngFill | `bool` | Liquid methane fill, normally closed |
| solenoidStateLngVent | `bool` | Liquid methane GSE panel vent, normally open |
> All fields shall be filled with valid command. For solenoids, a value of `1` or `true` **always** corresponds to an open or flowing valve. A value of `0` or `false` **always** corresponds to a closed or blocking valve, regardless of solenoid type. The translation is handled on the device firmware.

### GSE Data Packet
```c
struct gseData {
    uint32_t timestamp;
    bool igniterArmed;
    bool igniter0Continuity;
    bool igniter1Continuity;
    bool igniterInternalState0;
    bool igniterInternalState1;
    bool alarmInternalState;
    bool solenoidInternalStateGn2Fill;
    bool solenoidInternalStateGn2Vent;
    bool solenoidInternalStateMvasFill;
    bool solenoidInternalStateMvasVent;
    bool solenoidInternalStateMvas;
    bool solenoidInternalStateLoxFill;
    bool solenoidInternalStateLoxVent;
    bool solenoidInternalStateLngFill;
    bool solenoidInternalStateLngVent;
    float supplyVoltage0 = std::nanf("");
    float supplyVoltage1 = std::nanf("");
    float solenoidCurrentGn2Fill = std::nanf("");
    float solenoidCurrentGn2Vent = std::nanf("");
    float solenoidCurrentMvasFill = std::nanf("");
    float solenoidCurrentMvasVent = std::nanf("");
    float solenoidCurrentMvas = std::nanf("");
    float solenoidCurrentLoxFill = std::nanf("");
    float solenoidCurrentLoxVent = std::nanf("");
    float solenoidCurrentLngFill = std::nanf("");
    float solenoidCurrentLngVent = std::nanf("");
    float temperatureLox = std::nanf("");
    float temperatureLng = std::nanf("");
    float pressureGn2 = std::nanf("");
    uint32_t crc;
};
```
| Key | Data Type | Units | Description |
| --- | --- | --- | --- |
| timestamp | `unsigned long` | $ms$ | Milliseconds since Unix Epoch |
| igniterArmed | `bool` | | Igniter arming key state, `1` for armed |
| igniter0Continuity | `bool` | | Igniter 0 continuity, `1` for continuity detected |
| igniter1Continuity | `bool` | | Igniter 1 continuity, `1` for continuity detected |
| igniterInternalState0 | `bool` | | Igniter 0 fire state feedback |
| igniterInternalState1 | `bool` | | Igniter 1 fire state feedback |
| alarmInternalState1 | `bool` | | Alarm state feedback |
| solenoidInternalStateGn2Fill | `bool` | | Nitrogen vehicle fill solenoid state feedback |
| solenoidInternalStateGn2Vent | `bool` | | Nitrogen GSE panel vent solenoid state feedback |
| solenoidInternalStateMvasFill | `bool` | | MVAS line fill solenoid state feedback |
| solenoidInternalStateMvasVent | `bool` | | MVAS line vent solenoid state feedback |
| solenoidInternalStateMvas | `bool` | | MVAS actuation solenoid state feedback |
| solenoidInternalStateLoxFill | `bool` | | Liquid oxygen fill solenoid state feedback |
| solenoidInternalStateLoxVent | `bool` | | Liquid oxygen GSE panel vent solenoid state feedback |
| solenoidInternalStateLngFill | `bool` | | Liquid methane fill solenoid state feedback |
| solenoidInternalStateLngVent | `bool` | | Liquid methane GSE panel vent solenoid state feedback |
| supplyVoltage0 | `float` | $V$ | Power supply 0 voltage |
| supplyVoltage1 | `float` | $V$ | Power supply 1 voltage |
| solenoidCurrentGn2Fill | `float` | $A$ | Nitrogen vehicle fill solenoid current feedback |
| solenoidCurrentGn2Vent | `float` | $A$ | Nitrogen GSE panel vent solenoid current feedback |
| solenoidCurrentMvasFill | `float` | $A$ | MVAS line fill solenoid current feedback |
| solenoidCurrentMvasVent | `float` | $A$ | MVAS line vent solenoid current feedback |
| solenoidCurrentMvas | `float` | $A$ | MVAS actuation solenoid current feedback |
| solenoidCurrentLoxFill | `float` | $A$ | Liquid oxygen fill solenoid current feedback |
| solenoidCurrentLoxVent | `float` | $A$ | Liquid oxygen GSE panel vent solenoid current feedback |
| solenoidCurrentLngFill | `float` | $A$ | Liquid methane fill solenoid current feedback |
| solenoidCurrentLngVent | `float` | $A$ | Liquid methane GSE panel vent solenoid current feedback |
| temperatureLox | `float` | $\degree C$ | Liquid oxygen temperature |
| temperatureLng | `float` | $\degree C$ | Liquid methane temperature |
| pressureGn2 | `float` | $psi$ | Nitrogen bottle pressure, max range 5000 psi |
> `timestamp`, `igniterArmed`, `igniterContinuity`, and `solenoidInternalState*` fields are required. All other fields are optional and shall remain its default value to indicate no data.

### ECU Command Packet
```c
struct ecuCommand {
    bool solenoidStateCopvVent;
    bool solenoidStatePv1;
    bool solenoidStatePv2;
    bool solenoidStateVent;
    uint32_t crc;
};
```
| Key | Data Type | Description |
| --- | --- | --- |
| solenoidStateCopvVent | `bool` | COPV vent, normally closed |
| solenoidStatePv1 | `bool` | DLPR Dome fill, normally closed |
| solenoidStatePv2 | `bool` | DLPR Dome vent, normally open |
| solenoidStateVent | `bool` | LOX & LNG vent, normally open |
> All fields shall be filled with valid command. A value of `1` or `true` **always** corresponds to an open or flowing valve. A value of `0` or `false` **always** corresponds to a closed or blocking valve, regardless of solenoid type. The translation is handled on the device firmware.

### ECU Data Packet
```c
struct ecuData {
    uint32_t timestamp;
    float packetRssi;
    float packetLoss;
    bool solenoidInternalStateCopvVent;
    bool solenoidInternalStatePv1;
    bool solenoidInternalStatePv2;
    bool solenoidInternalStateVent;
    float supplyVoltage = std::nanf("");
    float batteryVoltage = std::nanf("");
    float solenoidCurrentCopvVent = std::nanf("");
    float solenoidCurrentPv1 = std::nanf("");
    float solenoidCurrentPv2 = std::nanf("");
    float solenoidCurrentVent = std::nanf("");
    float temperatureCopv = std::nanf("");
    float pressureCopv = std::nanf("");
    float pressureLox = std::nanf("");
    float pressureLng = std::nanf("");
    float pressureInjectorLox = std::nanf("");
    float pressureInjectorLng = std::nanf("");
    float angularVelocityX = std::nanf("");
    float angularVelocityY = std::nanf("");
    float angularVelocityZ = std::nanf("");
    float accelerationX = std::nanf("");
    float accelerationY = std::nanf("");
    float accelerationZ = std::nanf("");
    float magneticFieldX = std::nanf("");
    float magneticFieldY = std::nanf("");
    float magneticFieldZ = std::nanf("");
    float temperature = std::nanf("");
    float altitude = std::nanf("");
    float ecefPositionX = std::nanf("");
    float ecefPositionY = std::nanf("");
    float ecefPositionZ = std::nanf("");
    float ecefPositionAccuracy = std::nanf("");
    float ecefVelocityX = std::nanf("");
    float ecefVelocityY = std::nanf("");
    float ecefVelocityZ = std::nanf("");
    float ecefVelocityAccuracy = std::nanf("");
    uint32_t crc;
};
```
| Key | Data Type | Units | Description |
| --- | --- | --- | --- |
| timestamp | `unsigned long` | $ms$ | Milliseconds since Unix Epoch |
| packetRssi | `float` | $dBm$ | Radio packet receive signal strength indicator |
| packetLoss | `float` | | Radio packet loss rate ratio |
| solenoidInternalStateCopvVent | `bool` | | COPV vent solenoid state feedback |
| solenoidInternalStatePv1 | `bool` | | DLPR Dome fill solenoid state feedback |
| solenoidInternalStatePv2 | `bool` | | DLPR Dome vent solenoid state feedback |
| solenoidInternalStateVent | `bool` | | LOX & LNG vent solenoid state feedback |
| supplyVoltage | `float` | $V$ | Power supply voltage |
| batteryVoltage | `float` | $V$ | Battery supply voltage |
| solenoidCurrentCopvVent | `float` | $A$ | COPV vent solenoid current feedback |
| solenoidCurrentPv1 | `float` | $A$ | DLPR Dome fill solenoid current feedback |
| solenoidCurrentPv2 | `float` | $A$ | DLPR Dome vent solenoid current feedback |
| solenoidCurrentVent | `float` | $A$ | LOX & LNG vent solenoid current feedback |
| temperatureCopv | `float` | $\degree C$ | COPV temperature |
| pressureCopv | `float` | $psi$ | COPV pressure, max range 5000 psi |
| pressureLox | `float` | $psi$ | Liquid oxygen tank pressure, max range 1000 psi |
| pressureLng | `float` | $psi$ | Liquid methane tank pressure, max range 1000 psi |
| pressureInjectorLox | `float` | $psi$ | Injector liquid oxygen pressure, max range 1000 psi |
| pressureInjectorLng | `float` | $psi$ | Injector methane pressure, max range 1000 psi |
| angularVelocityX | `float` | $rad/s$ | Angular velocity counterclockwise along x-axis, launch vehicle frame |
| angularVelocityY | `float` | $rad/s$ | Angular velocity counterclockwise along y-axis, launch vehicle frame |
| angularVelocityZ | `float` | $rad/s$ | Angular velocity counterclockwise along z-axis, launch vehicle frame |
| accelerationX | `float` | $m/s^2$ |  Acceleration along x-axis, launch vehicle frame |
| accelerationY | `float` | $m/s^2$ |  Acceleration along y-axis, launch vehicle frame |
| accelerationZ | `float` | $m/s^2$ |  Acceleration along z-axis, launch vehicle frame |
| magneticFieldX | `float` | $T$ |  Magnetic field along x-axis, launch vehicle frame |
| magneticFieldY | `float` | $T$ |  Magnetic field along y-axis, launch vehicle frame |
| magneticFieldZ | `float` | $T$ |  Magnetic field along z-axis, launch vehicle frame |
| temperature | `float` | $\degree C$ | Environment temperature |
| altitude | `float` | $m$ | Mean sea level altitude |
| ecefPositionX | `float` | $m$ | GNSS Earth-Centered Earth-Fixed x position |
| ecefPositionY | `float` | $m$ | GNSS Earth-Centered Earth-Fixed y position |
| ecefPositionZ | `float` | $m$ | GNSS Earth-Centered Earth-Fixed z position |
| ecefPositionAccuracy | `float` | $m$ | GNSS estimated position accuracy |
| ecefVelocityX | `float` | $m/s$ | GNSS Earth-Centered Earth-Fixed x velocity |
| ecefVelocityY | `float` | $m/s$ | GNSS Earth-Centered Earth-Fixed y velocity |
| ecefVelocityZ | `float` | $m/s$ | GNSS Earth-Centered Earth-Fixed z velocity |
| ecefVelocityAccuracy | `float` | $m/s$ | GNSS estimated velocity accuracy |
> `timestamp` field is required. All other fields are optional and shall remain its default value to indicate no data.


## Standardized Memory Format
All data stored into onboard memory will use the standardized packet. All packets are 64 bytes aligned and packed little-endian. Checksums are IBM CRC-16 (polynomial `0x8005`). See files for CRC implementation in C++.

### AFS Telemetry Data
```c
struct afsTelemetryData {
    uint8_t type = 0x00;
    uint32_t timestamp;
    uint8_t state;
    int16_t angularVelocityX = 0xFFFF;
    int16_t angularVelocityY = 0xFFFF;
    int16_t angularVelocityZ = 0xFFFF;
    int16_t accelerationX = 0xFFFF;
    int16_t accelerationY = 0xFFFF;
    int16_t accelerationZ = 0xFFFF;
    int16_t magneticFieldX = 0xFFFF;
    int16_t magneticFieldY = 0xFFFF;
    int16_t magneticFieldZ = 0xFFFF;
    int16_t temperature = 0xFFFF;
    int32_t altitude = 0xFFFFFFFF;
    int32_t ecefPositionX = 0xFFFFFFFF;
    int32_t ecefPositionY = 0xFFFFFFFF;
    int32_t ecefPositionZ = 0xFFFFFFFF;
    uint32_t ecefPositionAccuracy = 0xFFFFFFFF;
    int32_t ecefVelocityX = 0xFFFFFFFF;
    int32_t ecefVelocityY = 0xFFFFFFFF;
    int32_t ecefVelocityZ = 0xFFFFFFFF;
    uint32_t ecefVelocityAccuracy = 0xFFFFFFFF;
    uint16_t crc = 0x0000;
};
```
| Key | Data Type | Units | Description |
| --- | --- | --- | --- |
| timestamp | `unsigned long` | $ms$ | Milliseconds since Unix Epoch |
| state | `unsigned char` | | System state, see `AfsState` struct below |
| angularVelocityX | `signed short` | $6.1 \cdot 10^{-2}\ \degree/s$ | Angular velocity counterclockwise along x-axis, launch vehicle frame |
| angularVelocityY | `signed short` | $6.1 \cdot 10^{-2}\ \degree/s$ | Angular velocity counterclockwise along y-axis, launch vehicle frame |
| angularVelocityZ | `signed short` | $6.1 \cdot 10^{-2}\ \degree/s$ | Angular velocity counterclockwise along z-axis, launch vehicle frame |
| accelerationX | `signed short` | $7.3 \cdot 10^{-4}\ g$ |  Acceleration along x-axis, launch vehicle frame |
| accelerationY | `signed short` | $7.3 \cdot 10^{-4}\ g$ |  Acceleration along y-axis, launch vehicle frame |
| accelerationZ | `signed short` | $7.3 \cdot 10^{-4}\ g$ |  Acceleration along z-axis, launch vehicle frame |
| magneticFieldX | `signed short` | $6.3 \cdot 10^{-8}\ T$ |  Magnetic field along x-axis, launch vehicle frame |
| magneticFieldY | `signed short` | $6.3 \cdot 10^{-8}\ T$ |  Magnetic field along y-axis, launch vehicle frame |
| magneticFieldZ | `signed short` | $6.3 \cdot 10^{-8}\ T$ |  Magnetic field along z-axis, launch vehicle frame |
| temperature | `signed short` | $10^{-2}\ \degree C$ |  Magnetic field along z-axis, launch vehicle frame |
| altitude | `signed long` | $cm$ | Mean sea level altitude |
| ecefPositionX | `signed long` | $cm$ | GNSS Earth-Centered Earth-Fixed x position |
| ecefPositionY | `signed long` | $cm$ | GNSS Earth-Centered Earth-Fixed y position |
| ecefPositionZ | `signed long` | $cm$ | GNSS Earth-Centered Earth-Fixed z position |
| ecefPositionAccuracy | `unsigned long` | $cm$ | GNSS estimated position accuracy |
| ecefVelocityX | `signed long` | $cm/s$ | GNSS Earth-Centered Earth-Fixed x velocity |
| ecefVelocityY | `signed long` | $cm/s$ | GNSS Earth-Centered Earth-Fixed y velocity |
| ecefVelocityZ | `signed long` | $cm/s$ | GNSS Earth-Centered Earth-Fixed z velocity |
| ecefVelocityAccuracy | `unsigned long` | $cm/s$ | GNSS estimated velocity accuracy |
```c
struct AfsState {
    bool armPinState : 1;       // 0: unarmed, 1: armed
    bool drogueContinuity : 1;  // 0: no continuity, 1: continuity
    bool mainContinuity : 1;    // 0: no continuity, 1: continuity
    uint16_t : 1;               // reserved
    /**
     * 0x0: standby
     * 0x1: armed
     * 0x2: boost
     * 0x3: coast
     * 0x4: apogee
     * 0x5: drogue fired
     * 0x6: drogue opened
     * 0x7 drogue failure
     * 0x8: main fired
     * 0x9: main opened
     * 0xA: main failure
     * 0xB: land
     */
    uint8_t state : 4;
};
```
> `timestamp` and `state` fields are required. All other fields are optional and shall remain its default value to indicate no data.

### ECU Telemetry Data
```c
struct ecuTelemetryData {
    uint8_t type = 0x10;
    uint32_t timestamp;
    uint64_t : 8;
    int16_t angularVelocityX = 0xFFFF;
    int16_t angularVelocityY = 0xFFFF;
    int16_t angularVelocityZ = 0xFFFF;
    int16_t accelerationX = 0xFFFF;
    int16_t accelerationY = 0xFFFF;
    int16_t accelerationZ = 0xFFFF;
    int16_t magneticFieldX = 0xFFFF;
    int16_t magneticFieldY = 0xFFFF;
    int16_t magneticFieldZ = 0xFFFF;
    int16_t temperature = 0xFFFF;
    int32_t altitude = 0xFFFFFFFF;
    int32_t ecefPositionX = 0xFFFFFFFF;
    int32_t ecefPositionY = 0xFFFFFFFF;
    int32_t ecefPositionZ = 0xFFFFFFFF;
    uint32_t ecefPositionAccuracy = 0xFFFFFFFF;
    int32_t ecefVelocityX = 0xFFFFFFFF;
    int32_t ecefVelocityY = 0xFFFFFFFF;
    int32_t ecefVelocityZ = 0xFFFFFFFF;
    uint32_t ecefVelocityAccuracy = 0xFFFFFFFF;
    uint16_t crc = 0x0000;
};
```
| Key | Data Type | Units | Description |
| --- | --- | --- | --- |
| timestamp | `unsigned long` | $ms$ | Milliseconds since Unix Epoch |
| angularVelocityX | `signed short` | $6.1 \cdot 10^{-2}\ \degree/s$ | Angular velocity counterclockwise along x-axis, launch vehicle frame |
| angularVelocityY | `signed short` | $6.1 \cdot 10^{-2}\ \degree/s$ | Angular velocity counterclockwise along y-axis, launch vehicle frame |
| angularVelocityZ | `signed short` | $6.1 \cdot 10^{-2}\ \degree/s$ | Angular velocity counterclockwise along z-axis, launch vehicle frame |
| accelerationX | `signed short` | $7.3 \cdot 10^{-4}\ g$ |  Acceleration along x-axis, launch vehicle frame |
| accelerationY | `signed short` | $7.3 \cdot 10^{-4}\ g$ |  Acceleration along y-axis, launch vehicle frame |
| accelerationZ | `signed short` | $7.3 \cdot 10^{-4}\ g$ |  Acceleration along z-axis, launch vehicle frame |
| magneticFieldX | `signed short` | $6.3 \cdot 10^{-8}\ T$ |  Magnetic field along x-axis, launch vehicle frame |
| magneticFieldY | `signed short` | $6.3 \cdot 10^{-8}\ T$ |  Magnetic field along y-axis, launch vehicle frame |
| magneticFieldZ | `signed short` | $6.3 \cdot 10^{-8}\ T$ |  Magnetic field along z-axis, launch vehicle frame |
| temperature | `signed short` | $10^{-2}\ \degree C$ |  Magnetic field along z-axis, launch vehicle frame |
| altitude | `signed long` | $cm$ | Mean sea level altitude |
| ecefPositionX | `signed long` | $cm$ | GNSS Earth-Centered Earth-Fixed x position |
| ecefPositionY | `signed long` | $cm$ | GNSS Earth-Centered Earth-Fixed y position |
| ecefPositionZ | `signed long` | $cm$ | GNSS Earth-Centered Earth-Fixed z position |
| ecefPositionAccuracy | `unsigned long` | $cm$ | GNSS estimated position accuracy |
| ecefVelocityX | `signed long` | $cm/s$ | GNSS Earth-Centered Earth-Fixed x velocity |
| ecefVelocityY | `signed long` | $cm/s$ | GNSS Earth-Centered Earth-Fixed y velocity |
| ecefVelocityZ | `signed long` | $cm/s$ | GNSS Earth-Centered Earth-Fixed z velocity |
| ecefVelocityAccuracy | `unsigned long` | $cm/s$ | GNSS estimated velocity accuracy |
> `timestamp` field is required. All other fields are optional and shall remain its default value to indicate no data.

### ECU Fluid System Data
```c
struct ecuFluidSystemData {
  uint8_t type = 0x11;
  uint32_t timestamp = 0xFFFFFFFF;
  uint16_t supplyVoltage = 0xFFFF;
  uint16_t batteryVoltage = 0xFFFF;
  uint64_t : 4;
  bool solenoidStateCopvVent : 1;
  bool solenoidStatePv1 : 1;
  bool solenoidStatePv2 : 1;
  bool solenoidStateVent : 1;
  uint16_t solenoidCurrentCopvVent = 0xFFFF;
  uint16_t solenoidCurrentPv1 = 0xFFFF;
  uint16_t solenoidCurrentPv2 = 0xFFFF;
  uint16_t solenoidCurrentVent = 0xFFFF;
  int16_t temperatureCopv = 0xFFFF;
  uint16_t pressureCopv = 0xFFFF;
  uint16_t pressureLox = 0xFFFF;
  uint16_t pressureLng = 0xFFFF;
  uint16_t pressureInjectorLox = 0xFFFF;
  uint16_t pressureInjectorLng = 0xFFFF;
  uint64_t : 64;
  uint64_t : 64;
  uint64_t : 64;
  uint64_t : 64;
  uint16_t crc = 0x0000;
};
```
| Key | Data Type | Units | Description |
| --- | --- | --- | --- |
| timestamp | `unsigned long` | $ms$ | Milliseconds since Unix Epoch |
| supplyVoltage | `unsigned short` | $mV$ | Power supply voltage |
| batteryVoltage | `unsigned short` | $mV$ | Battery supply voltage |
| solenoidStateCopvVent | `bool` | | COPV vent solenoid state |
| solenoidStatePv1 | `bool` | | DLPR Dome fill solenoid state |
| solenoidStatePv2 | `bool` | | DLPR Dome vent solenoid state |
| solenoidStateVent | `bool` | | LOX & LNG vent solenoid state |
| solenoidCurrentCopvVent | `unsigned short` | $mA$ | COPV vent solenoid current feedback |
| solenoidCurrentPv1 | `unsigned short` | $mA$ | DLPR Dome fill solenoid current feedback |
| solenoidCurrentPv2 | `unsigned short` | $mA$ | DLPR Dome vent solenoid current feedback |
| solenoidCurrentVent | `unsigned short` | $A$ | LOX & LNG vent solenoid current feedback |
| temperatureCopv | `signed short` | $10^{-2}\ \degree C$ | COPV temperature |
| pressureCopv | `unsigned short` | $10^{-1}\ psi$ | COPV pressure, max range 5000 psi |
| pressureLox | `unsigned short` | $10^{-1}\ psi$ | Liquid oxygen tank pressure, max range 1000 psi |
| pressureLng | `unsigned short` | $10^{-1}\ psi$ | Liquid methane tank pressure, max range 1000 psi |
| pressureInjectorLox | `unsigned short` | $10^{-1}\ psi$ | Injector oxygen pressure, max range 1000 psi |
| pressureInjectorLng | `unsigned short` | $10^{-1}\ psi$ | Injector methane pressure, max range 1000 psi |
> `timestamp` field is required. All other fields are optional and shall remain its default value to indicate no data.

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


## Standardized Connector
![Pin](/media/gx16_pinout.png)

### 24V Power
GX16 Connector \
1: +24V \
2: GND

### Battery Power
XT60 Connector \
1 (Square): +24V \
2 (Round): GND

### Solenoid
GX16 Connector \
1: +24V \
2: Control \
3: GND

### Pressure Transducer (PT)
GX12 Connector \
1: +24V \
2: Signal \
3: GND

### Thermocouple (TC)
ANSI Miniature Thermocouple Connector (K Type, Yellow) \
+: Nickel-Chromium \
-: Nickel-Alumel

### CAN Bus
GX12 Connector \
1: +24V \
2: CAN High \
3: CAN Low \
4: GND

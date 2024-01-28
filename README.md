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


## Standardized Communication Format
All ground communication will be over TCP/IP Ethernet and use the standardized packet format. All packets are packed little-endian.

### ECU Command Packet
```c
struct ecuCommand {
    uint64_t : 4;
    bool solenoidStateGn2Vent : 1;
    bool solenoidStatePv1 : 1;
    bool solenoidStatePv2 : 1;
    bool solenoidStateVent : 1;
};
```
| Key | Data Type | Description |
| --- | --- | --- |
| solenoidStateGn2Vent | `bool` | COPV vent, normally closed |
| solenoidStatePv1 | `bool` | DLPR Dome fill, normally closed |
| solenoidStatePv2 | `bool` | DLPR Dome vent, normally open |
| solenoidStateVent | `bool` | LOX & LNG vent, normally open |
> All fields shall be filled with valid command. A value of `1` or `true` **always** corresponds to an open or flowing valve. A value of `0` or `false` **always** corresponds to a closed or blocking valve, regardless of solenoid type. The translation is handled on the device firmware.

### ECU Data Packet
```c
struct ecuData {
    uint32_t timestamp;
    float solenoidCurrentGn2Vent = std::nanf("");
    float solenoidCurrentPv1 = std::nanf("");
    float solenoidCurrentPv2 = std::nanf("");
    float solenoidCurrentVent = std::nanf("");
    float temperatureGn2 = std::nanf("");
    float pressureGn2 = std::nanf("");
    float pressureLox = std::nanf("");
    float pressureLng = std::nanf("");
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
};
```
| Key | Data Type | Units | Description |
| --- | --- | --- | --- |
| timestamp | `unsigned long long` | $ms$ | Milliseconds since Unix Epoch |
| solenoidCurrentGn2Vent | `float` | $A$ | COPV vent solenoid current feedback |
| solenoidCurrentPv1 | `float` | $A$ | DLPR Dome fill solenoid current feedback |
| solenoidCurrentPv2 | `float` | $A$ | DLPR Dome vent solenoid current feedback |
| solenoidCurrentVent | `float` | $A$ | LOX & LNG vent solenoid current feedback |
| temperatureGn2 | `float` | $\degree C$ | COPV temperature |
| pressureGn2 | `float` | $psi$ | COPV pressure, max range 5000 psi |
| pressureLox | `float` | $psi$ | Liquid oxygen tank pressure, max range 1000 psi |
| pressureLng | `float` | $psi$ | Liquid methane tank pressure, max range 1000 psi |
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

### GSE Command Packet
```c
struct gseCommand {
    uint64_t : 6;
    bool igniterFire : 1;
    bool solenoidStateGn2Fill : 1;
    bool solenoidStateGn2Vent : 1;
    bool solenoidStateMvasFill : 1;
    bool solenoidStateMvasVent : 1;
    bool solenoidStateMvas : 1;
    bool solenoidStateLoxFill : 1;
    bool solenoidStateLoxVent : 1;
    bool solenoidStateLngFill : 1;
    bool solenoidStateLngVent : 1;
};
```
| Key | Data Type | Description |
| --- | --- | --- |
| igniterFire | `bool` | Fire igniter |
| solenoidStateGn2Fill | `bool` | Nitrogen vehicle fill, normally closed |
| solenoidStateGn2Vent | `bool` |  Nitrogen GSE panel vent, normally open |
| solenoidStateMvasFill | `bool` | MVAS line fill, normally closed |
| solenoidStateMvasVent | `bool` | MVAS line vent, normally open |
| solenoidStateMvas | `bool` | Actuate MVAS, normally closed |
| solenoidStateLoxFill | `bool` | Liquid oxygen fill, normally closed |
| solenoidStateLoxVent | `bool` | Liquid oxygen GSE panel vent, normally closed |
| solenoidStateLngFill | `bool` | Liquid methane fill, normally closed |
| solenoidStateLngVent | `bool` | Liquid methane GSE panel vent, normally closed |
> All fields shall be filled with valid command. For solenoids, a value of `1` or `true` **always** corresponds to an open or flowing valve. A value of `0` or `false` **always** corresponds to a closed or blocking valve, regardless of solenoid type. The translation is handled on the device firmware.

### GSE Data Packet
```c
struct gseData {
    uint32_t timestamp;
    uint64_t : 6;
    bool igniterArmed : 1;
    bool igniterContinuity : 1;
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
};
```
| Key | Data Type | Units | Description |
| --- | --- | --- | --- |
| timestamp | `unsigned long long` | $ms$ | Milliseconds since Unix Epoch |
| igniterArmed | `bool` | | Igniter arming key state, `1` for armed |
| igniterContinuity | `bool` | | Igniter continuity, `1` for continuity detected |
| solenoidCurrentGn2Fill | `float` | $A$ | Nitrogen vehicle fill solenoid current feedback |
| solenoidCurrentGn2Vent | `float` | $A$ | Nitrogen GSE panel vent solenoid current feedback |
| solenoidCurrentMvasFill | `float` | $A$ | MVAS line fill solenoid current feedback |
| solenoidCurrentMvasVent | `float` | $A$ | MVAS line vent solenoid current feedback |
| solenoidCurrentMvas | `float` | $A$ | MVAS actuation solenoid current feedback |
| solenoidCurrentLoxFill | `float` | $A$ | Liquid oxygen fill solenoid current feedback |
| solenoidCurrentLoxVent | `float` | $A$ | Liquid oxygen GSE panel vent solenoid current feedback |
| solenoidCurrentLngFill | `float` | $A$ | Liquid methane fill solenoid current feedback |
| solenoidCurrentLngVent | `float` | $A$ | Liquid methane GSE panel vent solenoid current feedback |
> `timestamp`, `igniterArmed`, and `igniterContinuity` fields are required. All other fields are optional and shall remain its default value to indicate no data.


## Standardized Memory Format
All data stored into onboard memory will use the standardized packet. All packets are 64 bytes aligned and packed little-endian. Checksums are IBM CRC-16 (polynomial `0x8005`).

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
| timestamp | `unsigned long long` | $ms$ | Milliseconds since Unix Epoch |
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
| timestamp | `unsigned long long` | $ms$ | Milliseconds since Unix Epoch |
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
  uint64_t : 4;
  bool solenoidStateGn2Vent : 1;
  bool solenoidStatePv1 : 1;
  bool solenoidStatePv2 : 1;
  bool solenoidStateVent : 1;
  uint16_t solenoidCurrentGn2Vent = 0xFFFF;
  uint16_t solenoidCurrentPv1 = 0xFFFF;
  uint16_t solenoidCurrentPv2 = 0xFFFF;
  uint16_t solenoidCurrentVent = 0xFFFF;
  int16_t temperatureGn2 = 0xFFFF;
  uint16_t pressureGn2 = 0xFFFF;
  uint16_t pressureLox = 0xFFFF;
  uint16_t pressureLng = 0xFFFF;
  uint64_t : 64;
  uint64_t : 64;
  uint64_t : 64;
  uint64_t : 64;
  uint64_t : 64;
  uint16_t crc = 0x0000;
};
```
| Key | Data Type | Units | Description |
| --- | --- | --- | --- |
| timestamp | `unsigned long long` | $ms$ | Milliseconds since Unix Epoch |
| solenoidStateGn2Vent | `bool` | | COPV vent solenoid state |
| solenoidStatePv1 | `bool` | | DLPR Dome fill solenoid state |
| solenoidStatePv2 | `bool` | | DLPR Dome vent solenoid state |
| solenoidStateVent | `bool` | | LOX & LNG vent solenoid state |
| solenoidCurrentGn2Vent | `unsigned short` | $mA$ | COPV vent solenoid current feedback |
| solenoidCurrentPv1 | `unsigned short` | $mA$ | DLPR Dome fill solenoid current feedback |
| solenoidCurrentPv2 | `unsigned short` | $mA$ | DLPR Dome vent solenoid current feedback |
| solenoidCurrentVent | `unsigned short` | $A$ | LOX & LNG vent solenoid current feedback |
| temperatureGn2 | `signed short` | $10^{-2}\ \degree C$ | COPV temperature |
| pressureGn2 | `unsigned short` | $10^{-1}\ psi$ | COPV pressure, max range 5000 psi |
| pressureLox | `unsigned short` | $10^{-1}\ psi$ | Liquid oxygen tank pressure, max range 1000 psi |
| pressureLng | `unsigned short` | $10^{-1}\ psi$ | Liquid methane tank pressure, max range 1000 psi |
> `timestamp` field is required. All other fields are optional and shall remain its default value to indicate no data.


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


## Hardware Contributing Guidelines
1. Use `main` instead of `master`.
2. Use [KiCAD 7](https://www.kicad.org/).
3. Use provided [.gitignore](/hardware/.gitignore). Do not include fabrication files (Gerber, BOM, etc).
4. Use KiCAD standard component and footprint library for simple components (passives, transistors, standard footprints, etc). Use [UCIRP-KiCAD-Lib](https://github.com/UCI-Rocket-Project/UCIRP-KiCAD-Lib) as Git submodule for complex and non-standard components. **Do not import and use any other online library such as Adafruit, create new symbols and footprints if required and contribute to the shared library.**
5. Make changes directly on the `main` branch. Ensure local files are up to date before making changes. Merge conflicts are practically irreconcilable, do not attempt to merge.
6. Do not create hierarchical sheets.


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

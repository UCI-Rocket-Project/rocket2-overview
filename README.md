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


## Standardized JSON Format
All ground communication will be over TCP/IP Ethernet and use the standardized JSON format. Data that is not available shall be omitted. Do not send filler or default data.

### Required Fields
| Key | Data Type | Units | Description |
| --- | --- | --- | --- |
| `timestamp` | `unsigned long long` | ms | Milliseconds since Unix Epoch |

### ECU Data
| Key | Data Type | Units | Description
| --- | --- | --- | --- |
| gyroscopeX | `float` | $rad/s$ | Angular velocity along x-axis, launch vehicle frame |
| gyroscopeY | `float` | $rad/s$ | Angular velocity along y-axis, launch vehicle frame |
| gyroscopeZ | `float` | $rad/s$ | Angular velocity along z-axis, launch vehicle frame |
| accelerometerX | `float` | $m/s^2$ |  Acceleration along x-axis, launch vehicle frame |
| accelerometerY | `float` | $m/s^2$ |  Acceleration along y-axis, launch vehicle frame |
| accelerometerZ | `float` | $m/s^2$ |  Acceleration along z-axis, launch vehicle frame |
| magnetometerX | `float` | $\mu T$ |  Magnetic field along x-axis, launch vehicle frame |
| magnetometerY | `float` | $\mu T$ |  Magnetic field along y-axis, launch vehicle frame |
| magnetometerZ | `float` | $\mu T$ |  Magnetic field along z-axis, launch vehicle frame |
| altimeterTemperature | `float` | $\degree C$ | Environment temperature |
| altimeterAltitude | `float` | $m$ | Mean sea level altitude |
| gnssEcefPositionX | `float` | $m$ | GNSS Earth-Centered Earth-Fixed x position |
| gnssEcefPositionY | `float` | $m$ | GNSS Earth-Centered Earth-Fixed y position |
| gnssEcefPositionZ | `float` | $m$ | GNSS Earth-Centered Earth-Fixed z position |
| gnssEcefPositionAccuracy | `float` | $m$ | GNSS estimated position accuracy |
| gnssEcefVelocityX | `float` | $m/s$ | GNSS Earth-Centered Earth-Fixed x velocity |
| gnssEcefVelocityY | `float` | $m/s$ | GNSS Earth-Centered Earth-Fixed y velocity |
| gnssEcefVelocityZ | `float` | $m/s$ | GNSS Earth-Centered Earth-Fixed z velocity |
| gnssEcefVelocityAccuracy | `float` | $m/s$ | GNSS estimated velocity accuracy |

### GSE Data
TBD.

### Tracking Data
TBD.


## Standardized Connector
![Pin](/media/gx16_pinout.png)

### 24V Power
GX20 Connector \
1: +24V \
2: GND

### Battery Power
XT60 Connector \
1 (Square): +24V \
2 (Round): GND

### Solenoid
GX16 Connector \
1: +24V \
2: GND / Control

### Pressure Transducer (PT)
GX16 Connector \
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
2: +3.3V \
3: CAN Low \
4: CAN High \
5: Reserved \
6: GND


## Standardized Memory Packets
All data stored into onboard memory will use the standardized packet. All packets are 64 bytes aligned and packed little-endian.

### Telemetry Data
```c
struct TelemetryData {
    uint8_t type = 0x00;
    uint32_t timestamp = 0xFFFFFFFF;             // ms since startup
    uint8_t state = 0x00;                        // state for AFS, unused on ECU
    int16_t imuGyroscopeX = 0xFFFF;              // 0.061 deg/s per LSB
    int16_t imuGyroscopeY = 0xFFFF;              // 0.061 deg/s per LSB
    int16_t imuGyroscopeZ = 0xFFFF;              // 0.061 deg/s per LSB
    int16_t imuAccelerometerX = 0xFFFF;          // 0.00073g per LSB
    int16_t imuAccelerometerY = 0xFFFF;          // 0.00073g per LSB
    int16_t imuAccelerometerZ = 0xFFFF;          // 0.00073g per LSB
    int16_t imuMagnetometerX = 0xFFFF;           // 0.063 uT per LSB
    int16_t imuMagnetometerY = 0xFFFF;           // 0.063 uT per LSB
    int16_t imuMagnetometerZ = 0xFFFF;           // 0.063 uT per LSB
    int16_t altimeterTemperature = 0xFFFF;       // 10^-2 C
    int32_t altimeterAltitude = 0xFFFFFFFF;      // cm
    int32_t gnssEcefPositionX = 0xFFFFFFFF;      // cm
    int32_t gnssEcefPositionY = 0xFFFFFFFF;      // cm
    int32_t gnssEcefPositionZ = 0xFFFFFFFF;      // cm
    uint32_t gnssPositionAccuracy = 0xFFFFFFFF;  // cm
    int32_t gnssEcefVelocityX = 0xFFFFFFFF;      // cm/s
    int32_t gnssEcefVelocityY = 0xFFFFFFFF;      // cm/s
    int32_t gnssEcefVelocityZ = 0xFFFFFFFF;      // cm/s
    uint32_t gnssVelocityAccuracy = 0xFFFFFFFF;  // cm/s
    uint16_t crc = 0x0000;
};
```
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

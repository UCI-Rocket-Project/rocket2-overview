# Rocket 2 Avionics System

## Overview
![SystemArchitecture](/system_architecture.svg)


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
| ecuGyroscopeX | `float` | $rad/s$ | Angular velocity along x-axis |
| ecuGyroscopeY | `float` | $rad/s$ | Angular velocity along y-axis |
| ecuGyroscopeZ | `float` | $rad/s$ | Angular velocity along z-axis |
| ecuAccelerometerX | `float` | $m/s^2$ |  Acceleration along x-axis |
| ecuAccelerometerY | `float` | $m/s^2$ |  Acceleration along y-axis |
| ecuAccelerometerZ | `float` | $m/s^2$ |  Acceleration along z-axis |
| ecuMagnetometerX | `float` | $\mu T$ |  Magnetic field along x-axis |
| ecuMagnetometerY | `float` | $\mu T$ |  Magnetic field along y-axis |
| ecuMagnetometerZ | `float` | $\mu T$ |  Magnetic field along z-axis |
| ecuAltimeterPressure | `float` | $hPa$ | Environment pressure |
| ecuAltimeterTemperature | `float` | $\degree C$ | Environment temperature |
| ecuAltimeterAltitude | `float` | $m$ | Mean sea level altitude |
| ecuGnssLongitude | `float` | $\degree$ | GNSS longitude |
| ecuGnssLatitude | `float` | $\degree$ | GNSS latitude |
| ecuGnssAltitude | `float` | $m$ | GNSS altitude |

### GSE Data
TBD.

### Tracking Data
TBD.


## Hardware Contributing Guidelines
1. Use [KiCAD 7](https://www.kicad.org/).
2. Use provided [.gitignore](/hardware/.gitignore). Do not include fabrication files (Gerber, BOM, etc).
3. Use KiCAD standard component and footprint library for simple components (passives, transistors, standard footprints, etc). Use [UCIRP-KiCAD-Lib](https://github.com/UCI-Rocket-Project/UCIRP-KiCAD-Lib) as Git submodule for complex and non-standard components. **Do not import and use any other online library such as Adafruit, create new symbols and footprints if required and contribute to the shared library.**
4. Make changes directly on the `master` branch. Ensure local files are up to date before making changes. Merge conflicts are practically irreconcilable, do not attempt to merge.
5. Do not create hierarchical sheets.


## Software Contributing Guidelines
1. Use [pre-commit](https://pre-commit.com/). Copy provided [.pre-commit-config.yaml](/software/.pre-commit-config.yaml) to project repository root and install.
    - Python files are formatted with [Black](https://github.com/psf/black). No additional actions necessary.
    - C/C++ and Javascript files are formatted with [clang-format](https://clang.llvm.org/docs/ClangFormat.html). Copy provided [.clang-format](/software/.clang-format) to project repository root.
2. Each software module (each repository) shall have a singular command or script that enables the whole module.
3. Naming conventions are as follows:
    - Files: `snake_case.c`
    - Variables: `camelCase`
    - Constant variables: `UPPER_SNAKE_CASE`
    - Functions: `PascalCase`
    - Types: `PascalCase`
    - Private variables within classes: `_camelCaseWithUnderscorePrefix`

## Embedded Firmware Contributing Guidelines
1. Follow all items in Software Contributing Guidelines.
2. Use [PlatformIO](https://platformio.org/).

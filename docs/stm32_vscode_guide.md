# Overview
This guide outlines the standard procedure for initializing an STM32 project using **STM32CubeMX** with **VSCode**

---

## 1. Project Initialization
Depending on your starting point, choose one of the two methods below:

### Option A: New Project
1. Navigate to `File` > `New Project`.
2. Use the **MCU/Board Selector** to choose your specific hardware.

### Option B: Import Existing Configuration (`.ioc`)
1. Navigate to `File` > `Import Project`.
2. Browse to and select your `.ioc` file path.
3. Click **OK** to load the existing configuration (default settings are recommended).

---

## 2. Project Manager Configuration
Switch to the **Project Manager** tab and apply the following settings:

| Setting | Selection |
| :--- | :--- |
| **Project Name** | *Insert your project name* |
| **Application Structure** | `Advanced` |
| **Toolchain / IDE** | `CMake` |

![STM32CubeMX Configuration](https://github.com/user-attachments/assets/fc09d8b5-b3bc-4903-a7c2-8e85d68451b1)

---

## 3. IOC configuration
IOC file configuration is entirely dependent on your use case. But here's an example:
<img width="671" height="724" alt="image" src="https://github.com/user-attachments/assets/8ba7b1ef-51e4-42ee-a377-30ced45a5884" />

Some common mistakes on IOC configuration:
* Make sure to run the automatic clock configuration with the desired **EXTERNAL** crystal oscillator frequency.
  * If you don't do this, then the stm32 will just run off of its internal crystal.
  * Important if you are trying to use USB 2.0 high speed
* Keep in mind the prescalars and baud rates for SPI/I2C and UART respectively.
  * 90% of the time for an error when trying to talk to a downstream device, like reading nothing or straight garbage, it's because your timing is wrong from these settings.

## 4. Code Generation
Before finalizing, ensure your settings match the configuration shown below:

1. Click **GENERATE CODE** in the top-right corner.
2. STM32CubeMX will generate the `CMakeLists.txt` and necessary HAL/LL drivers.

---

## 4. VSCode Setup & Extensions
Once you click **GENERATE CODE** in CubeMX, transition to VSCode:

### Required Extensions
* **STM32 VS Code Extension** (Official from STMicroelectronics)
* **CMake Tools**

### Workspace Initialization
1. Open the generated project folder in VSCode.
2. In the bottom right notification, select **"Open CMake project as STM32 Project"**.
3. Open the **STM32 Cube Extension** tab on the left sidebar.
4. Select your specific board/probe and save the configuration.

---

## 5. Building the Project
To verify the initial C project:
1. Open the **CMake** extension tab.
2. Locate the **"Run Task"** pinned command (hover over the header to reveal the play button).
3. Select `CMake: Build`.
4. Ensure the terminal outputs: `[driver] Build finished successfully.`

---

## 6. Upgrading to C++ & Custom Drivers
To support C++ development and include custom driver libraries (e.g., `RocketDrivers`), follow these steps:

### File Structure Setup
1. Create `Core/Src/main.cpp`.
2. Create `Core/Inc/cpp_main.h`.
3. Create a folder named `RocketDrivers` in the root directory for your custom libraries.

### Bridge C to C++
In **`main.c`**, wrap your C++ entry point to prevent linker errors:
### C file
```c
/* USER CODE BEGIN Includes */
#include "cpp_main.h"
/* USER CODE END Includes */

// ... inside main() function ...

/* USER CODE BEGIN 2 */
cpp_main();
/* USER CODE END 2 */
```
### Cmake file
```cmake
# 1. Enable CXX (C++) Support
enable_language(C ASM CXX)

# 2. Define your Chip Family (Example: STM32F1)
target_compile_definitions(${CMAKE_PROJECT_NAME} PRIVATE STM32F1)

# 3. Recursive Driver Discovery
file(GLOB_RECURSE ALL_DRIVER_SOURCES 
    "RocketDrivers/*.c"
    "RocketDrivers/*.cc"
    "RocketDrivers/*.cpp"
)

# Find all directories containing headers in RocketDrivers
file(GLOB_RECURSE ALL_DRIVER_HEADERS "RocketDrivers/*.h")
set(ALL_DRIVER_INC_DIRS "")
foreach(_headerFile ${ALL_DRIVER_HEADERS})
    get_filename_component(_dir ${_headerFile} DIRECTORY)
    list(APPEND ALL_DRIVER_INC_DIRS ${_dir})
endforeach()
list(REMOVE_DUPLICATES ALL_DRIVER_INC_DIRS)

# 4. Add sources to executable
target_sources(${CMAKE_PROJECT_NAME} PRIVATE
    ${ALL_DRIVER_SOURCES}
    Core/Src/main.cpp
)

# 5. Add include paths
target_include_directories(${CMAKE_PROJECT_NAME} PRIVATE
    ${ALL_DRIVER_INC_DIRS}
)
```

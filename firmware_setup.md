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


# TO BE UPDATED BELOW 
the following hasn't been updated to be readable but generally has what's needed to build a project





## 4. Next Steps
Once the code is generated, you can build the project using:
```bash
mkdir build
cd build
cmake ..
make
  generate code
     make sure to download firmware packages as necessary
}

In VSCode {
  extensions: "STM32CubeIDE for Visual Studio Code"

  open project folder

  select Debug configuration preset

  in the bottom right, select to open cmake project as stm32 project

  go to stm32cube extension tag on left bar

  setup stm32cubeide project

  select your board

  save all
}

We will run build to test, then do the more advanced things.

To Build {
  Open CMake extension tab (should be installed with cubeide)

  Press the play button to the right of the "Run Task" pinned command (hidden until hover)

  Select cmake -> CMake: Build

  Build should succeed in terminal at bottom: "build finished successfully."
}

Status report: Project is currently C base project. We will be upgrading it to support cpp and include our drivers.

To upgrade to cpp and include drivers {
  Add main.cpp to Core/Src directory. Base code is available in the main.cpp file

  Add cpp_main.h to Core/Inc directory. Code is available in the cpp_main.h file

  Open main.c (NOT main.cpp). In the "USER CODE BEGIN Includes" section insert:
    #include "cpp_main.h"

  In the "USER CODE BEGIN 2" section insert:
    cpp_main();

  Make a "RocketDrivers" folder

  Go into CMakeLists.txt
    edit `enable_language(C ASM)` to be `enable_language(C ASM CXX)`
    add `STM32F1` or whatever your STM chip family is in the `target_compile_definitions` directive

  Replace the `target_sources` and `target_include_directories` directives with:

``````````````````````````````````````````````````````````
file(GLOB_RECURSE ALL_DRIVER_SOURCES 
    "RocketDrivers/*.c"
    "RocketDrivers/*.cc"
    "RocketDrivers/*.cpp"
)

# B. Recursively find all directories that contain .h files
# (This allows you to include "header.h" directly, even if it's deep in a folder)
file(GLOB_RECURSE ALL_DRIVER_HEADERS "RocketDrivers/*.h")
set(ALL_DRIVER_INC_DIRS "")
foreach(_headerFile ${ALL_DRIVER_HEADERS})
    get_filename_component(_dir ${_headerFile} DIRECTORY)
    list(APPEND ALL_DRIVER_INC_DIRS ${_dir})
endforeach()
list(REMOVE_DUPLICATES ALL_DRIVER_INC_DIRS)

# Add sources to executable
target_sources(${CMAKE_PROJECT_NAME} PRIVATE
    # Add user sources here
    ${ALL_DRIVER_SOURCES}
    Core/Src/main.cpp
)

# Add include paths
target_include_directories(${CMAKE_PROJECT_NAME} PRIVATE
    # Add user defined include paths
    ${ALL_DRIVER_INC_DIRS}
)
``````````````````````````````````````````````````````````
}

Run a build to test this setup

cmake clean if project needs to be relinked

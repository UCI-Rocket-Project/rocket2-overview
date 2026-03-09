Make sure latest cubeide and cubemx are installed (cubeide not necessary)

In cube mx {
  file-> new project

  select MCU

  IF IMPORTING IOC {
    file -> import project

    insert ioc path

    default settings are good

    press ok
  }

  go to project manager tab

  insert project name


  application structure: advanced

  toolchain/IDE: CMake

  save project (file -> save)

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

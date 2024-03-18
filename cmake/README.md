# CMake crashcourse


## Quick start

Project voorbeeld:

```
little_rtos/
├── CMakeLists.txt
├── libs
│   └── io
│       ├── CMakeLists.txt
│       ├── inc
│       │   └── spi.h
│       │   └── wire.h
│       └── src
│           └── spi.c
│           └── wire.c
└── src
    └── main.c
```

Bijbehorende CMakeLists.txt:

```
cmake_minimum_required(VERSION 3.10)

project(little_rtos)

add_subdirectory(libs/io)

set(SOURCES src/main.c)

add_executable(${PROJECT_NAME} ${SOURCES})
target_link_libraries(${PROJECT_NAME} io)
```


```cmake
# minimum vereiste cmake versie
CMAKE_MINIMUM_REQUIRED(VERSION 3.10)

# set the project name
PROJECT(Simple Rtos Project)

# add the library
ADD_LIBRARY(HelloWorld SHARED helloworld.h helloworld.c)

# add the executable
ADD_EXECUTABLE(HelloWorld ${CMAKE_CURRENT_SOURCE_DIR}/helloworld.c)
```


```
cmake_minimum_required(VERSION 3.11.1)

project(targetPrj)

set(CMAKE_SYSTEM_NAME Generic)
set(CMAKE_SYSTEM_PROCESSOR arm)
set(CMAKE_CROSSCOMPILING 1)

set(GCC_PATH
/tri/ti/css901/ccs/tools/compiler/gcc-arm-none-eabi-7-2017-q4-major/bin)

set(CMAKE_C_COMPILER ${GCC_PATH}/arm-none-eabi-gcc CACHE PATH "" FORCE)

set(SOURCES src/main.c src/tm4c123gh6pm_startup_ccs_gcc.c)

set(CMAKE_C_FLAGS "-mcpu=cortex-m4 -march=armv7e-m -mthumb -mfloat-abi=hard
-mfpu=fpv4-sp-d16 -DPART_TM4C123GH6PM -ffunction-sections -fdata-sections -Wall
-specs=\"nosys.specs\"")

set(CMAKE_C_FLAGS_RELEASE "-Os")
set(CMAKE_C_FLAGS_DEBUG "-Og -g -gdwarf-3 -gstrict-dwarf")
set(CMAKE_EXE_LINKER_FLAGS "--entry ResetISR -Wl,-T\"../tm4c123gh6pm.lds\"")

add_subdirectory(libs/hello)

add_executable(${PROJECT_NAME}.elf ${SOURCES})
target_link_libraries(${PROJECT_NAME}.elf hello)
```

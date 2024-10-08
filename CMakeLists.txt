# Copyright (c) 2022 Mr. Green's Workshop https://www.MrGreensWorkshop.com
# SPDX-License-Identifier: Apache-2.0

# Set minimum CMake version
cmake_minimum_required(VERSION 3.17)

# Include the subsidiary .cmake file to get the SDK
include($ENV{PICO_SDK_PATH}/pico_sdk_init.cmake)

project(pico_examples C CXX ASM)
set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

if (PICO_SDK_VERSION_STRING VERSION_LESS "1.3.0")
    message(FATAL_ERROR "Raspberry Pi Pico SDK version 1.3.0 (or later) required. Your version is ${PICO_SDK_VERSION_STRING}")
endif()



# Set the name and version of the project
project(PicoBTSPP VERSION 1.0.0)

# Set the board to Raspberry Pi Pico W
set(PICO_BOARD pico_w)

# Initialize the SDK
pico_sdk_init()

# Set top source code folder
set(SOURCE_FOLDER "src")

# Set build type (options: 'Debug', 'Release', 'MinSizeRel', 'RelWithDebInfo')
set(default_build_type "Release")

# Set CMAKE_BUILD_TYPE
set(CMAKE_BUILD_TYPE "${default_build_type}" CACHE STRING "Choose the type of build, options are: 'Debug', 'Release', 'MinSizeRel', 'RelWithDebInfo'." FORCE)


# Add an executable target for the project
add_executable(${PROJECT_NAME})

# Get all C and C++ files, when the glob value changes, cmake will run again and update the files
file(GLOB_RECURSE app_src CONFIGURE_DEPENDS "${SOURCE_FOLDER}/*.c" "${SOURCE_FOLDER}/*.cpp" )

# Add C and C++ source files to the build
target_sources(${PROJECT_NAME} PRIVATE ${app_src})

# Link the Project to extra libraries
target_link_libraries(${PROJECT_NAME} PRIVATE
  pico_stdlib
  pico_btstack_classic
  pico_btstack_cyw43
  pico_cyw43_arch_none
)

# Including header files directly from project directory
target_include_directories(${PROJECT_NAME} PRIVATE
    ${CMAKE_CURRENT_LIST_DIR}
)

# Export binaries like hex, bin, and uf2 files.
pico_add_extra_outputs(${PROJECT_NAME})

# Enable or Disable UART
pico_enable_stdio_uart(${PROJECT_NAME} 0)

# Enable or Disable USB CDC
pico_enable_stdio_usb(${PROJECT_NAME} 1)

# Disable DTR check for USB CDC connection
add_definitions(-DPICO_STDIO_USB_CONNECTION_WITHOUT_DTR=1)

# Generated Cmake Pico project file

cmake_minimum_required(VERSION 3.13)

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

# initalize pico_sdk from installed location
# (note this can come from environment, CMake cache etc)
# set(PICO_SDK_PATH "/home/mathew/src/pico/pico-sdk")

# Pull in Raspberry Pi Pico SDK (must be before project)
include(pico_sdk_import.cmake)

project(TestProject C CXX ASM)

set(PICO_CXX_ENABLE_EXCEPTIONS 1)

# Initialise the Raspberry Pi Pico SDK
pico_sdk_init()

add_subdirectory(blink)
add_subdirectory(hello_world)
add_subdirectory(picow_ntp_client)

# Add the standard library to the build
# target_link_libraries(TestProject
#         pico_stdlib
#         pico_cyw43_arch_none     # we need Wifi to access the GPIO, but we don't need anything else
# )

# Add any user requested libraries
# target_link_libraries(TestProject
#         hardware_spi
#         hardware_i2c
#         hardware_dma
#         hardware_clocks
#         )

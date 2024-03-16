add_executable(hello_world
        hello_world.c
        )

target_link_libraries(hello_world
        pico_stdlib              # for core functionality
        pico_cyw43_arch_none     # we need Wifi to access the GPIO, but we don't need anything else
        )

# enable usb output, disable uart output
pico_enable_stdio_usb(hello_world 1)
pico_enable_stdio_uart(hello_world 0)
# create map/bin/hex file etc.
pico_add_extra_outputs(hello_world)

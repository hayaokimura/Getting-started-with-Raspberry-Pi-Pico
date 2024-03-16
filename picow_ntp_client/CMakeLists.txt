set(WIFI_SSID "${WIFI_SSID}" CACHE INTERNAL "WiFi SSID for examples")
set(WIFI_PASSWORD "${WIFI_PASSWORD}" CACHE INTERNAL "WiFi password for examples")

add_executable(picow_ntp_client_background
        picow_ntp_client.c
        )

# enable usb output, disable uart output
pico_enable_stdio_usb(picow_ntp_client_background 1)
pico_enable_stdio_uart(picow_ntp_client_background 0)

target_compile_definitions(picow_ntp_client_background PRIVATE
        WIFI_SSID=\"${WIFI_SSID}\"
        WIFI_PASSWORD=\"${WIFI_PASSWORD}\"
        )
target_include_directories(picow_ntp_client_background PRIVATE
        ${CMAKE_CURRENT_LIST_DIR}
        ${CMAKE_CURRENT_LIST_DIR}/.. # for our common lwipopts
)
target_link_libraries(picow_ntp_client_background
        pico_cyw43_arch_lwip_threadsafe_background
        pico_stdlib
        )

pico_add_extra_outputs(picow_ntp_client_background)

add_executable(picow_ntp_client_poll
        picow_ntp_client.c
        )

# enable usb output, disable uart output
pico_enable_stdio_usb(picow_ntp_client_poll 1)
pico_enable_stdio_uart(picow_ntp_client_poll 0)

target_compile_definitions(picow_ntp_client_poll PRIVATE
        WIFI_SSID=\"${WIFI_SSID}\"
        WIFI_PASSWORD=\"${WIFI_PASSWORD}\"
        )
target_include_directories(picow_ntp_client_poll PRIVATE
        ${CMAKE_CURRENT_LIST_DIR}
        ${CMAKE_CURRENT_LIST_DIR}/.. # for our common lwipopts
        )
target_link_libraries(picow_ntp_client_poll
        pico_cyw43_arch_lwip_poll
        pico_stdlib
        )
pico_add_extra_outputs(picow_ntp_client_poll)

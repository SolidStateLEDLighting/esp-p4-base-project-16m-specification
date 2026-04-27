## CMakeLists.txt Files
Here is an example of that format that I want you to learn and reproduce.

<example start>
#
# Use the Global Recursive action to gather up all the source files under a variable called SOURCES
#
# NOTE: Anytime you add a new .cpp files, the entire project must be cleaned and rebuilt or the new files
# will not be included in the compilation process.  This is the one drawback of GLOB_RECURSE implicit inclusion.
#
FILE(GLOB_RECURSE SOURCES ${PROJECT_DIR}/main/src/*.cpp)
#
# Included components which are exposed in public header files.
set(REQUIRES
    driver         # IDF components
    esp_timer
    efuse
    json           # FreeRTOS components
    network_wifi   # Local project components
    network_aws
    bus_spi
)
#
# Components that must be included, but may remain hidden from the public header files.
# Limiting component scope to exclude publically included files can reduce Undefined Reference linkage problems in large applications.
set(PRIV_REQUIRES
    app_update     # IDF components
    esp_event
    esp_netif
    storage_nvs    # Local project components
    control_heater
    control_leds
    display_oled
    sensors_adc
)   
#
#
#
idf_component_register(SRCS ${SOURCES}
                       INCLUDE_DIRS "include"
                       REQUIRES ${REQUIRES}
                       PRIV_REQUIRES ${PRIV_REQUIRES}
)
<example end>

1) Add these notes at the top to each CMakeLists.txt files.
2) Add the FILE statement as seen to capture all the src/*.cpp files
3) Add the REQUIRES and the PRIV_REQUIRES sections.  Do not include these example components.
4) Include the idf_component_register call.
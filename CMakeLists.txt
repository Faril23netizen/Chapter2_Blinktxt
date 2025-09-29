# Generated CMake Pico project file

cmake_minimum_required(VERSION 3.13)

# Set C and C++ standards
set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# == DO NOT EDIT THE FOLLOWING LINES for the Raspberry Pi Pico VS Code Extension to work ==
if(WIN32)
    set(USERHOME $ENV{USERPROFILE})
else()
    set(USERHOME $ENV{HOME})
endif()

set(sdkVersion 2.2.0)
set(toolchainVersion 14_2_Rel1)
set(picotoolVersion 2.2.0)
set(picoVscode ${USERHOME}/.pico-sdk/cmake/pico-vscode.cmake)

if (EXISTS ${picoVscode})
    include(${picoVscode})
endif()
# ====================================================================================

# Board configuration
set(PICO_BOARD pico CACHE STRING "Board type")

# Pull in Raspberry Pi Pico SDK (must be before project)
include(pico_sdk_import.cmake)

# Set project name
project(blink C CXX ASM)

# Disable default new/delete overrides from Pico SDK
add_compile_definitions(PICO_CXX_DISABLE_ALLOCATION_OVERRIDES)

# Initialize the Raspberry Pi Pico SDK
pico_sdk_init()

# Add FreeRTOS
set(FREERTOS_KERNEL_PATH "${CMAKE_CURRENT_LIST_DIR}/lib/FreeRTOS-Kernel" CACHE STRING "FreeRTOS path")
set(FREERTOS_CONFIG_FILE_DIRECTORY "${CMAKE_CURRENT_LIST_DIR}/port/FreeRTOS-Kernel" CACHE STRING "FreeRTOS config path")
include("${FREERTOS_CONFIG_FILE_DIRECTORY}/freertos_config.cmake")
include(FreeRTOS_Kernel_import.cmake)

# Add executable and source files
add_executable(blink
    src/main.cpp
    port/FreeRTOS-Kernel/cppMemory.cpp
)

# Include directories
target_include_directories(blink PRIVATE
    src
    port/FreeRTOS-Kernel
)

# Link libraries
target_link_libraries(blink 
    pico_stdlib
    FreeRTOS-Kernel-Heap4
    freertos_config
)

# Enable USB and UART output
pico_enable_stdio_usb(blink 1)
pico_enable_stdio_uart(blink 1)

# Generate additional outputs (bin/hex/uf2/map)
pico_add_extra_outputs(blink)

# Optional: deployment script
install(CODE "execute_process(COMMAND $ENV{HOME}/bin/picoDeploy.sh ${CMAKE_CURRENT_BINARY_DIR}/blink.elf)")

# Package the uf2 file
install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/blink.uf2
    DESTINATION ${CMAKE_CURRENT_BINARY_DIR}
)

# CPack settings
set(CPACK_INCLUDE_TOPLEVEL_DIRECTORY OFF)
set(CPACK_GENERATOR "ZIP" "TGZ")
include(CPack)

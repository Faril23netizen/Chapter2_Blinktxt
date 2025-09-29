add_executable(${NAME}
    main.cpp
)

# Pull in our pico_stdlib which pulls in commonly used features
target_link_libraries(${NAME}
	pico_stdlib
    FreeRTOS-Kernel-Heap4
    freertos_config 
)

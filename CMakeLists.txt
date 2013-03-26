cmake_minimum_required(VERSION 2.8)
project(usbdmx)

set(SRC usbdmx.c)
set(HEADER usbdmx.h)

if(NOT HAS_MZ_GLOBAL)
    include(global.cmake)
endif()

# library
add_library(usbdmx SHARED ${SRC} ${HEADER})
set_property(TARGET usbdmx PROPERTY VERSION 0.1~e4405ac337git)
set_property(TARGET usbdmx PROPERTY SOVERSION 0.1~e4405ac337git)
add_library(usbdmx-static STATIC ${SRC} ${HEADER})
set_property(TARGET usbdmx-static PROPERTY VERSION 0.1~e4405ac337git)
set_property(TARGET usbdmx-static PROPERTY SOVERSION 0.1~e4405ac337git)
target_link_libraries(usbdmx hidapi pthread)
target_link_libraries(usbdmx-static hidapi pthread)
mz_target_props(usbdmx)
mz_target_props(usbdmx-static)

# examples
mz_use_default_compiler_settings()

add_executable(simple_example WIN32 simple_example.c)
target_link_libraries(simple_example usbdmx)
mz_target_props(simple_example)

add_executable(usbdmx_example WIN32 usbdmx_example.cpp)
target_link_libraries(usbdmx_example usbdmx)
mz_target_props(usbdmx_example)


if(WIN32)
    install(TARGETS usbdmx
                LIBRARY DESTINATION lib
                RUNTIME DESTINATION bin
                ARCHIVE DESTINATION lib)
    install(TARGETS usbdmx-static
                LIBRARY DESTINATION lib
                RUNTIME DESTINATION bin
                ARCHIVE DESTINATION lib)
    install(FILES usbdmx.h
                DESTINATION include
                PATTERN "*.svn" EXCLUDE)
    install(FILES usbdmx_example.cpp simple_example.c
                DESTINATION examples)
else()
    install(TARGETS usbdmx
                LIBRARY DESTINATION lib
                RUNTIME DESTINATION bin
                ARCHIVE DESTINATION lib)
    install(TARGETS usbdmx-static
                LIBRARY DESTINATION lib
                RUNTIME DESTINATION bin
                ARCHIVE DESTINATION lib)
    install(FILES usbdmx.h
                DESTINATION include)
    if(APPLE)
        configure_file(${CMAKE_CURRENT_SOURCE_DIR}/libusbdmx.pc.mac.in 
                ${CMAKE_CURRENT_BINARY_DIR}/libusbdmx.pc @ONLY)
        install(FILES usbdmx_example.cpp simple_example.c
                DESTINATION Library/Documentation/usbdmx/examples)
        install(FILES ${CMAKE_CURRENT_BINARY_DIR}/libusbdmx.pc
                DESTINATION pkgconfig)
    else()
        configure_file(${CMAKE_CURRENT_SOURCE_DIR}/libusbdmx.pc.linux.in  
                ${CMAKE_CURRENT_BINARY_DIR}/libusbdmx.pc @ONLY)
        install(FILES usbdmx_example.cpp simple_example.c
                DESTINATION share/doc/usbdmx-doc/examples)
        install(FILES 50-usbdmx.rules
                DESTINATION /etc/udev/rules.d)
        install(FILES ${CMAKE_CURRENT_BINARY_DIR}/libusbdmx.pc
                DESTINATION share/pkgconfig)
    endif()
endif()

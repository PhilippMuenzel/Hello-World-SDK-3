cmake_minimum_required (VERSION 3.0)
project(HelloWorld)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_VERBOSE_MAKEFILE ON)

if(UNIX AND NOT APPLE)
set(LINUX TRUE)
endif()

if(UNIX)
    set(PLATFORM_COMPILE_OPTIONS -Wall -Wextra -Wfloat-equal -pedantic)
else()
    set(PLATFORM_COMPILE_OPTIONS /W3 /wd4996)
endif()

if(APPLE)
    set(CMAKE_OSX_DEPLOYMENT_TARGET 10.12)
    set(PLATFORM_CORE_DEFINITIONS
        APL=1
        LIN=0
        IBM=0)
else()
if(LINUX)
    set(PLATFORM_CORE_DEFINITIONS
        APL=0
        LIN=1
        IBM=0)
else()
    set(PLATFORM_CORE_DEFINITIONS
        APL=0
        LIN=0
        IBM=1)
    set(PLATFORM_DEBUG_OPTIONS "-MTd")
    set(PLATFORM_RELEASE_OPTIONS "-MT")
endif()
endif()

set(DEFINITIONS
    XPLM200=1
    XPLM210=1
    XPLM300=1
    XPLM301=1
    ${PLATFORM_CORE_DEFINITIONS})

include_directories(${CMAKE_SOURCE_DIR}/SDK/CHeaders/XPLM
                    ${CMAKE_SOURCE_DIR}/SDK/CHeaders/Widgets
                    ${PLATFORM_INCLUDE_DIRECTORIES})


set(SOURCES
    Hello-World-SDK-3.cpp)


add_library(Hello-World MODULE ${SOURCES})

target_compile_options(Hello-World PRIVATE ${PLATFORM_COMPILE_OPTIONS})
target_compile_options(Hello-World PUBLIC "$<$<CONFIG:DEBUG>:${PLATFORM_DEBUG_OPTIONS}>")
target_compile_options(Hello-World PUBLIC "$<$<CONFIG:RELEASE>:${PLATFORM_RELEASE_OPTIONS}>")
target_compile_definitions(Hello-World PRIVATE ${DEFINITIONS})

if (APPLE)
    target_link_libraries(Hello-World "-framework OpenGL")
    target_link_libraries(Hello-World "-framework CoreFoundation")
    target_link_libraries(Hello-World "-framework XPWidgets")
    target_link_libraries(Hello-World "-framework XPLM")
    set_target_properties(Hello-World PROPERTIES LINK_FLAGS "-Wl,-F/Library/Frameworks, -Wl,-FSDK/Libraries/Mac\
    -Wl,-exported_symbol -Wl,_XPluginStart\
    -Wl,-exported_symbol -Wl,_XPluginEnable\
    -Wl,-exported_symbol -Wl,_XPluginReceiveMessage\
    -Wl,-exported_symbol -Wl,_XPluginDisable\
    -Wl,-exported_symbol -Wl,_XPluginStop")
else()
if(LINUX)
    target_compile_options(Hello-World PRIVATE "-fvisibility=hidden")
else()
    target_link_libraries(Hello-World "SDK/Libraries/Win/XPWidgets_64")
    target_link_libraries(Hello-World "SDK/Libraries/Win/XPLM_64")
endif()
endif()

set_target_properties(Hello-World PROPERTIES PREFIX "")
set_target_properties(Hello-World PROPERTIES SUFFIX ".xpl")


cmake_minimum_required (VERSION 3.8)

project ("DRFL examples")

set(OS "ubuntu")
if (MSVC)
    set(OS "win64")
endif()

list(INSERT CMAKE_MODULE_PATH 0 "${PROJECT_SOURCE_DIR}/cmake/modules")
find_package(print_include_directories)

set(DRFL_LIBRARY_DIRECTORY ${CMAKE_SOURCE_DIR}/lib/arch/${OS})
include_directories(${CMAKE_SOURCE_DIR}/include/)
link_directories(${DRFL_LIBRARY_DIRECTORY})

print_include_directories()

message("OS: ${OS}")
message("CMAKE_BINARY_DIR: ${CMAKE_BINARY_DIR}")
message("DRFL_LIBRARY_DIRECTORY: ${DRFL_LIBRARY_DIRECTORY}")

if(CMAKE_BUILD_TYPE MATCHES DEBUG)
    message("debug mode")
endif()

add_executable(doosan_robot_demo "${PROJECT_SOURCE_DIR}/src/arch/${OS}/main.cpp")

if ( MSVC )
# if (${OS} == "win32")
#     target_link_libraries( doosan_robot_demo DRFLWin32 PocoFoundation PocoNet)
# endif()
# if (${OS} == "win64")
#     target_link_libraries( doosan_robot_demo DRFLWin64 PocoFoundation PocoNet)
# endif()

target_link_libraries( doosan_robot_demo DRFLWin64 PocoFoundation PocoNet)
set_target_properties( doosan_robot_demo PROPERTIES RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}" )
set_target_properties( doosan_robot_demo PROPERTIES RUNTIME_OUTPUT_DIRECTORY_DEBUG "${CMAKE_BINARY_DIR}" )
set_target_properties( doosan_robot_demo PROPERTIES RUNTIME_OUTPUT_DIRECTORY_RELEASE "${CMAKE_BINARY_DIR}" )

file(GLOB DRLF_DLL_FILES ${DRFL_LIBRARY_DIRECTORY}/*.dll)
foreach( dll_file ${DRLF_DLL_FILES})
    message(STATUS ${dll_file})
    add_custom_command(TARGET doosan_robot_demo
                       POST_BUILD
                       COMMAND ${CMAKE_COMMAND}
                       ARGS -E copy ${dll_file} "${CMAKE_BINARY_DIR}")
endforeach()

endif ( MSVC )


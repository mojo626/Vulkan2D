cmake_minimum_required(VERSION 3.4...3.28 FATAL_ERROR)

project(Vulkan2D VERSION 0.1.0)

add_subdirectory(lib/glfw)
add_subdirectory(lib/glm)

find_package(Vulkan REQUIRED)



add_library(${PROJECT_NAME} STATIC)


target_sources(${PROJECT_NAME} PRIVATE src/Window.cpp)

target_link_libraries(${PROJECT_NAME} PRIVATE glfw glm)
if (VULKAN_FOUND)
    message(STATUS "Found Vulkan,     Including and Linking now")
    include_directories(${Vulkan_INCLUDE_DIRS})
    target_link_libraries(${PROJECT_NAME} PRIVATE Vulkan::Vulkan)
endif (VULKAN_FOUND)

target_include_directories(${PROJECT_NAME}
    PRIVATE
        # where the library itself will look for its internal headers
        ${CMAKE_CURRENT_SOURCE_DIR}/src
    PUBLIC
        # where top-level project will look for the library's public headers
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        # where external projects will look for the library's public headers
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)


# note that it is not CMAKE_INSTALL_PREFIX we are checking here
if(DEFINED CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
    message(
        STATUS
        "CMAKE_INSTALL_PREFIX is not set\n"
        "Default value: ${CMAKE_INSTALL_PREFIX}\n"
        "Will set it to ${CMAKE_SOURCE_DIR}/install"
    )
    set(CMAKE_INSTALL_PREFIX
        "${CMAKE_SOURCE_DIR}/install"
        CACHE PATH "Where the library will be installed to" FORCE
    )
else()
    message(
        STATUS
        "CMAKE_INSTALL_PREFIX was already set\n"
        "Current value: ${CMAKE_INSTALL_PREFIX}"
    )
endif()


# without it public headers won't get installed
set(public_headers
    include/Window.h
)
# note that ${public_headers} has to be in quotes
set_target_properties(${PROJECT_NAME} PROPERTIES PUBLIC_HEADER "${public_headers}")
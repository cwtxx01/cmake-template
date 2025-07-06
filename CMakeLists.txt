cmake_minimum_required(VERSION 3.25.0)

project(project-name LANGUAGES CXX VERSION 1.0.0)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# c++ standard
set(CMAKE_CXX_STANDARD 20)

set(CMAKE_CXX_STANDARD_REQUIRED ON)

include(GNUInstallDirs)

# interface
add_library(project-name_iface_lib INTERFACE)

target_include_directories(project-name_iface_lib
    INTERFACE $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    INTERFACE $<INSTALL_INTERFACE:include>
)

if(MSVC)
    target_compile_definitions(project-name_iface_lib INTERFACE _WIN32_WINNT=0x0A00)

    target_compile_options(project-name_iface_lib INTERFACE /W4 /utf-8)
else()
    target_compile_options(project-name_iface_lib
        INTERFACE -Wall,
        INTERFACE -Wextra
    )
endif()

# 3rd party


# source
add_subdirectory(source)

# install
install(TARGETS project-name_iface_lib
    EXPORT project-nameTargets
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMKAE_INSTALL_LIBDIR}
)

install(DIRECTORY include/
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

install(EXPORT project-nameTargets
    FILE project-nameTargets.cmake
    NAMESPACE project-name::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/project-name
)

# package config
include(CMakePackageConfigHelpers)

# project-nameConfig.cmake.in : 
# @PACKAGE_INIT@
# include("${CMAKE_CURRENT_LIST_DIR}/project-nameTargets.cmake")
# check_required_component(project-name)
configure_package_config_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/cmake/project-nameConfig.cmake.in
    "${CMAKE_CURRENT_BINARY_DIR}/project-nameConfig.cmake"
    INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/project-name
)

write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/project-nameConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion
)

install(FILES 
    ${CMAKE_CURRENT_BINARY_DIR}/project-nameConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/project-nameConfigVersion.cmake
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/project-name
)

# package
set(CMAKE_PACKAGE_NAME "project-name")

set(CMAKE_PACKAGE_VERSION "${PROJECT_VERSION}")

if(WIN32)
    set(CPACK_GENERATOR "ZIP")
else()
    set(CPACK_GENERATOR "TGZ")
endif()

include(CPack)
cmake_minimum_required(VERSION 3.10...3.27)

#set project name
project(
    piolib
    VERSION 1.0.0
    LANGUAGES C)

include(GNUInstallDirs)

add_subdirectory(examples)

add_compile_definitions(LIBRARY_BUILD=1)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wextra -Werror")

if (CMAKE_COMPILER_IS_GNUCC)
   add_definitions (-ffunction-sections)
endif ()

add_library (pio STATIC piolib.c library_piochips.c pio_rp1.c)
target_include_directories(pio PUBLIC include)

set(INCLUDE_FILES
    "piolib.h"
    "pio_platform.h"
    "hardware/clocks.h"
    "hardware/gpio.h"
    "hardware/pio.h"
    "hardware/pio_instructions.h"
    "hardware/timer.h"
    "pico/stdlib.h"
)

install(TARGETS pio ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR} COMPONENT piolib)
foreach ( file ${INCLUDE_FILES} )
    get_filename_component( dir ${file} DIRECTORY )
    install( FILES "include/${file}" DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}/${dir}" COMPONENT piolib)
endforeach()

include(CMakePackageConfigHelpers)

configure_package_config_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/Config.cmake.in
  "${CMAKE_CURRENT_BINARY_DIR}/piolibConfig.cmake"
  INSTALL_DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/piolib
)

# generate the version file for the config file
write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/piolibConfigVersion.cmake"
  VERSION "${version}"
  COMPATIBILITY AnyNewerVersion
)

### Install Config and ConfigVersion files
install(
  FILES "${CMAKE_CURRENT_BINARY_DIR}/piolibConfig.cmake"
        "${CMAKE_CURRENT_BINARY_DIR}/piolibConfigVersion.cmake"
  DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/piolib"
)

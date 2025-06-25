cmake_minimum_required(VERSION 3.10)
project(opusenc C)

find_package(Opus REQUIRED)

execute_process(
    COMMAND git describe --tags --match "v*" --dirty
    WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
    OUTPUT_VARIABLE GIT_HASH
    OUTPUT_STRIP_TRAILING_WHITESPACE
)
message(STATUS "PACKAGE_VERSION: ${GIT_HASH}")
file(WRITE ${CMAKE_BINARY_DIR}/include/version.h "#define PACKAGE_VERSION \"${GIT_HASH}\"")

add_library(opusenc)
target_compile_definitions(opusenc PRIVATE
    $<$<PLATFORM_ID:Windows>:_LIB>
    $<$<PLATFORM_ID:Windows>:_UNICODE>
    $<$<PLATFORM_ID:Windows>:UNICODE>
    $<$<PLATFORM_ID:Windows>:_CRT_SECURE_NO_WARNINGS>
    $<$<PLATFORM_ID:Windows>:RANDOM_PREFIX=libopusenc>
    $<$<PLATFORM_ID:Windows>:OUTSIDE_SPEEX>
    $<$<PLATFORM_ID:Windows>:HAVE_CONFIG_H>
    $<$<PLATFORM_ID:Windows>:WIN32>
    $<$<BOOL:${BUILD_SHARED_LIBS}>:DLL_EXPORT>
)
target_include_directories(opusenc PUBLIC
    $<$<PLATFORM_ID:Windows>:${CMAKE_CURRENT_SOURCE_DIR}/win32>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)
target_sources(opusenc PRIVATE
    include/opusenc.h
    src/arch.h
    src/ogg_packer.c
    src/ogg_packer.h
    src/opus_header.c
    src/opus_header.h
    src/opusenc.c
    src/picture.c
    src/picture.h
    src/resample_sse.h
    src/resample.c
    src/speex_resampler.h
    src/unicode_support.c
    src/unicode_support.h
)
target_link_libraries(opusenc PUBLIC Opus::opus)

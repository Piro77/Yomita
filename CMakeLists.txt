project (Yomita C CXX)
set (CMAKE_VERBOSE_MAKEFILE on)

# Tweak build mode
if(NOT CMAKE_BUILD_TYPE)
        set(CMAKE_BUILD_TYPE "Debug" CACHE STRING "" FORCE)
endif()

if(NOT CMAKE_BUILD_TYPE MATCHES "^[Dd][Ee][Bb][Uu][Gg]")
        set(CMAKE_BUILD_RELEASE TRUE)
else()
        set(CMAKE_BUILD_RELEASE FALSE)
        add_definitions(-D_DEBUG)
endif()

message(STATUS "Building ${PROJECT_NAME} in ${CMAKE_BUILD_TYPE} mode")

cmake_minimum_required(VERSION 3.0.2)
set (CMAKE_VERBOSE_MAKEFILE on)

ENABLE_TESTING()

# Basic build options

find_package (Threads)

add_definitions(-DUSE_MAKEFILE)
add_definitions(-D_LINUX)

IF(${CMAKE_SYSTEM_NAME} MATCHES "FreeBSD")
  set (CMAKE_CXX_FLAGS "-std=c++11 -stdlib=libc++")
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR MINGW OR APPLE)
  set (CMAKE_CXX_FLAGS "-std=c++14 -fno-exceptions -fno-rtti -Wextra -Ofast -MMD -MP -fopenmp -fpermissive")
endif()

include_directories ("${PROJECT_SOURCE_DIR}/source")

if (MINGW)
  ## static
  set (CMAKE_CXX_FLAGS "-std=c++11 -fno-exceptions -fno-rtti -Wextra -static -static-libgcc -static-libstdc++ -fno-strict-aliasing")
endif()

set (YOMITA_EDITION "YOMITA_UNIX")
## Yomita
file(GLOB_RECURSE YOMITA_HEADERS  yomita/yomita/src/*.h)
file(GLOB_RECURSE YOMITA_SRCS  yomita/yomita/src/*.cpp)
set(YOMITA_ALL_SRCS ${YOMITA_HEADERS} ${YOMITA_SRCS})

set(TARGET Yomita)

add_custom_target(avx2)
set(TARGET Yomita-avx2)
set(TARGET_OPT  "-DUSE_AVX2 -mbmi -mbmi2 -mavx2")
add_executable(${TARGET} ${YOMITA_ALL_SRCS})
set_target_properties(${TARGET} PROPERTIES COMPILE_FLAGS "${TARGET_OPT} -D${YOMITA_EDITION}")
target_link_libraries (${TARGET} ${CMAKE_THREAD_LIBS_INIT})
add_dependencies(avx2 ${TARGET})

add_custom_target(sse42)
set(TARGET Yomita-sse42)
set(TARGET_OPT  "-DUSE_SSE42 -msse4.2")
add_executable(${TARGET} ${YOMITA_ALL_SRCS})
set_target_properties(${TARGET} PROPERTIES COMPILE_FLAGS "${TARGET_OPT} -D${YOMITA_EDITION}")
target_link_libraries (${TARGET} ${CMAKE_THREAD_LIBS_INIT})
add_dependencies(sse42 ${TARGET})

add_custom_target(sse41)
set(TARGET Yomita-sse41)
set(TARGET_OPT  "-DUSE_SSE41 -msse4.1")
add_executable(${TARGET} ${YOMITA_ALL_SRCS})
set_target_properties(${TARGET} PROPERTIES COMPILE_FLAGS "${TARGET_OPT} -D${YOMITA_EDITION}")
target_link_libraries (${TARGET} ${CMAKE_THREAD_LIBS_INIT})
add_dependencies(sse41 ${TARGET})

add_custom_target(sse2)
set(TARGET Yomita-sse2)
set(TARGET_OPT  "-DUSE_SSE2 -msse2")
add_executable(${TARGET} ${YOMITA_ALL_SRCS})
set_target_properties(${TARGET} PROPERTIES COMPILE_FLAGS "${TARGET_OPT} -D${YOMITA_EDITION}")
target_link_libraries (${TARGET} ${CMAKE_THREAD_LIBS_INIT})
add_dependencies(sse2 ${TARGET})

add_custom_target(nosse)
set(TARGET Yomita-nosse)
set(TARGET_OPT  " ")
add_executable(${TARGET} ${YOMITA_ALL_SRCS})
set_target_properties(${TARGET} PROPERTIES COMPILE_FLAGS "${TARGET_OPT} -D${YOMITA_EDITION}")
target_link_libraries (${TARGET} ${CMAKE_THREAD_LIBS_INIT})
add_dependencies(nosse ${TARGET})

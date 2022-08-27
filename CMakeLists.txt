cmake_minimum_required(VERSION 3.22.0)
project(psim C)

set(SDL_SHARED OFF)
set(SDL_STATIC ON)
add_subdirectory(vendor/SDL2)

find_package(OpenGL REQUIRED)

file(GLOB_RECURSE PSIM_SOURCES
  ${PROJECT_SOURCE_DIR}/src/*.c
)

file(GLOB_RECURSE PSIM_HEADERS
  ${PROJECT_SOURCE_DIR}/include/*.h
)

add_executable(psim ${PSIM_HEADERS} ${PSIM_SOURCES})

if(WIN32)
  target_link_libraries(psim
    mingw32
    SDL2main
    SDL2-static
    opengl32
    glu32
  )
elseif(APPLE)
  target_link_libraries(psim
    SDL2-static
    opengl
    glu
  )
elseif(UNIX)
  target_link_libraries(psim
    SDL2-static
    opengl
    glu
  )
else()
  message(FATAL_ERROR "Platform not supported")
endif()

target_include_directories(psim
  PRIVATE "${PROJECT_SOURCE_DIR}/include"
  PRIVATE "${PROJECT_SOURCE_DIR}/vendor/SDL2/include"
)

if(WIN32)
  target_link_options(psim PUBLIC
    -Wl,-subsystem,console
  )
endif()

if(CMAKE_BUILD_TYPE STREQUAL "Debug")
  target_compile_definitions(psim PUBLIC
    DEBUG
  )

  target_compile_options(psim PUBLIC
    -g -Wall -Werror
  )
else()
  target_compile_options(psim PUBLIC
    -O2 -Wall -Werror
  )
endif()

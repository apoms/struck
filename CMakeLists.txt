cmake_minimum_required(VERSION 3.2)

project(Struck)

if (NOT CMAKE_BUILD_TYPE)
    message(STATUS "No build type selected, defaulting to Release")
    set(CMAKE_BUILD_TYPE "Release")
endif()

set(CMAKE_MODULE_PATH ${CMAKE_HOME_DIRECTORY}/cmake ${CMAKE_MODULE_PATH})

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)

find_package(Eigen REQUIRED)
find_package(OpenCV REQUIRED)

include_directories(
    src
    ${EIGEN_INCLUDE_DIRS}
    ${OpenCV_INCLUDE_DIRS}
)

# add all .cpp and .h files in src
file(GLOB_RECURSE SRC RELATIVE ${PROJECT_SOURCE_DIR} src/*.cpp)
file(GLOB_RECURSE HEADERS RELATIVE ${PROJECT_SOURCE_DIR} src/*.h)
foreach(item ${SRC})
  if(${item} MATCHES "src/main.cpp")
    LIST(REMOVE_ITEM SRC ${item})
  endif()
endforeach()

add_library(struck ${HEADERS} ${SRC})

add_executable(struck_example
  src/main.cpp)

target_link_libraries(struck
  ${OpenCV_LIBS})

target_link_libraries(struck_example
  struck
  ${OpenCV_LIBS})

install(TARGETS struck
  EXPORT StruckTarget
  ARCHIVE DESTINATION lib)

set(STRUCK_LIBRARY "${CMAKE_INSTALL_PREFIX}/lib/libstruck.a")
set(STRUCK_LIBRARIES ${CUSTOM_LIBRARIES})

configure_file(cmake/StruckConfig.cmake.in
  "${PROJECT_BINARY_DIR}/cmake/StruckConfig.cmake" @ONLY)

install(FILES "${PROJECT_BINARY_DIR}/cmake/StruckConfig.cmake"
  DESTINATION cmake)

install(EXPORT StruckTarget
  DESTINATION cmake)

install(DIRECTORY src/ DESTINATION include/struck
        FILES_MATCHING PATTERN "*.h")

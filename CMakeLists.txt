CMAKE_MINIMUM_REQUIRED(VERSION 3.0 FATAL_ERROR)
if (COMMAND cmake_policy)
    cmake_policy(SET CMP0003 NEW)
endif (COMMAND cmake_policy)

project(CNPY)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wconversion -Werror")

find_package(ZLIB REQUIRED)


add_library(cnpy SHARED cnpy.cpp)
target_include_directories(cnpy PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        ${ZLIB_INCLUDE_DIRS}
        $<INSTALL_INTERFACE:include>)
set_target_properties(cnpy PROPERTIES PUBLIC_HEADER include/cnpy/cnpy.h)
target_link_libraries(cnpy ${ZLIB_LIBRARIES})

add_executable(example1 example1.cpp)
target_link_libraries(example1 cnpy)

install(TARGETS cnpy EXPORT cnpyTargets
        LIBRARY DESTINATION lib
        PUBLIC_HEADER DESTINATION include/cnpy)

install(FILES mat2npz npy2mat npz2mat DESTINATION bin)

set(ConfigPackageLocation lib/cmake/cnpy)

export(EXPORT cnpyTargets
        FILE "${CMAKE_CURRENT_BINARY_DIR}/cnpy/cnpyTargets.cmake"
        NAMESPACE cnpy::
        )

configure_file(cmake/cnpy-config.cmake
        "${CMAKE_CURRENT_BINARY_DIR}/cnpy-config.cmake"
        COPYONLY
        )

install(EXPORT cnpyTargets
        FILE
        cnpy-targets.cmake
        NAMESPACE
        cnpy::
        DESTINATION
        ${ConfigPackageLocation}
        )

install(
        FILES
        cmake/cnpy-config.cmake
        DESTINATION
        ${ConfigPackageLocation}
)

cmake_minimum_required(VERSION 3.10)

project(Active_Suspension_DEMO)

SET(PROJECT_LIB_DIR "${PROJECT_SOURCE_DIR}/lib/$<CONFIGURATION>")
SET(PROJECT_BIN_DIR "${PROJECT_SOURCE_DIR}/bin/$<CONFIGURATION>")
set(CMAKE_CXX_FLAGS "/O2 /Ot")

SET(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${PROJECT_SOURCE_DIR}/lib")
SET(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${PROJECT_SOURCE_DIR}/lib")
SET(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${PROJECT_SOURCE_DIR}/bin")
SET(CMAKE_BINARY_DIR "${PROJECT_SOURCE_DIR}/../bin")

set(Chrono_DIR "C:/Users/jkimball/Documents/Chrono/chrono_build/cmake")

LIST(APPEND ${CHRONO_CXX_FLAGS} "/O2")
LIST(APPEND ${CHRONO_CXX_FLAGS} "/Ot")
LIST(APPEND ${CHRONO_CXX_FLAGS} "/Ob2")

LIST(APPEND CMAKE_PREFIX_PATH "${CMAKE_INSTALL_PREFIX}/../Chrono/lib")
find_package(Chrono
             COMPONENTS Irrlicht MATLAB Vehicle Multicore OpenGL
             CONFIG)

if (NOT Chrono_FOUND)
  message("Could not find Chrono or one of its required modules")
  return()
endif()

if(APPLE)
    set(CMAKE_MACOSX_BUNDLE ON)
endif()

if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
  if(MSVC AND ${MSVC_VERSION} GREATER_EQUAL 1915)
    add_definitions( "-D_ENABLE_EXTENDED_ALIGNED_STORAGE" )
  endif()
endif()

#--------------------------------------------------------------
# Tweaks to disable some warnings with MSVC
#--------------------------------------------------------------
if(MSVC)
    add_definitions("-D_CRT_SECURE_NO_DEPRECATE")  # avoids deprecation warnings
    add_definitions("-D_SCL_SECURE_NO_DEPRECATE")  # avoids deprecation warnings
    add_definitions( "-DNOMINMAX" )                # do not use MSVC's min/max macros
    add_compile_options(/wd4275)                   # disable warnings triggered by Irrlicht
    add_compile_options(/wd4251)                   # class needs to have dll-interface
endif()

#--------------------------------------------------------------
# === 4 === Test the library
#--------------------------------------------------------------
LIST(APPEND Active_Suspension_DEMO_src "src/main.cpp")

# Set the include directories
include_directories(Active_Suspension_DEMO ${CHRONO_INCLUDE_DIRS})

# Add the exe
add_executable(Active_Suspension_DEMO ${Active_Suspension_DEMO_src})

# Add the library dependencies
target_link_libraries(Active_Suspension_DEMO ${CHRONO_LIBRARIES})

target_compile_options(Active_Suspension_DEMO PUBLIC ${CHRONO_CXX_FLAGS})
target_link_options(Active_Suspension_DEMO PUBLIC ${CHRONO_LINKER_FLAGS})

# Compile with C++17
target_compile_features(Active_Suspension_DEMO PRIVATE cxx_std_17)

# Print information
message(STATUS "Linker Flags" ${CHRONO_LINKER_FLAGS})
message(STATUS "CXX Flags" ${CHRONO_CXX_FLAGS})
message(STATUS ${CHRONO_LIBRARIES})

#--------------------------------------------------------------
# === 4 (OPTIONAL) ===
# 
# Optionally, add a custom command for copying all Chrono and
# dependency DLLs to the appropriate binary output folder.
# This function has effect only on Windows.
# 
# Note that you must first set EXECUTABLE_OUTPUT_PATH
# (this can simply be ${CMAKE_BINARY_DIR}, like in this example)
#--------------------------------------------------------------

message(STATUS ${CHRONO_DLL_NAMES})

set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR})
add_DLL_copy_command()


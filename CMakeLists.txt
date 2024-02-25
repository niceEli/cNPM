cmake_minimum_required(VERSION 3.8)

# Enable Hot Reload for MSVC compilers if supported.
if(POLICY CMP0141)
    cmake_policy(SET CMP0141 NEW)
    set(CMAKE_MSVC_DEBUG_INFORMATION_FORMAT "$<IF:$<AND:$<C_COMPILER_ID:MSVC>,$<CXX_COMPILER_ID:MSVC>>,$<$<CONFIG:Debug,RelWithDebInfo>:EditAndContinue>,$<$<CONFIG:Debug,RelWithDebInfo>:ProgramDatabase>>")
endif()

project("cNPM")

# Add source to this project's executable.
add_executable(${PROJECT_NAME} "src/main.cpp" "src/main.h" "src/otherFle.cpp" "src/otherFle.h")

if(CMAKE_VERSION VERSION_GREATER 3.12)
    set_property(TARGET ${PROJECT_NAME} PROPERTY CXX_STANDARD 20)
endif()

# Find all subdirectories in node_modules
file(GLOB NODE_MODULES_DIRS "${CMAKE_CURRENT_SOURCE_DIR}/node_modules/*")

# Loop through each directory
foreach(NODE_MODULES_DIR ${NODE_MODULES_DIRS})
    if(IS_DIRECTORY ${NODE_MODULES_DIR})
        # Look for a CMakeLists.txt file in the directory
        file(GLOB CMAKE_FILES "${NODE_MODULES_DIR}/CMakeLists.txt")
        if(CMAKE_FILES)
            # Assuming each package in node_modules has a CMakeLists.txt file, add it as a subdirectory
            message(STATUS "Adding package in ${NODE_MODULES_DIR}")
            add_subdirectory(${NODE_MODULES_DIR})
            # Extract the library name from the directory name (assuming it's the last part after the last '/')
            string(REGEX MATCH "[^/]+$" LIB_NAME ${NODE_MODULES_DIR})
            # Add the library to the target_link_libraries
            target_link_libraries(${PROJECT_NAME} PRIVATE ${LIB_NAME})
        endif()
    endif()
endforeach()

# Add node_modules directory to the include path
set(CMAKE_INCLUDE_PATH ${CMAKE_INCLUDE_PATH} "${CMAKE_SOURCE_DIR}/node_modules")

# TODO: Add tests and install targets if needed.

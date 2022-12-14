cmake_minimum_required(VERSION 3.1)
project(ereignis LANGUAGES CXX VERSION 1.0)

# --------------------------------------------------------------------------------------------------------
# Library options
# --------------------------------------------------------------------------------------------------------

option(ereignis_tests "Run tests" OFF)

# --------------------------------------------------------------------------------------------------------
# Create library
# --------------------------------------------------------------------------------------------------------

add_library(${PROJECT_NAME} INTERFACE)
add_library(soundux::${PROJECT_NAME} ALIAS ${PROJECT_NAME})

target_compile_features(${PROJECT_NAME} INTERFACE cxx_std_17)
set_target_properties(${PROJECT_NAME} PROPERTIES CXX_STANDARD 17 CXX_EXTENSIONS OFF CXX_STANDARD_REQUIRED ON)

# --------------------------------------------------------------------------------------------------------
# Include "include" folder
# --------------------------------------------------------------------------------------------------------

target_include_directories(${PROJECT_NAME} INTERFACE "include")

# --------------------------------------------------------------------------------------------------------
# Build Tests
# --------------------------------------------------------------------------------------------------------
    
if (ereignis_tests)
    message(STATUS "Building ereignis tests")

    if (NOT TARGET Catch2::Catch2WithMain)
        include(FetchContent)
        FetchContent_Declare(Catch2 GIT_REPOSITORY "https://github.com/catchorg/Catch2" GIT_TAG v2.13.7)
        option(CATCH_BUILD_STATIC_LIBRARY "" ON)
        FetchContent_MakeAvailable(Catch2)
    endif()

    file(GLOB src "tests/*.cpp")
    add_executable(${PROJECT_NAME}_tests ${src})
    target_link_libraries(${PROJECT_NAME}_tests PRIVATE Catch2::Catch2WithMain ${PROJECT_NAME})
    
    if (UNIX)
        target_link_libraries(${PROJECT_NAME}_tests PRIVATE pthread)
        target_compile_options(${PROJECT_NAME}_tests INTERFACE -Wall -Werror -Wextra -pedantic)
    endif()
    
    target_compile_features(${PROJECT_NAME}_tests INTERFACE cxx_std_17)
    set_target_properties(${PROJECT_NAME}_tests PROPERTIES CXX_STANDARD 17 CXX_EXTENSIONS OFF CXX_STANDARD_REQUIRED ON)
endif()

# --------------------------------------------------------------------------------------------------------
# Install Targets
# --------------------------------------------------------------------------------------------------------

install(DIRECTORY "include/" TYPE INCLUDE)
install(TARGETS ${PROJECT_NAME} INCLUDES DESTINATION include)
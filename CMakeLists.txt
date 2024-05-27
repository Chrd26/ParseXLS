cmake_minimum_required(VERSION 3.26)
project("XLS_Parser" VERSION 0.1 DESCRIPTION "Made with C++ and Love")
set(CMAKE_CXX_STANDARD 23)
set(OpenXLSX_DIR "${PROJECT_SOURCE_DIR}/modules/OpenXLSX/build/OpenXLSX/OpenXLSX/")

# Handle Submodules: https://cliutils.gitlab.io/modern-cmake/chapters/projects/submodule.html 
find_package(Git QUIET)
if(GIT_FOUND AND EXISTS "${PROJECT_SOURCE_DIR}/.git")
# Update submodules as needed
    option(GIT_SUBMODULE "Check submodules during build" ON)
    if(GIT_SUBMODULE)
        message(STATUS "Submodule update")
        execute_process(COMMAND ${GIT_EXECUTABLE} submodule update --init --recursive
                        WORKING_DIRECTORY "./modules/"
                        RESULT_VARIABLE GIT_SUBMOD_RESULT)
        if(NOT GIT_SUBMOD_RESULT EQUAL "0")
            message(FATAL_ERROR "git submodule update --init --recursive failed with ${GIT_SUBMOD_RESULT}, please checkout submodules")
        endif()
    endif()
endif()

# Has open xlsx been downloaded?
if(NOT EXISTS "${PROJECT_SOURCE_DIR}/modules/OpenXLSX/CMakeLists.txt")
    message(FATAL_ERROR "The submodules were not downloaded! GIT_SUBMODULE was turned off or failed. Please update submodules and try again.")
endif()

add_subdirectory("${PROJECT_SOURCE_DIR}/modules/OpenXLSX")
find_package(OpenXLSX REQUIRED CONFIG REQUIRED COMPONENTS OpenXLSX)

add_executable(${PROJECT_NAME} ./src/main.cpp)
target_link_libraries(${PROJECT_NAME} PRIVATE OpenXLSX::OpenXLSX)



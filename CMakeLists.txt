cmake_minimum_required(VERSION 3.13)
project(pyext)

set(CMAKE_CXX_STANDARD 14)

find_package(PythonInterp 3.5 REQUIRED)

# This goes after, since it uses PythonInterp as hint
find_package(PythonLibs 3.5 REQUIRED)
include_directories(${PYTHON_INCLUDE_DIRS})

exec_program(${PYTHON_EXECUTABLE}
        ARGS "-c \"import sys; print(':'.join([x for x in sys.path if x]))\""
        OUTPUT_VARIABLE PYTHON_SYS_PATH
        RETURN_VALUE PYTHON_NOT_FOUND
        )
if(PYTHON_NOT_FOUND)
    message(FATAL_ERROR "Python headers not found")
else()
    message(${PYTHON_SYS_PATH})
    message(${PYTHON_EXECUTABLE})
endif()

# This comes to hand if we also need to use the NumPy C API
exec_program(${PYTHON_EXECUTABLE}
        ARGS "-c \"import numpy; print(numpy.get_include())\""
        OUTPUT_VARIABLE NUMPY_INCLUDE_DIR
        RETURN_VALUE NUMPY_NOT_FOUND
        )
if(NUMPY_NOT_FOUND)
    message(FATAL_ERROR "NumPy headers not found")
endif()

add_executable(pyext_exe main.cpp)

add_library(pyext SHARED main.cpp)
target_link_libraries(pyext ${PYTHON_LIBRARIES})

set_target_properties(
        pyext
        PROPERTIES
        PREFIX ""
        OUTPUT_NAME "pyext"
        LINKER_LANGUAGE C
)
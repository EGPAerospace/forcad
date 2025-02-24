# CMake project file for FOO

##################################################
# Define the project and the depencies that it has
##################################################

CMAKE_MINIMUM_REQUIRED(VERSION 3.0)
PROJECT(FORCAD Fortran)

enable_language(Fortran CXX)

# Set the FOO version
SET(VERSION 0.0.1)

set(CMAKE_BUILD_TYPE Release) 

# Add our local modules to the module path
SET(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake/Modules/")

# Uncomment if it is required that Fortran 90 is supported
#IF(NOT CMAKE_Fortran_COMPILER_SUPPORTS_F90)
#    MESSAGE(FATAL_ERROR "Fortran compiler does not support F90")
#ENDIF(NOT CMAKE_Fortran_COMPILER_SUPPORTS_F90)

# Set some options the user may choose
# Uncomment the below if you want the user to choose a parallelization library
#OPTION(USE_MPI "Use the MPI library for parallelization" OFF)
#OPTION(USE_OPENMP "Use OpenMP for parallelization" OFF)

# This INCLUDE statement executes code that sets the compile flags for DEBUG,
# RELEASE, and TESTING.  You should  review this file and make sure the flags 
# are to your liking.
INCLUDE(${CMAKE_MODULE_PATH}/SetFortranFlags.cmake) 
# Locate and set parallelization libraries.  There are some CMake peculiarities
# taken care of here, such as the fact that the FindOpenMP routine doesn't know
# about Fortran.
#INCLUDE(${CMAKE_MODULE_PATH}/SetParallelizationLibrary.cmake)
# Setup the LAPACK libraries.  This also takes care of peculiarities, such as
# the fact the searching for MKL requires a C compiler, and that the results
# are not stored in the cache. 
#INCLUDE(${CMAKE_MODULE_PATH}/SetUpLAPACK.cmake)

# There is an error in CMAKE with this flag for pgf90.  Unset it
GET_FILENAME_COMPONENT(FCNAME ${CMAKE_Fortran_COMPILER} NAME)
IF(FCNAME STREQUAL "pgf90")
    UNSET(CMAKE_SHARED_LIBRARY_LINK_Fortran_FLAGS)
ENDIF(FCNAME STREQUAL "pgf90")


#######################
# Adding git submodules
#######################

find_package(Git QUIET)
if(GIT_FOUND AND EXISTS "${PROJECT_SOURCE_DIR}/.git")
# Update submodules as needed
    option(GIT_SUBMODULE "Check submodules during build" ON)
    if(GIT_SUBMODULE)
        message(STATUS "Submodule update")
        execute_process(COMMAND ${GIT_EXECUTABLE} submodule update --init --recursive
                        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
                        RESULT_VARIABLE GIT_SUBMOD_RESULT)
        if(NOT GIT_SUBMOD_RESULT EQUAL "0")
            message(FATAL_ERROR "git submodule update --init --recursive failed with ${GIT_SUBMOD_RESULT}, please checkout submodules")
        endif()
    endif()
endif()

if(NOT EXISTS "${PROJECT_SOURCE_DIR}/ext/stdlib/CMakeLists.txt")
    message(FATAL_ERROR "The stdlib submodule was not downloaded! GIT_SUBMODULE was turned off or failed. Please update submodules and try again.")
endif()




############################################################
# Define the actual files and folders that make up the build
############################################################

# Define the executable name
#SET(RADCOMPEXE RadComp)

# Define the library name
#SET(1DPERFORMANCELIB 1DPerformance)
#SET(GASPATHLIB GasPath)
SET(FORCADLIB forcad)

# Define some directories
#SET(EXTINCLUDE ${CMAKE_SOURCE_DIR}/ext/stdlib/src)
SET(EXTSTDLIB ${PROJECT_SOURCE_DIR}/ext/stdlib)
SET(SRC ${PROJECT_SOURCE_DIR}/src)
SET(LIB ${PROJECT_SOURCE_DIR}/lib)
SET(BIN ${PROJECT_SOURCE_DIR}/bin)
#SET(SRCRADCOMP ${SRC}/RadComp)
#SET(SRC1DPERFORMANCE ${SRC}/1DPerformance)
#SET(SRCGASPATH ${SRC}/GasPath)
#SET(SRCSIZING ${SRC}/Sizing)
#include_directories(${EXTINCLUDE})

# Have the .mod files placed in the lib folder
SET(CMAKE_Fortran_MODULE_DIRECTORY ${LIB})

# The source for the libraries and have it placed in the lib folder
ADD_SUBDIRECTORY(${EXTSTDLIB} ${LIB}/ext/stdlib)
#ADD_SUBDIRECTORY(${SRC1DPERFORMANCE} ${LIB}/1DPerformance)
#ADD_SUBDIRECTORY(${SRCGASPATH} ${LIB}/GasPath)
ADD_SUBDIRECTORY(${SRC} ${LIB})

# The source for the RadComp binary and have it placed in the bin folder
#ADD_SUBDIRECTORY(${SRCRADCOMP} ${BIN})

# Add a distclean target to the Makefile
ADD_CUSTOM_TARGET(distclean 
    COMMAND ${CMAKE_COMMAND} -P ${PROJECT_SOURCE_DIR}/distclean.cmake
)
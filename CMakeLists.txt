##---------------------------------------------------------------------------------------------------------------------
##  MICO TEMPLATE plugin
##---------------------------------------------------------------------------------------------------------------------
##  Copyright 2020 Pablo Ramon Soria (a.k.a. Bardo91) pabramsor@gmail.com & Ricardo Lopez Lopez (a.k.a Ric92) & Marco Montes Grova (a.k.a mgrova)
##---------------------------------------------------------------------------------------------------------------------
##  Permission is hereby granted, free of charge, to any person obtaining a copy of this software
##  and associated documentation files (the "Software"), to deal in the Software without restriction,
##  including without limitation the rights to use, copy, modify, merge, publish, distribute,
##  sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is
##  furnished to do so, subject to the following conditions:
##
##  The above copyright notice and this permission notice shall be included in all copies or substantial
##  portions of the Software.
##
##  THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING
##  BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
##  NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES
##  OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
##  CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
##---------------------------------------------------------------------------------------------------------------------

cmake_minimum_required (VERSION 3.12 FATAL_ERROR)
project(mico VERSION 1.0.3 LANGUAGES C CXX)
set(PLUGIN_NAME ${PROJECT_NAME}-template)

##################################################
###### Create project and configure ALIAS ########
##################################################

file(GLOB_RECURSE MICO_TEMPLATE_SOURCE_FILES "src/*.cpp" "src/*.c")
file(GLOB_RECURSE MICO_TEMPLATE_HEADER_FILES "include/*.h" "include/*.inl")
 
add_library(${PLUGIN_NAME} SHARED ${MICO_TEMPLATE_HEADER_FILES} ${MICO_TEMPLATE_SOURCE_FILES})
target_compile_features(${PLUGIN_NAME} PUBLIC cxx_std_17)
target_include_directories(${PLUGIN_NAME}
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)

add_library(${PROJECT_NAME}::${PLUGIN_NAME} ALIAS ${PLUGIN_NAME})

##################################################
######    Configure project               ########
##################################################



##################################################
######    Loading 3rd party libraries.    ########
##################################################
if( NOT CMAKE_BUILD_TYPE )
  set( CMAKE_BUILD_TYPE Release CACHE STRING
       "Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel."
       FORCE )
endif()

set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake/modules/" ${CMAKE_MODULE_PATH})

target_compile_options(${PLUGIN_NAME} PUBLIC "-Wall")

find_package(flow REQUIRED HINTS 	"/usr/local/lib/cmake/mico" 
									"C:\\Program Files\\mico-corp\\mico\\lib\\cmake")
if(${flow_FOUND})
  target_link_libraries(${PLUGIN_NAME}  LINK_PUBLIC flow::flow)
  target_compile_definitions(${PLUGIN_NAME} PUBLIC HAS_FLOW)
endif()


set(MICO_DEPS "core" "")
foreach(DEP ${MICO_DEPS})
  find_package(mico-${DEP} REQUIRED HINTS 	"/usr/local/lib/cmake/mico" 
											"C:\\Program Files\\mico-corp\\mico\\lib\\cmake")
  target_link_libraries(${PLUGIN_NAME} LINK_PRIVATE mico::mico-${DEP})
endforeach()

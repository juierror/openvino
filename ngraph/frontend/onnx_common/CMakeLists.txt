# Copyright (C) 2021 Intel Corporation
# SPDX-License-Identifier: Apache-2.0
#

set(TARGET_NAME "onnx_common")

file(GLOB_RECURSE LIBRARY_SRC ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)
file(GLOB_RECURSE PUBLIC_HEADERS ${CMAKE_CURRENT_SOURCE_DIR}/include/*.hpp)

# Create named folders for the sources within the .vcproj
# Empty name lists them directly under the .vcproj

source_group("src" FILES ${LIBRARY_SRC})
source_group("include" FILES ${PUBLIC_HEADERS} ${PUBLIC_HEADERS})

# Create static library

add_library(${TARGET_NAME} STATIC ${LIBRARY_SRC} ${PUBLIC_HEADERS})
add_library(ngraph::onnx_common ALIAS ${TARGET_NAME})

# TODO Add handling ie_faster_build

set(ONNX_COMMON_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/include)
set(ONNX_COMMON_SRC_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)
set(ONNX_COMMON_INSTALL_INCLUDE "${NGRAPH_INSTALL_INCLUDE}/ngraph/frontend")

target_include_directories(${TARGET_NAME} PUBLIC $<BUILD_INTERFACE:${ONNX_COMMON_INCLUDE_DIR}>
                                                 $<INSTALL_INTERFACE:${ONNX_COMMON_INSTALL_INCLUDE}>)

target_link_libraries(${TARGET_NAME} PRIVATE ngraph onnx_proto onnx ${Protobuf_LIBRARIES})

target_include_directories(${TARGET_NAME} PRIVATE ${ONNX_COMMON_SRC_DIR} ${NGRAPH_INCLUDE_PATH} ${Protobuf_INCLUDE_DIRS})

if(NGRAPH_USE_PROTOBUF_LITE)
    target_compile_definitions(${TARGET_NAME} PRIVATE NGRAPH_USE_PROTOBUF_LITE)
endif()

# ******************************************************************************
# Copyright 2017-2021 Intel Corporation
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
# ******************************************************************************

add_definitions("-DSERIALIZED_ZOO=\"${CMAKE_CURRENT_SOURCE_DIR}/models\"")
set(NGRAPH_ONNX_NAMESPACE ngraph_onnx)

add_subdirectory(runtime)

if(NOT NGRAPH_UNIT_TEST_ENABLE)
    message(STATUS "unit tests disabled")
    add_subdirectory(util)
    return()
endif()

message(STATUS "unit tests enabled")

if(LINUX)
    set(CMAKE_BUILD_WITH_INSTALL_RPATH FALSE)
endif()

if(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    if(CMAKE_CXX_COMPILER_VERSION VERSION_GREATER "4.0.0")
        # gtest has issues with this with v1.8.x
        # gtest issue is supposed to be addressed after v1.8.x
        add_compile_options(-Wno-zero-as-null-pointer-constant)
    endif()
endif()

set(SRC
    aligned_buffer.cpp
    all_close_f.cpp
    attributes.cpp
    bfloat16.cpp
    build_graph.cpp
    builder_autobroadcast.cpp
    check.cpp
    conditional_compilation/ngraph_cc_collect.cpp
    conditional_compilation/ngraph_cc_off.cpp
    conditional_compilation/ngraph_cc_on.cpp
    constant.cpp
    constant_folding.cpp
    control_dependencies.cpp
    convert_u1_to_string.cpp
    coordinate.cpp
    coordinate_range.cpp
    copy.cpp
    element_type.cpp
    eval.cpp
    file_util.cpp
    float16.cpp
    graph_rewrite.cpp
    includes.cpp
    input_output_assign.cpp
    intervals.cpp
    main.cpp
    matcher_pass.cpp
    misc.cpp
    ngraph_api.cpp
    node_input_output.cpp
    op.cpp
    op_eval/binary_convolution.cpp
    op_eval/bucketize.cpp
    op_eval/floor_mod.cpp
    op_eval/gelu.cpp
    op_eval/hsigmoid.cpp
    op_eval/hswish.cpp
    op_eval/interpolate.cpp
    op_eval/matmul.cpp
    op_eval/mish.cpp
    op_eval/non_zero.cpp
    op_eval/reduce_l1.cpp
    op_eval/reduce_l2.cpp
    op_eval/roi_align.cpp
    op_eval/roi_pooling.cpp
    op_eval/round.cpp
    op_eval/softplus.cpp
    op_eval/split.cpp
    op_eval/swish.cpp
    op_eval/strided_slice.cpp
    op_eval/variadic_split.cpp
    op_is.cpp
    opset1.cpp
    partial_shape.cpp
    pass_config.cpp
    pass_liveness.cpp
    pass_manager.cpp
    pass_shape_relevance.cpp
    pattern.cpp
    provenance.cpp
    replace_node.cpp
    shape.cpp
    specialize_function.cpp
    tensor.cpp
    type_prop/assign.cpp
    type_prop/avg_pool.cpp
    type_prop/batch_norm.cpp
    type_prop/batch_to_space.cpp
    type_prop/binary_convolution.cpp
    type_prop/binary_elementwise.cpp
    type_prop/broadcast.cpp
    type_prop/bucketize.cpp
    type_prop/clamp.cpp
    type_prop/concat.cpp
    type_prop/constant.cpp
    type_prop/convert.cpp
    type_prop/convolution.cpp
    type_prop/ctc_greedy_decoder.cpp
    type_prop/ctc_greedy_decoder_seq_len.cpp
    type_prop/ctc_loss.cpp
    type_prop/deformable_convolution.cpp
    type_prop/deformable_psroi_pooling.cpp
    type_prop/detection_output.cpp
    type_prop/depth_to_space.cpp
    type_prop/dyn_reshape.cpp
    type_prop/experimental_detectron_generate_proposals.cpp
    type_prop/experimental_detectron_roi_feature_extractor.cpp
    type_prop/experimental_detectron_topkrois.cpp
    type_prop/strided_slice.cpp
    type_prop/elu.cpp
    type_prop/embeddingbag_offsetssum.cpp
    type_prop/experimental_detectron_detection_output.cpp
    type_prop/experimental_detectron_prior_grid_generator.cpp
    type_prop/extractimagepatches.cpp
    type_prop/embeddingbag_packedsum.cpp
    type_prop/embedding_segments_sum.cpp
    type_prop/fake_quantize.cpp
    type_prop/gather.cpp
    type_prop/gather_elements.cpp
    type_prop/gather_nd.cpp
    type_prop/gather_tree.cpp
    type_prop/grn.cpp
    type_prop/group_convolution.cpp
    type_prop/group_convolution_backprop_data.cpp
    type_prop/gru_cell.cpp
    type_prop/gru_sequence.cpp
    type_prop/hard_sigmoid.cpp
    type_prop/hsigmoid.cpp
    type_prop/hswish.cpp
    type_prop/interpolate.cpp
    type_prop/lrn.cpp
    type_prop/lstm_cell.cpp
    type_prop/lstm_sequence.cpp
    type_prop/loop.cpp
    type_prop/matmul.cpp
    type_prop/max_pool.cpp
    type_prop/mish.cpp
    type_prop/mvn.cpp
    type_prop/non_max_suppression.cpp
    type_prop/non_zero.cpp
    type_prop/normalize.cpp
    type_prop/one_hot.cpp
    type_prop/pad.cpp
    type_prop/parameter.cpp
    type_prop/prelu.cpp
    type_prop/proposal.cpp
    type_prop/psroi_pooling.cpp
    type_prop/range.cpp
    type_prop/read_value.cpp
    type_prop/reduce_l1.cpp
    type_prop/reduce_l2.cpp
    type_prop/reorg_yolo.cpp
    type_prop/reshape.cpp
    type_prop/reverse.cpp
    type_prop/reverse_sequence.cpp
    type_prop/roi_align.cpp
    type_prop/roi_pooling.cpp
    type_prop/round.cpp
    type_prop/rnn_cell.cpp
    type_prop/rnn_sequence.cpp
    type_prop/round.cpp
    type_prop/scatter_elements_update.cpp
    type_prop/scatter_nd_update.cpp
    type_prop/scatter_update.cpp
    type_prop/select.cpp
    type_prop/shape_of.cpp
    type_prop/shuffle_channels.cpp
    type_prop/softmax.cpp
    type_prop/softplus.cpp
    type_prop/space_to_batch.cpp
    type_prop/space_to_depth.cpp
    type_prop/split.cpp
    type_prop/squared_difference.cpp
    type_prop/squeeze.cpp
    type_prop/swish.cpp
    type_prop/reduce_mean.cpp
    type_prop/reduce_prod.cpp
    type_prop/reduce_sum.cpp
    type_prop/ti.cpp
    type_prop/tile.cpp
    type_prop/top_k.cpp
    type_prop/transpose.cpp
    type_prop/unary_elementwise.cpp
    type_prop/unary_ops.cpp
    type_prop/unsqueeze.cpp
    type_prop/variadic_split.cpp
    type_prop_layers.cpp
    util.cpp
)

set_source_files_properties(includes.cpp PROPERTIES COMPILE_DEFINITIONS
    NGRAPH_INCLUDES="${PROJECT_SOURCE_DIR}/src/ngraph")

if (ENABLE_MKL_DNN)
    message(STATUS "NGRAPH_TESTS: IE:CPU enabled")
    set(ACTIVE_BACKEND_LIST ${ACTIVE_BACKEND_LIST} "IE:CPU")
    list(APPEND UNIT_TESTS_DEPENDENCIES MKLDNNPlugin)
endif()

if (ENABLE_CLDNN)
    message(STATUS "NGRAPH_TESTS: IE:GPU enabled")
    set(ACTIVE_BACKEND_LIST ${ACTIVE_BACKEND_LIST} "IE:GPU")
    list(APPEND UNIT_TESTS_DEPENDENCIES clDNNPlugin)
endif()

if (NGRAPH_INTERPRETER_ENABLE)
    list(APPEND SRC
        builder.cpp
        backend_api.cpp)
    set(ACTIVE_BACKEND_LIST ${ACTIVE_BACKEND_LIST} INTERPRETER)
endif()

add_definitions("-DTEST_FILES=\"${CMAKE_CURRENT_SOURCE_DIR}/files\"")
add_subdirectory(util)

# backend specific test files must meet the following requirements:
# 1) The must be named <name>.in.cpp
# 2) They must be in the `test/backend` directory
# 3) Include "util/test_control.hpp" in your cpp file
# 4) add the line `static string s_manifest = "${MANIFEST}";` to your cpp file
# 5) Use the `NGRAPH_TEST` macro in place of `TEST`.
# All such files are configured via cmake which replaces all instances of cmake variables
# such as ${BACKEND_NAME} with their values, such as CPU, GPU, or INTERPRETER.

set(MULTI_TEST_SRC
    backend/abc.in.cpp
    backend/abs.in.cpp
    backend/acos.in.cpp
    backend/acosh.in.cpp
    backend/add.in.cpp
    backend/aliased_output.in.cpp
    backend/api.in.cpp
    backend/asin.in.cpp
    backend/asinh.in.cpp
    backend/atan.in.cpp
    backend/atanh.in.cpp
    backend/auto_broadcast.in.cpp
    backend/avg_pool.in.cpp
    backend/batch_norm.in.cpp
    backend/broadcast.in.cpp
    backend/bucketize.in.cpp
    backend/builder_reduce_ops_opset1.in.cpp
    backend/ceiling.in.cpp
    backend/comparison.in.cpp
    backend/concat.in.cpp
    backend/constant.in.cpp
    backend/convert.in.cpp
    backend/convert_like.in.cpp
    backend/convolution.in.cpp
    backend/binary_convolution.in.cpp
    backend/cos.in.cpp
    backend/cosh.in.cpp
    backend/ctc_greedy_decoder.in.cpp
    backend/ctc_greedy_decoder_seq_len.in.cpp
    backend/cum_sum.in.cpp
    backend/detection_output.in.cpp
    backend/divide.in.cpp
    backend/dyn_reshape.in.cpp
    backend/strided_slice.in.cpp
    backend/dynamic.in.cpp
    backend/erf.in.cpp
    backend/exp.in.cpp
    backend/floor.in.cpp
    backend/function_name.in.cpp
    backend/fused_op.in.cpp
    backend/gather.in.cpp
    backend/gather_elements.in.cpp
    backend/gather_nd.in.cpp
    backend/gelu.in.cpp
    backend/group_convolution.in.cpp
    backend/group_convolution_backprop_data.in.cpp
    backend/hard_sigmoid.in.cpp
    backend/interpolate.in.cpp
    backend/log.in.cpp
    backend/log_softmax.in.cpp
    backend/logical_and.in.cpp
    backend/logical_not.in.cpp
    backend/logical_or.in.cpp
    backend/logical_xor.in.cpp
    backend/lrn.in.cpp
    backend/matmul.in.cpp
    backend/maximum.in.cpp
    backend/max_pool.in.cpp
    backend/minimum.in.cpp
    backend/multiple_backends.in.cpp
    backend/multiple_result.in.cpp
    backend/multiply.in.cpp
    backend/mvn.in.cpp
    backend/negative.in.cpp
    backend/node_name.in.cpp
    backend/normalize_l2.in.cpp
    backend/non_max_suppression.in.cpp
    backend/non_zero.in.cpp
    backend/numeric.in.cpp
    backend/one_hot.in.cpp
    backend/pad.in.cpp
    backend/parameter_as_output.in.cpp
    backend/power.in.cpp
    backend/proposal.in.cpp
    backend/psroi_pooling.in.cpp
    backend/range.in.cpp
    backend/recurrent_cells.in.cpp
    backend/reduce_max.in.cpp
    backend/reduce_mean.in.cpp
    backend/reduce_min.in.cpp
    backend/reduce_prod.in.cpp
    backend/reduce_sum.in.cpp
    backend/region_yolo.in.cpp
    backend/relu.in.cpp
    backend/reorg_yolo.in.cpp
    backend/reshape.in.cpp
    backend/reverse_sequence.in.cpp
    backend/reverse.in.cpp
    backend/roi_pooling.in.cpp
    backend/round.in.cpp
    backend/select.in.cpp
    backend/shape_of.in.cpp
    backend/sigmoid.in.cpp
    backend/sign.in.cpp
    backend/sin.in.cpp
    backend/sinh.in.cpp
    backend/softmax.in.cpp
    backend/split.in.cpp
    backend/sqrt.in.cpp
    backend/subtract.in.cpp
    backend/tan.in.cpp
    backend/tanh.in.cpp
    backend/tile.in.cpp
    backend/topk.in.cpp
    backend/transpose.in.cpp
    backend/unhandled_op.in.cpp
    backend/validate_call.in.cpp
    backend/zero_sized.in.cpp
)

if (NGRAPH_ONNX_IMPORT_ENABLE AND NOT NGRAPH_USE_PROTOBUF_LITE)
    list(APPEND MULTI_TEST_SRC
            onnx/onnx_import.in.cpp
            onnx/onnx_import_controlflow.in.cpp
            onnx/onnx_import_const_folding.in.cpp
            onnx/onnx_import_convpool.in.cpp
            onnx/onnx_import_dyn_shapes.in.cpp
            onnx/onnx_import_external_data.in.cpp
            onnx/onnx_import_org_openvino.in.cpp
            onnx/onnx_import_provenance.in.cpp
            onnx/onnx_import_reshape.in.cpp
            onnx/onnx_import_rnn.in.cpp
            onnx/onnx_import_quant.in.cpp
            onnx/onnx_test_utils.in.cpp)
    list(APPEND SRC
            onnx/onnx_import_exceptions.cpp
            onnx/onnx_import_library.cpp
            onnx/onnx_editor.cpp
            onnx/onnx_tensor_names.cpp)
endif()

foreach(BACKEND_NAME ${ACTIVE_BACKEND_LIST})
    string(TOLOWER ${BACKEND_NAME} BACKEND_DIR)
    string(REGEX REPLACE "([a-z0-9]+):(.*)" "\\1" BACKEND_DIR ${BACKEND_DIR})
    set(MANIFEST ${CMAKE_CURRENT_SOURCE_DIR}/runtime/${BACKEND_DIR}/unit_test.manifest)

    foreach(TEST_SRC ${MULTI_TEST_SRC})
        string(REPLACE ":" "_" BACKEND_NAME ${BACKEND_NAME})
        string(REPLACE ".in." "_${BACKEND_NAME}." TARGET_NAME ${TEST_SRC})
        configure_file(${TEST_SRC} ${TARGET_NAME})
        set(SRC ${CMAKE_CURRENT_BINARY_DIR}/${TARGET_NAME} ${SRC})
    endforeach()

    message(STATUS "Adding unit test for backend ${BACKEND_NAME}")
endforeach()

add_executable(unit-test ${SRC})

target_include_directories(unit-test PRIVATE ".")
target_include_directories(unit-test PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/runtime)

add_definitions("-DCURDIR=\"${CMAKE_CURRENT_SOURCE_DIR}\"")
add_definitions("-DJSON_INCLUDES=\"${JSON_INCLUDE_DIR}\"")

if(UNIT_TESTS_DEPENDENCIES)
    add_dependencies(unit-test ${UNIT_TESTS_DEPENDENCIES})
endif()

if(NGRAPH_ADDRESS_SANITIZER)
    add_compile_options(-g -fsanitize=address -fno-omit-frame-pointer)
endif()

target_link_libraries(unit-test PRIVATE ngraph_test_util
                                        openvino::conditional_compilation
                                        openvino::itt
                                        ngraph
                                        ngraph::builder
                                        ngraph_backend
                                        libgtest)

# Protobuf-lite does not support parsing files from prototxt format
# Since most of the onnx models are stored in this format it have to be disabled
if (NGRAPH_ONNX_IMPORT_ENABLE AND NOT NGRAPH_USE_PROTOBUF_LITE)
    # It's needed by onnx_import_library.cpp and onnx_import_exceptions.cpp tests to include onnx_pb.h.
    # Not linking statically to libprotobuf (linked into libonnx) avoids false-failing onnx_editor tests.
    target_include_directories(unit-test
        SYSTEM PRIVATE
            $<TARGET_PROPERTY:onnx,INTERFACE_INCLUDE_DIRECTORIES>
            $<TARGET_PROPERTY:onnx_proto,INTERFACE_INCLUDE_DIRECTORIES>
            ${Protobuf_INCLUDE_DIRS})
    target_compile_definitions(unit-test
        PRIVATE $<TARGET_PROPERTY:onnx,INTERFACE_COMPILE_DEFINITIONS>)

    get_target_property(ONNX_IMPORTER_SRC_DIR onnx_importer SOURCE_DIR)
    target_include_directories(unit-test PRIVATE ${ONNX_IMPORTER_SRC_DIR}/src)
endif()

target_compile_definitions(unit-test PRIVATE NGRAPH_VERSION_LABEL="${NGRAPH_VERSION_LABEL}")
if(NOT WIN32)
    target_link_libraries(unit-test PRIVATE pthread)
endif()
target_link_libraries(unit-test PRIVATE ${CMAKE_DL_LIBS})

if ("${CMAKE_CXX_COMPILER_ID}" MATCHES "^(Apple)?Clang$")
    target_compile_options(unit-test PRIVATE -Wno-undef -Wno-reserved-id-macro)
endif()

# So many type_prop tests these days that we need to set /bigobj flag for MSVC.
# We should probably split up type_prop.cpp.
if (MSVC)
    target_compile_options(unit-test PRIVATE "/bigobj")
endif()

target_link_libraries(unit-test PRIVATE inference_engine)
target_link_libraries(unit-test PRIVATE ie_backend)

if (NGRAPH_ONNX_IMPORT_ENABLE)
    target_link_libraries(unit-test PRIVATE onnx_importer onnx_editor)
endif()

if (NGRAPH_INTERPRETER_ENABLE)
    target_compile_definitions(unit-test PRIVATE NGRAPH_INTERPRETER_ENABLE)
    target_link_libraries(unit-test PRIVATE interpreter_backend)
endif()

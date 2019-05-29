cmake_minimum_required(VERSION 3.5)

 # Project
project(tensorflow-lite C CXX)


 # From https://stackoverflow.com/questions/10113017/setting-the-msvc-runtime-in-cmake
macro(configure_msvc_runtime)
  if(MSVC)
    # Default to statically-linked runtime.
    if("${MSVC_RUNTIME}" STREQUAL "")
      set(MSVC_RUNTIME "static")
    endif()
    # Set compiler options.
    set(variables
      CMAKE_C_FLAGS_DEBUG
      CMAKE_C_FLAGS_MINSIZEREL
      CMAKE_C_FLAGS_RELEASE
      CMAKE_C_FLAGS_RELWITHDEBINFO
      CMAKE_CXX_FLAGS_DEBUG
      CMAKE_CXX_FLAGS_MINSIZEREL
      CMAKE_CXX_FLAGS_RELEASE
      CMAKE_CXX_FLAGS_RELWITHDEBINFO
    )
    if(${MSVC_RUNTIME} STREQUAL "static")
      message(STATUS
        "MSVC -> forcing use of statically-linked runtime."
      )
      foreach(variable ${variables})
        if(${variable} MATCHES "/MD")
          string(REGEX REPLACE "/MD" "/MT" ${variable} "${${variable}}")
        endif()
      endforeach()
    else()
      message(STATUS
        "MSVC -> forcing use of dynamically-linked runtime."
      )
      foreach(variable ${variables})
        if(${variable} MATCHES "/MT")
          string(REGEX REPLACE "/MT" "/MD" ${variable} "${${variable}}")
        endif()
      endforeach()
    endif()
  endif()
endmacro()







 # Set C++14 as standard for the whole project
set(CMAKE_CXX_STANDARD 14)
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}" )

set(TENSORFLOW_ROOT_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../..")
set(TENSORFLOW_LITE_ROOT_DIR "${TENSORFLOW_ROOT_DIR}/tensorflow/lite")
set(TENSORFLOW_LITE_KERNEL_DIR "${TENSORFLOW_LITE_ROOT_DIR}/kernels")
set(TENSORFLOW_LITE_DOWNLOADED_DEPENDENCIES_DIR "${TENSORFLOW_LITE_ROOT_DIR}/downloads")
set(TENSORFLOW_LITE_GEMMLOWP_DIR "${TENSORFLOW_LITE_ROOT_DIR}/../../../gemmlowp")
set(TENSORFLOW_LITE_EIGEN_DIR "${TENSORFLOW_LITE_ROOT_DIR}/../../../eigen")
set(TENSORFLOW_LITE_FLATBUFFERS_DIR "${TENSORFLOW_LITE_ROOT_DIR}/../../../flatbuffers/include")
set(TENSORFLOW_LITE_FARMHASH_DIR "${TENSORFLOW_LITE_ROOT_DIR}/../../../farmhash/src")
set(TENSORFLOW_LITE_GMOCK_DIR "${TENSORFLOW_LITE_ROOT_DIR}/../../../googletest/googlemock/include")
set(TENSORFLOW_LITE_GTEST_DIR "${TENSORFLOW_LITE_ROOT_DIR}/../../../googletest/googletest/include")
set(TENSORFLOW_LITE_ABSEIL_DIR "${TENSORFLOW_LITE_ROOT_DIR}/../../../abseil-cpp")

set(GTEST_LIB_DIR "${TENSORFLOW_LITE_ROOT_DIR}/../../../googletest/lib/Debug")
set(FLATBUFFERS_LIB_DIR "${TENSORFLOW_LITE_ROOT_DIR}/../../../flatbuffers/Debug")

 if (WIN32)
    set(PLATFORM_ENV_TIME_SRC ${TENSORFLOW_ROOT_DIR}/tensorflow/core/platform/windows/env_time.cc)
    configure_msvc_runtime()
    set(variables
      CMAKE_C_FLAGS_DEBUG
      CMAKE_C_FLAGS_MINSIZEREL
      CMAKE_C_FLAGS_RELEASE
      CMAKE_C_FLAGS_RELWITHDEBINFO
      CMAKE_CXX_FLAGS_DEBUG
      CMAKE_CXX_FLAGS_MINSIZEREL
      CMAKE_CXX_FLAGS_RELEASE
      CMAKE_CXX_FLAGS_RELWITHDEBINFO
    )
else()
    set(PLATFORM_ENV_TIME_SRC ${TENSORFLOW_ROOT_DIR}/tensorflow/core/platform/unix/env_time.cc)
endif (WIN32)


 # TODO: SIMD Visual studio
add_definitions(
    -DGEMMLOWP_ALLOW_SLOW_SCALAR_FALLBACK
    -DNOMINMAX
    -DFARMHASH_NO_BUILTIN_EXPECT)


 include_directories(${TENSORFLOW_ROOT_DIR}
    ${TENSORFLOW_LITE_GEMMLOWP_DIR}
    ${TENSORFLOW_LITE_FLATBUFFERS_DIR}
    ${TENSORFLOW_LITE_EIGEN_DIR}
    ${TENSORFLOW_LITE_FARMHASH_DIR}
    ${TENSORFLOW_LITE_GMOCK_DIR}
    ${TENSORFLOW_LITE_GTEST_DIR}
    ${TENSORFLOW_LITE_ABSEIL_DIR}
    )

 SET (TENSORFLOW_LITE_KERNEL_SOURCES
    ${TENSORFLOW_LITE_KERNEL_DIR}/activations.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/add.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/bidirectional_sequence_rnn.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/basic_rnn.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/batch_to_space_nd.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/concatenation.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/conv.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/cpu_backend_gemm_eigen.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/cpu_backend_context.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/cpu_backend_support.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/depthwise_conv.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/embedding_lookup.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/embedding_lookup_sparse.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/elementwise.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/eigen_support.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/fake_quant.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/fully_connected.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/gather.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/gemmlowp_support.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/hashtable_lookup.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/internal/optimized/neon_tensor_utils.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/internal/quantization_util.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/internal/reference/portable_tensor_utils.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/internal/tensor_utils.cc    
    ${TENSORFLOW_LITE_KERNEL_DIR}/kernel_util.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/internal/kernel_utils.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/l2norm.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/local_response_norm.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/lsh_projection.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/lstm.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/lstm_eval.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/neg.cc    
    ${TENSORFLOW_LITE_KERNEL_DIR}/audio_spectrogram.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/internal/spectrogram.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/mfcc.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/internal/mfcc.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/internal/mfcc_dct.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/internal/mfcc_mel_filterbank.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/detection_postprocess.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/if.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/while.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/div.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/sub.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/bidirectional_sequence_lstm.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/unidirectional_sequence_lstm.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/resize_nearest_neighbor.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/split.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/split_v.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/squeeze.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/strided_slice.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/exp.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/topk_v2.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/cast.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/dequantize.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/maximum_minimum.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/arg_min_max.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/comparisons.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/floor.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/ceil.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/round.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/tile.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/select.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/slice.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/transpose_conv.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/expand_dims.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/sparse_to_dense.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/pack.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/one_hot.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/logical.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/unpack.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/floor_div.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/zeros_like.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/floor_mod.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/range.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/squared_difference.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/fill.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/mirror_pad.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/unique.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/add_n.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/gather.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/gather_nd.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/mul.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/pad.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/pow.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/where.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/reverse_sequence.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/matrix_diag.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/quantize.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/matrix_set_diag.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/pooling.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/rank.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/register.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/reshape.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/reverse.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/reduce.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/resize_bilinear.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/shape.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/skip_gram.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/space_to_batch_nd.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/space_to_depth.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/svdf.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/transpose.cc
    ${TENSORFLOW_LITE_KERNEL_DIR}/unidirectional_sequence_rnn.cc
    )

 add_library(tensorflow-lite STATIC
    ${TENSORFLOW_LITE_ROOT_DIR}/simple_memory_arena.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/arena_planner.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/graph_info.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/minimal_logging_default.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/minimal_logging.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/stderr_reporter.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/interpreter.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/string_util.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/allocation.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/model.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/util.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/tflite_osal.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/fft4g.c
    ${TENSORFLOW_LITE_ROOT_DIR}/mmap_allocation_disabled.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/mutable_op_resolver.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/nnapi/nnapi_implementation_disabled.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/delegates/nnapi/nnapi_delegate.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/optional_debug_tools.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/c/c_api_internal.c
    ${TENSORFLOW_LITE_ROOT_DIR}/core/api/error_reporter.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/core/api/op_resolver.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/core/api/flatbuffer_conversions.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/core/subgraph.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/experimental/ruy/allocator.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/experimental/ruy/context.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/experimental/ruy/trmul.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/experimental/ruy/tune.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/experimental/ruy/block_map.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/experimental/ruy/thread_pool.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/experimental/ruy/blocking_counter.cc
    ${TENSORFLOW_LITE_ROOT_DIR}/experimental/ruy/detect_dotprod.cc
    ${TENSORFLOW_LITE_KERNEL_SOURCES}
    ${TENSORFLOW_LITE_FARMHASH_DIR}/farmhash.cc
    )



 add_library(tensorflow-lite_test_util STATIC
    ${TENSORFLOW_LITE_KERNEL_DIR}/test_util.cc
    ${TENSORFLOW_ROOT_DIR}/tensorflow/core/platform/default/logging.cc
    ${TENSORFLOW_ROOT_DIR}/tensorflow/core/platform/env_time.cc
    ${PLATFORM_ENV_TIME_SRC}
  )

 function(add_tflite_tests test_directory)
  file(GLOB TEST_SRC_FILES RELATIVE ${test_directory} ${test_directory}/*_test.cc )
  foreach (TEST_SRC_FILE ${TEST_SRC_FILES})
      string(REPLACE ".cc" "" TEST_NAME ${TEST_SRC_FILE})
      add_executable(${TEST_NAME} ${test_directory}/${TEST_SRC_FILE} )
      target_link_libraries(${TEST_NAME} debug tensorflow-lite ${GTEST_LIB_DIR}/gtestd.lib ${GTEST_LIB_DIR}/gmockd.lib ${FLATBUFFERS_LIB_DIR}/flatbuffers.lib tensorflow-lite_test_util)
      #target_link_libraries(${TEST_NAME} optimized tensorflow-lite ${GTEST_LIB_DIR}/gtest.lib ${GTEST_LIB_DIR}/gmock.lib tensorflow-lite_test_util)
  endforeach()

 endfunction()


add_tflite_tests(${TENSORFLOW_LITE_ROOT_DIR})
#add_tflite_tests(${TENSORFLOW_LITE_KERNEL_DIR})
#add_tflite_tests(${TENSORFLOW_LITE_KERNEL_DIR}/internal)
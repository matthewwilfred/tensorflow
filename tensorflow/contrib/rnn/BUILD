# Description:
#   Contains ops to train linear models on top of TensorFlow.
#   APIs here are meant to evolve over time.

licenses(["notice"])  # Apache 2.0

exports_files(["LICENSE"])

package(default_visibility = ["//visibility:public"])

load("//tensorflow:tensorflow.bzl", "cuda_py_tests")
load(
    "//tensorflow:tensorflow.bzl",
    "tf_custom_op_library",
    "tf_cc_test",
    "tf_py_test",
    "tf_gen_op_libs",
    "tf_kernel_library",
)
load(
    "//tensorflow/core:platform/default/build_config.bzl",
    "tf_kernel_tests_linkstatic",
)

py_library(
    name = "rnn_py",
    srcs = ["__init__.py"] + glob(["python/ops/*.py"]),
    data = [
        ":python/ops/_gru_ops.so",
        ":python/ops/_lstm_ops.so",
    ],
    srcs_version = "PY2AND3",
    visibility = ["//visibility:public"],
    deps = [
        "//tensorflow/contrib/layers:layers_py",
        "//tensorflow/contrib/util:util_py",
        "//tensorflow/python:array_ops",
        "//tensorflow/python:clip_ops",
        "//tensorflow/python:framework_for_generated_wrappers",
        "//tensorflow/python:init_ops",
        "//tensorflow/python:math_ops",
        "//tensorflow/python:nn_ops",
        "//tensorflow/python:platform",
        "//tensorflow/python:rnn",
        "//tensorflow/python:rnn_cell",
        "//tensorflow/python:util",
        "//tensorflow/python:variable_scope",
    ],
)

cuda_py_tests(
    name = "rnn_cell_test",
    size = "small",
    srcs = ["python/kernel_tests/rnn_cell_test.py"],
    additional_deps = [
        ":rnn_py",
        "//tensorflow:tensorflow_py",
        "//tensorflow/python:framework_test_lib",
        "//tensorflow/python:platform_test",
    ],
)

cuda_py_tests(
    name = "core_rnn_cell_test",
    size = "small",
    srcs = ["python/kernel_tests/core_rnn_cell_test.py"],
    additional_deps = [
        "//tensorflow:tensorflow_py",
        "//tensorflow/python:rnn_cell",
    ],
)

cuda_py_tests(
    name = "rnn_test",
    size = "medium",
    srcs = ["python/kernel_tests/rnn_test.py"],
    additional_deps = [
        ":rnn_py",
        "//tensorflow:tensorflow_py",
        "//tensorflow/python:framework_test_lib",
        "//tensorflow/python:platform_test",
    ],
)

cuda_py_tests(
    name = "core_rnn_test",
    size = "medium",
    srcs = ["python/kernel_tests/core_rnn_test.py"],
    additional_deps = [
        "//tensorflow:tensorflow_py",
        "//tensorflow/python:util",
    ],
    shard_count = 10,
)

tf_py_test(
    name = "fused_rnn_cell_test",
    size = "small",
    srcs = ["python/kernel_tests/fused_rnn_cell_test.py"],
    additional_deps = [
        ":rnn_py",
        "//tensorflow:tensorflow_py",
        "//tensorflow/python:framework_test_lib",
        "//tensorflow/python:platform_test",
    ],
)

cuda_py_tests(
    name = "lstm_ops_test",
    size = "medium",
    srcs = ["python/kernel_tests/lstm_ops_test.py"],
    additional_deps = [
        ":rnn_py",
        "//tensorflow:tensorflow_py",
        "//tensorflow/python:framework_test_lib",
        "//tensorflow/python:platform_test",
    ],
)

tf_custom_op_library(
    name = "python/ops/_lstm_ops.so",
    srcs = [
        "kernels/blas_gemm.cc",
        "kernels/blas_gemm.h",
        "kernels/lstm_ops.cc",
        "kernels/lstm_ops.h",
        "ops/lstm_ops.cc",
    ],
    gpu_srcs = [
        "kernels/blas_gemm.h",
        "kernels/lstm_ops_gpu.cu.cc",
        "kernels/lstm_ops.h",
    ],
    deps = [
        "//tensorflow/core/kernels:eigen_helpers",
    ],
)

tf_custom_op_library(
    name = "python/ops/_gru_ops.so",
    srcs = [
        "kernels/blas_gemm.cc",
        "kernels/blas_gemm.h",
        "kernels/gru_ops.cc",
        "kernels/gru_ops.h",
        "ops/gru_ops.cc",
    ],
    gpu_srcs = [
        "kernels/blas_gemm.h",
        "kernels/gru_ops_gpu.cu.cc",
        "kernels/gru_ops.h",
    ],
    deps = [
        "//tensorflow/core/kernels:eigen_helpers",
    ],
)

cuda_py_tests(
    name = "gru_ops_test",
    size = "small",
    srcs = ["python/kernel_tests/gru_ops_test.py"],
    additional_deps = [
        ":rnn_py",
        "//tensorflow:tensorflow_py",
        "//tensorflow/python:framework_test_lib",
        "//tensorflow/python:platform_test",
        "//tensorflow/python:variable_scope",
    ],
)

tf_cc_test(
    name = "ops/gru_ops_test",
    size = "small",
    srcs = ["ops/gru_ops_test.cc"],
    data = [":python/ops/_gru_ops.so"],
    linkstatic = tf_kernel_tests_linkstatic(),
    deps = [
        "//tensorflow/c:c_api",
        "//tensorflow/core:framework",
        "//tensorflow/core:framework_internal",
        "//tensorflow/core:lib",
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
        "//tensorflow/core:testlib",
    ],
)

tf_cc_test(
    name = "ops/lstm_ops_test",
    size = "small",
    srcs = ["ops/lstm_ops_test.cc"],
    data = [":python/ops/_lstm_ops.so"],
    linkstatic = tf_kernel_tests_linkstatic(),
    deps = [
        "//tensorflow/c:c_api",
        "//tensorflow/core:framework",
        "//tensorflow/core:framework_internal",
        "//tensorflow/core:lib",
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
        "//tensorflow/core:testlib",
    ],
)

filegroup(
    name = "all_files",
    srcs = glob(
        ["**/*"],
        exclude = [
            "**/METADATA",
            "**/OWNERS",
        ],
    ),
    visibility = ["//tensorflow:__subpackages__"],
)

tf_gen_op_libs(
    op_lib_names = [
        "lstm_ops",
        "gru_ops",
    ],
)

tf_kernel_library(
    name = "gru_ops_kernels",
    srcs = [
        "kernels/blas_gemm.cc",
        "kernels/blas_gemm.h",
    ],
    gpu_srcs = [
        "kernels/blas_gemm.h",
    ],
    prefix = "kernels/gru_ops",
    deps = [
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core/kernels:eigen_helpers",
        "//third_party/eigen3",
    ],
)

tf_kernel_library(
    name = "lstm_ops_kernels",
    srcs = [
        "kernels/blas_gemm.cc",
        "kernels/blas_gemm.h",
    ],
    gpu_srcs = [
        "kernels/blas_gemm.h",
    ],
    prefix = "kernels/lstm_ops",
    deps = [
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core/kernels:eigen_helpers",
        "//third_party/eigen3",
    ],
)

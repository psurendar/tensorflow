licenses(["notice"])  # Apache 2.0

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

load(
    "//tensorflow/core:platform/default/build_config.bzl",
    "tf_proto_library",
)
load(
    "@local_config_cuda//cuda:build_defs.bzl",
    "if_cuda",
)

tf_proto_library(
    name = "op_performance_data",
    srcs = ["op_performance_data.proto"],
    cc_api_version = 2,
    protodeps = [
        "//tensorflow/core:protos_all",
    ],
    visibility = ["//visibility:public"],
)

cc_library(
    name = "graph_properties",
    srcs = ["graph_properties.cc"],
    hdrs = ["graph_properties.h"],
    visibility = ["//visibility:public"],
    deps = [
        ":op_performance_data_cc",
        ":utils",
        "//tensorflow/core:core_cpu_base",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core/grappler:grappler_item",
        "//tensorflow/core/grappler/clusters:cluster",
    ],
)

cc_test(
    name = "graph_properties_test",
    srcs = ["graph_properties_test.cc"],
    args = ["--heap_check=local"],  # The GPU tracer leaks memory
    deps = [
        ":graph_properties",
        "//tensorflow/cc:cc_ops",
        "//tensorflow/cc:scope",
        "//tensorflow/core:framework",
        "//tensorflow/core:lib_proto_parsing",
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
        "//tensorflow/core/grappler:grappler_item",
        "//tensorflow/core/grappler/clusters:single_machine",
        "//tensorflow/core/grappler/inputs:trivial_test_graph_input_yielder",
    ],
)

cc_library(
    name = "graph_memory",
    srcs = ["graph_memory.cc"],
    hdrs = ["graph_memory.h"],
    visibility = ["//visibility:public"],
    deps = [
        ":graph_properties",
        "//tensorflow/core:framework",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core/grappler:grappler_item",
        "//tensorflow/core/grappler/clusters:cluster",
    ],
)

cc_test(
    name = "graph_memory_test",
    srcs = ["graph_memory_test.cc"],
    args = ["--heap_check=local"],  # The GPU tracer leaks memory
    deps = [
        ":graph_memory",
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
        "//tensorflow/core/grappler:grappler_item",
        "//tensorflow/core/grappler/inputs:trivial_test_graph_input_yielder",
    ],
)

cc_library(
    name = "robust_stats",
    srcs = ["robust_stats.cc"],
    hdrs = ["robust_stats.h"],
    visibility = ["//visibility:public"],
)

cc_test(
    name = "robust_stats_test",
    srcs = ["robust_stats_test.cc"],
    deps = [
        ":robust_stats",
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
    ],
)

cc_library(
    name = "utils",
    srcs = ["utils.cc"],
    hdrs = ["utils.h"],
    defines = if_cuda(["GOOGLE_CUDA=1"]),
    visibility = ["//visibility:public"],
    deps = [
        ":op_performance_data_cc",
        "//third_party/eigen3",
        "//tensorflow/core/grappler/clusters:utils",
        "//tensorflow/core:core_cpu_base",
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core:lib_proto_parsing",
        "//tensorflow/core:protos_all_cc",
    ] + if_cuda([
        "//tensorflow/core:cuda",
        "@local_config_cuda//cuda:cuda_headers",
    ]),
)

cc_library(
    name = "cost_estimator",
    hdrs = ["cost_estimator.h"],
    visibility = ["//visibility:public"],
    deps = [
        "//tensorflow/core:lib",
    ],
)

cc_library(
    name = "virtual_placer",
    srcs = ["virtual_placer.cc"],
    hdrs = ["virtual_placer.h"],
    visibility = ["//visibility:public"],
    deps = [
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core/grappler:devices",
        "//tensorflow/core/grappler/clusters:cluster",
    ],
)

cc_test(
    name = "virtual_placer_test",
    srcs = ["virtual_placer_test.cc"],
    deps = [
        ":virtual_placer",
        "//tensorflow/core:core_cpu",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
        "//tensorflow/core/grappler/clusters:virtual_cluster",
    ],
)

cc_library(
    name = "virtual_scheduler",
    srcs = ["virtual_scheduler.cc"],
    hdrs = ["virtual_scheduler.h"],
    visibility = ["//visibility:public"],
    deps = [
        ":graph_properties",
        ":utils",
        ":virtual_placer",
        "//tensorflow/core:framework",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core/grappler:grappler_item",
        "//tensorflow/core/grappler:utils",
        "//tensorflow/core/grappler/clusters:utils",
        "//tensorflow/core/grappler/costs:cost_estimator",
    ],
)

cc_library(
    name = "measuring_cost_estimator",
    srcs = ["measuring_cost_estimator.cc"],
    hdrs = ["measuring_cost_estimator.h"],
    visibility = ["//visibility:public"],
    deps = [
        ":robust_stats",
        "//tensorflow/core:core_cpu",
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core:lib_internal",
        "//tensorflow/core:lib_proto_parsing",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core/grappler:grappler_item",
        "//tensorflow/core/grappler/clusters:cluster",
        "//tensorflow/core/grappler/costs:cost_estimator",
        "//tensorflow/core/kernels:ops_util",
    ],
)

cc_library(
    name = "op_level_cost_estimator",
    srcs = ["op_level_cost_estimator.cc"],
    hdrs = ["op_level_cost_estimator.h"],
    visibility = ["//visibility:public"],
    deps = [
        ":cost_estimator",
        ":op_performance_data_cc",
        "//tensorflow/core:framework",
        "//tensorflow/core/grappler/clusters:utils",
    ],
)

cc_test(
    name = "op_level_cost_estimator_test",
    srcs = ["op_level_cost_estimator_test.cc"],
    deps = [
        ":op_level_cost_estimator",
        "//tensorflow/core:framework",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
    ],
)

cc_library(
    name = "analytical_cost_estimator",
    srcs = ["analytical_cost_estimator.cc"],
    hdrs = ["analytical_cost_estimator.h"],
    visibility = ["//visibility:public"],
    deps = [
        ":cost_estimator",
        ":graph_properties",
        ":op_level_cost_estimator",
        ":op_performance_data_cc",
        ":utils",
        ":virtual_placer",
        ":virtual_scheduler",
        "//tensorflow/core:core_cpu_base",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core/grappler:grappler_item",
    ],
)

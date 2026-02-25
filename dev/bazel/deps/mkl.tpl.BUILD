package(default_visibility = ["//visibility:public"])
load("@rules_cc//cc:defs.bzl", "cc_library", "cc_import")

cc_library(
    name = "headers",
    hdrs = glob([
        "include/**/*.h",
        "include/**/*.hpp",
    ]),
    includes = [
        "include",
    ],
    defines = [
        "MKL_ILP64"
    ],
)

# MKL static archives have circular symbol dependencies (core ↔ tbb_thread ↔ ilp64).
# Using cc_import with alwayslink=True causes Bazel to pass --whole-archive for each,
# which forces all symbols to be resolved regardless of link order.
# This replaces the previous $(location ...) + --start-group/--end-group approach
# which failed in binary link sandboxes because $(location) paths are not staged
# as linker inputs for downstream cc_test/cc_binary targets.
cc_import(
    name = "mkl_core_a",
    static_library = "lib/libmkl_core.a",
    alwayslink = True,
)
cc_import(
    name = "mkl_ilp64_a",
    static_library = "lib/libmkl_intel_ilp64.a",
    alwayslink = True,
)
cc_import(
    name = "mkl_tbb_a",
    static_library = "lib/libmkl_tbb_thread.a",
    alwayslink = True,
)

cc_library(
    name = "mkl_static",
    linkopts = [
        "-lpthread",
        "-lm",
        "-ldl",
    ],
    deps = [
        ":mkl_core_a",
        ":mkl_ilp64_a",
        ":mkl_tbb_a",
        ":headers",
        "@opencl//:opencl_binary",
    ],
)

cc_library(
    name = "mkl_core",
    linkopts = [
        "-lpthread",
    ],
    deps = [
        ":headers",
        ":mkl_static",
    ]
)

cc_library(
    name = "mkl_dpc_utils",
    linkopts = [
        "-fsycl-max-parallel-link-jobs=16",
    ],
    srcs = [
        "lib/libmkl_sycl_blas.so",
        "lib/libmkl_sycl_lapack.so",
        "lib/libmkl_sycl_sparse.so",
        "lib/libmkl_sycl_dft.so",
        "lib/libmkl_sycl_vm.so",
        "lib/libmkl_sycl_rng.so",
        "lib/libmkl_sycl_stats.so",
        "lib/libmkl_sycl_data_fitting.so",
        "lib/libmkl_sycl_blas.so.5",
        "lib/libmkl_sycl_lapack.so.5",
        "lib/libmkl_sycl_sparse.so.5",
        "lib/libmkl_sycl_dft.so.5",
        "lib/libmkl_sycl_vm.so.5",
        "lib/libmkl_sycl_rng.so.5",
        "lib/libmkl_sycl_stats.so.5",
        "lib/libmkl_sycl_data_fitting.so.5",
    ],
)

cc_library(
    name = "mkl_dpc",
    linkopts = [
        "-fsycl-max-parallel-link-jobs=16",
    ],
    srcs = [
        "lib/libmkl_sycl.so",
    ],
    deps = [
        ":headers",
        ":mkl_dpc_utils",
    ],
)

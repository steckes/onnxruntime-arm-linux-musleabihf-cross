load("@rules_foreign_cc//foreign_cc:defs.bzl", "cmake")

filegroup(
    name = "all",
    srcs = glob(["**"]),
)

cmake(
    name = "onnxruntime",
    env = {
        "PATH": "$$EXT_BUILD_ROOT$$/external/arm32_gcc_linux_x86_64_musl/bin:$$PATH",
        "CMAKE_BUILD_PARALLEL_LEVEL": "4",
        "AR": "$$EXT_BUILD_ROOT$$/external/arm32_gcc_linux_x86_64_musl/arm-buildroot-linux-musleabihf/bin/ar",
        "AR_FLAGS": "--plugin=$$EXT_BUILD_ROOT$$/external/arm32_gcc_linux_x86_64_musleabihf/12.3.0/liblto_plugin.so",
        "ARFLAGS": "--plugin=$$EXT_BUILD_ROOT$$/external/arm32_gcc_linux_x86_64_musleabihf/12.3.0/liblto_plugin.so",
    },
    generate_args = [
        "-Donnxruntime_BUILD_UNIT_TESTS=OFF",
        "-Donnxruntime_ENABLE_CPUINFO=OFF",
        "-DONNX_BUILD_SHARED_LIBS=ON",  # Change to ON to build shared libraries
        "-DCMAKE_SYSTEM_NAME=Linux",
        "-DCMAKE_SYSTEM_VERSION=1",
        "-DCMAKE_C_COMPILER=arm-buildroot-linux-musleabihf-gcc.br_real",
        "-DCMAKE_CXX_COMPILER=arm-buildroot-linux-musleabihf-g++.br_real",
        "-DCMAKE_AR=$$EXT_BUILD_ROOT$$/external/arm32_gcc_linux_x86_64_musl/arm-buildroot-linux-musleabihf/bin/ar",
        "-DCMAKE_SYSTEM_PROCESSOR=armv7",
        "-DCMAKE_CXX_STANDARD=20",
        "-DCMAKE_CXX_EXTENSIONS=ON",
        "-DCMAKE_FIND_ROOT_PATH_MODE_PROGRAM=NEVER",
        "-DCMAKE_FIND_ROOT_PATH_MODE_LIBRARY=ONLY",
        "-DCMAKE_FIND_ROOT_PATH_MODE_INCLUDE=ONLY",
        "-DCMAKE_FIND_ROOT_PATH_MODE_PACKAGE=ONLY",
        "-DCMAKE_EXE_LINKER_FLAGS=-lstdc++",
        "-DCMAKE_TRY_COMPILE_TARGET_TYPE=STATIC_LIBRARY",
        "-DCMAKE_INTERPROCEDURAL_OPTIMIZATION=TRUE",
        "-DFLATBUFFERS_BUILD_FLATC:BOOL=ON",
        "-DBUILD_SHARED_LIBS=ON",  # Enable shared library build
    ],
    generate_crosstool_file = False,
    lib_source = ":all",
    linkopts = [
        "-pthread",
        "-latomic",
    ],
    out_shared_libs = ["libonnxruntime.so"],  # Specify the shared library output
    postfix_script = """
        include_dir="$INSTALLDIR/include"

        find "$include_dir" -type l | while IFS= read -r file; do
            if [[ -L "$file" ]]; then
                target="$(readlink -f "$file")"
                rm "$file" && cp -a "${target}" "$file"
            fi
        done

        build_deps_dir="$BUILD_TMPDIR/_deps"
        lib_dir="$INSTALLDIR/lib"

        find $build_deps_dir -type f -name '*.so' -exec cp {} $lib_dir \\;

        find "$lib_dir" -type l -name "*.so" | while IFS= read -r file; do
            if [[ -L "$file" ]]; then
                target="$(readlink -f "$file")"
                rm "$file" && cp -a "${target}" "$file"
            fi
        done
    """,
    tags = ["requires-network"],
    visibility = ["//visibility:public"],
    working_directory = "cmake",
)

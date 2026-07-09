---
title: Linux开发环境的探讨
published: 2026-07-08
pinned: true
description: 我即将进行重装系统，为此我正在进行准备。
tags:
  - Thoughts_Ideas
  - Blogging
category: Linux
licenseName: Unlicensed
author: casumi
draft: false
---
> 我经常喜欢去GitHub上面乱逛然后找点感兴趣的小项目并在自己的电脑上进行编译尝试，不过这也导致了一个问题：这些项目的语言各不相同，有C、Cpp、Rust、Zig、Go、Python、Kotlin、Java、Ts等等等等各种各样，需要的系统库依赖的版本也各种各样，导致我管理起来非常麻烦。我又非常关系系统库的干净与整洁，这导致我非常痛苦。

## 解决方案

使用 devbox + direnv ，必要时再用上 distrobox

为了使得编译出来的项目在任何linux上都能够运行，决定尽可能使用静态编译：
- 对于C、C++项目：CMake配置使用 `zig cc`、`zig c++` 进行静态编译并用Ninja构建
- 对于Rust项目：本身Cargo静态编译就行了，再使用 `cargo-zigbuild` 来接管链接过程，`cargo zigbuild --target x86_64-unknown-linux-musl`
- 对于Go项目：直接Go静态编译就行了
- 对于Zig项目：直接Zig静态编译就行了
- 对于Python项目：uv随便吧
- 对于Kotlin项目：反正基本上也只在安卓上运行
- 对于TS项目：npm随便吧

测试方案：编译llama.cpp

cmake -B build-cuda -G Ninja \
  -DCMAKE_C_COMPILER:STRING="zig;cc" \
  -DCMAKE_CXX_COMPILER:STRING="zig;c++" \
  -DCMAKE_BUILD_WITH_INSTALL_RPATH=ON \
  -DCMAKE_INSTALL_RPATH='$ORIGIN' \
  -DGGML_CUDA=ON \
  -DCMAKE_CUDA_FLAGS='-Xcompiler=-U_GNU_SOURCE'

  
cmake --build build-cuda -j$(nproc)


# 注意，cuda后端不支持静态编译

# 不过vulkan后端支持

cmake -B build-vulkan -G Ninja \
  -DCMAKE_C_COMPILER:STRING="zig;cc" \
  -DCMAKE_CXX_COMPILER:STRING="zig;c++" \
  -DCMAKE_BUILD_RPATH='$ORIGIN' \
  -DGGML_STATIC=ON \
  -DBUILD_SHARED_LIBS=OFF \
  -DGGML_VULKAN=ON \
  -DGGML_CUDA=OFF

我认为直接使用vulkan后端就行了，没有必要使用cuda

cmake -B build-vulkan -G Ninja \
  -DCMAKE_C_COMPILER:STRING="zig;cc" \
  -DCMAKE_CXX_COMPILER:STRING="zig;c++" \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_BUILD_RPATH='$ORIGIN' \
  -DBUILD_SHARED_LIBS=ON \
  -DGGML_VULKAN=ON \
  -DGGML_CUDA=OFF

cmake --build build-vulkan -j$(nproc)
  
strip build-vulkan/bin/*



cmake -B build-vulkan -G Ninja \
  -DCMAKE_C_COMPILER:STRING="zig;cc" \
  -DCMAKE_CXX_COMPILER:STRING="zig;c++" \
  -DCMAKE_BUILD_TYPE=Release \
  -DBUILD_SHARED_LIBS=ON \
  -DGGML_STATIC=OFF \
  -DGGML_VULKAN=ON \
  -DCMAKE_BUILD_RPATH='$ORIGIN'
cmake --build build-vulkan -j$(nproc)
strip build-vulkan/bin/*.so* build-vulkan/bin/llama-*


zsh主题推荐：robbyrussell、
<!--
- Copyright (c) 2019-2020, Arm Limited and Contributors
-
- SPDX-License-Identifier: Apache-2.0
-
- Licensed under the Apache License, Version 2.0 the "License";
- you may not use this file except in compliance with the License.
- You may obtain a copy of the License at
-
-     http://www.apache.org/licenses/LICENSE-2.0
-
- Unless required by applicable law or agreed to in writing, software
- distributed under the License is distributed on an "AS IS" BASIS,
- WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
- See the License for the specific language governing permissions and
- limitations under the License.
-
-->

# Build Guides <!-- omit in toc -->

# Contents <!-- omit in toc -->

- [CMake Options](#cmake-options)
  - [VKB_<sample_name>](#vkb_sample_name)
  - [VKB_SYMLINKS](#vkb_symlinks)
  - [VKB_ENTRYPOINTS](#vkb_entrypoints)
  - [VKB_VALIDATION_LAYERS](#vkb_validation_layers)
  - [VKB_WARNINGS_AS_ERRORS](#vkb_warnings_as_errors)
- [3D models](#3d-models)
- [Performance data](#performance-data)
- [Windows](#windows)
  - [Dependencies](#dependencies)
  - [Build with CMake](#build-with-cmake)
- [Linux](#linux)
  - [Dependencies](#dependencies-1)
  - [Build with CMake](#build-with-cmake-1)
- [macOS](#macos)
  - [Dependencies](#dependencies-2)
  - [Build with CMake](#build-with-cmake-2)
- [Android](#android)
  - [Dependencies](#dependencies-3)
  - [Build with Gradle](#build-with-gradle)
  - [Build with CMake](#build-with-cmake-3)
- [Building Individual Samples](#building-individual-samples)
  - [CMake](#cmake)
  - [Visual Studio](#visual-studio)

# CMake Options

The following options are used to change the build configuration

#### VKB\_<sample_name>

Choose whether to include a sample at build time.

- `ON` - Build Sample
- `OFF` - Exclude Sample

**Default:** `ON`

#### VKB_BUILD_SAMPLES

Choose whether to build the samples.

- `ON` - Build All Samples
- `OFF` - Skip building Samples

**Default:** `ON`

#### VKB_BUILD_TESTS

Choose whether to build the tests

- `ON` - Build All Tests
- `OFF` - Skip building Tests

**Default:** `OFF`

#### VKB_SYMLINKS
Rather than changing the working directory inside the IDE, `VKB_SYMLINKS` will enable symlink creation pointing to the root directory which exposes the assets and outputs folders to the samples.

**Default:** `OFF`

#### VKB_ENTRYPOINTS

Generate a build project for each application so that they can be run separately

**Default:** `OFF`

#### VKB_VALIDATION_LAYERS

Enable Validation Layers

**Default:** `OFF`

#### VKB_WARNINGS_AS_ERRORS

Treat all warnings as errors

**Default:** `ON`

# 3D models

Most of the samples require 3D models downloaded from https://github.com/KhronosGroup/Vulkan-Samples-Assets as git submodule.

On Android, Gradle will run CMake which will sync assets to the device if there has been a change.

However, to sync them manually you may run the following command to ensure up to date assets are on the device:

```
adb push --sync assets /sdcard/Android/data/com.khronos.vulkan_samples/files/
adb push --sync shaders /sdcard/Android/data/com.khronos.vulkan_samples/files/
```

# Performance data

In order for performance data to be displayed, profiling needs to be enabled on the device. Some devices may disable it by default.

Profiling can be enabled via adb:

```
adb shell setprop security.perf_harden 0
```

> Performance data is captured using HWCPipe.
> For details on this project and how to integrate it in your pipeline,
> visit: https://github.com/ARM-software/HWCPipe

# Windows

## Dependencies

- CMake v3.10+ (known to work with 3.10.2)
- Visual Studio 2017 or above
- [clang-format-8](#clang-format-and-visual-studio)
- [CMake Options](#cmake-options)
- [3D models](#3d-models)

## Clang Format and Visual Studio

Visual Studio comes with `clang-format-6` which is incompatible with some of the styles we use in our `.clang-format` file. It is recommended to point to a `clang-format-8.exe` binary within the in-built clang formatter, or disable it and use a third party extension that is more up to date.

Go to the [LLVM downloads page](http://releases.llvm.org/download.html) to get clang.

## Build with CMake

> Please make sure, when running any sample, that you either:
>
> - Enable [Developer Mode](https://docs.microsoft.com/en-us/windows/uwp/get-started/enable-your-device-for-development "Microsoft Tutorial to Enable Developer Mode 'docs.microsoft.com'")
> - Run Command Prompt or Visual Studio as administrator

`Step 1.` The following command will generate the VS project

```
cmake -G"Visual Studio 15 2017 Win64" -H. -Bbuild/windows
```

`Step 2.` Build the Visual Studio project

```
cmake --build build/windows --config Release --target vulkan_samples
```

`Step 3.` Run the **Vulkan Samples** application

```
build\windows\app\bin\Release\AMD64\vulkan_samples.exe
```

# Linux

## Dependencies

- CMake v3.10+ (known to work with 3.10.2)
- C++14 Compiler (tested on GCC 7.3)
- [CMake Options](#cmake-options)
- [3D models](#3d-models)

```
sudo apt-get install cmake g++ xorg-dev libglu1-mesa-dev
```

## Build with CMake

`Step 1.` The following command will generate the project

```
cmake -G "Unix Makefiles" -H. -Bbuild/linux -DCMAKE_BUILD_TYPE=Release
```

`Step 2.` Build the project

```
cmake --build build/linux --config Release --target vulkan_samples -- -j4
```

`Step 3.` Run the **Vulkan Samples** application to display the help message

```
./build/linux/app/bin/Release/x86_64/vulkan_samples --help
```

# macOS

## Dependencies

- CMake v3.10+ (known to work with 3.10.2)
- Command Line Tools (CLT) for Xcode `xcode-select --install`
- [Vulkan SDK](https://vulkan.lunarg.com/doc/sdk/latest/mac/getting_started.html) `./install_vulkan.py`
- [CMake Options](#cmake-options)
- [3D models](#3d-models)

## Build with CMake

`Step 1.` The following command will generate the project

```
cmake -H. -Bbuild/mac -DCMAKE_BUILD_TYPE=Release
```

`Step 2.` Build the project

```
cmake --build build/mac --config Release --target vulkan_samples -- -j4
```

`Step 3.` Run the **Vulkan Samples** application to display the help message

```
./build/mac/app/bin/Release/x86_64/vulkan_samples --help
```



# 编译Android平台

## 依赖包如下：

- CMake v3.10+ `CMAKE_ROOT=<WORK_DIR>/cmake`
- JDK 8+ `JAVA_HOME=<WORK_DIR>/java`
- Android NDK r18+ `ANDROID_NDK_ROOT=<WORK_DIR>/android-ndk`
- Android SDK `ANDROID_HOME=<WORK_DIR>/android-sdk`
- Gradle 5+ `GRADLE_HOME=<WORK_DIR>/gradle`

## 环境配置如下：

### 安装Android Studio

  - 请在软件中安装

### 下载Gradle5.6（版本不限制）
  - 地址：https://gradle.org/next-steps/?version=5.6&format=bin

### 配置环境变量---单个变量
  - ADB_EXECUTABLE ：ADB环境变量
  - ANDROID_NDK_HOME ：NDK环境变量
  - ANDROID_SDK_ROOT ：SDK环境变量
  - CMAKE_ROOT ：CMake环境变量
  - JAVA_HOME ：Java环境变量
  - GRADLE_HOME : Gradle环境变量

### 将以上变量全部添加到Path中,如下图：

%ANDROID_NDK_HOME%\toolchains\llvm\prebuilt\windows-x86_64\bin;%CMAKE_ROOT%;%ADB_EXECUTABLE%;%ANDROID_NDK_HOME%;%ANDROID_SDK_ROOT%;%GRADLE_HOME%;


## 使用Gradle编译

`第一步：` 生成Gradle编译必要的build.gradle和settings.gradle文件，命令如下：

```
bldsys\scripts\generate_android_gradle.bat
```

结果就是在根目录新增目录： `build\android_gradle`

`第二步：` 编译项目

```
cd build/android_gradle
gradle.bat assembleDebug
```
编译结果，如下图：

![](compile_result.jpg)

`第三步：` 连接设备调试程序，命令如下

```
adb install build/outputs/apk/debug/vulkan_samples-debug.apk
```

> 另外, 你也可以使用DevEco Studio导入 `build/android_gradle` ，然后按照通用Android项目进行调试即可。



# Building Individual Samples

`Step 1.` When generating cmake set the `VKB_ENTRYPOINTS` flag to `ON`

```
cmake -B"build" -H"." -DVKB_ENTRYPOINTS=ON
```

### CMake

`Step 2.` Set the target to `<samplename>_app`. For example:

```
cmake --build build --config Debug --target afbc_app
```

### Visual Studio

`Step 2.` In the Solution Explorer there now is an Entrypoints folder. Select the `<sample>_app` you would like to run and set it as the `Start Up Project`

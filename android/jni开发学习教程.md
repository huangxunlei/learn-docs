## 什么是JNI，怎么使用

JNI---- Java Native Inteface，他是Java平台的一个特性（并不是Android系统特有的）。其实主要是定义了一些jni函数，让开发者可以通过调用这些函数实现Java代码调用C/C++的代码，C/C++的代码也可以调用Java的代码，这样就可以发挥各个语言的特点了。那么怎么使用Jni呢，一般情况下我们首先是将写好的C/C++代码编译成对应平台的动态库（window一般是dll文件，linux一般是so文件等）

## NDK 入门指南

原生开发套件 (NDK) 是一套工具，使您能够在 Android 应用中使用 C 和 C++ 代码，并提供众多[平台库](https://developer.android.com/ndk/guides/stable_apis)，您可使用这些平台库管理原生 Activity 和访问实体设备组件，例如传感器和轻触输入。NDK 可能不适合大多数 Android 编程初学者，这些初学者只需使用 Java 代码和框架 API 开发应用。然而，如果您需要完成以下一个或多个事项，那么 NDK 就能派上用场：

- 进一步提升设备性能，以降低延迟，或运行计算密集型应用，如游戏或物理模拟。
- 重复使用您自己或其他开发者的 C 或 C++ 库。

您可以在 [Android Studio 2.2 或更高版本](https://developer.android.com/studio)中使用 NDK 将 C 和 C++ 代码编译到原生库中，然后使用 Android Studio 的集成构建系统 Gradle 将原生库打包到 APK 中。Java 代码随后可以通过 [Java 原生接口 (JNI)](http://docs.oracle.com/javase/7/docs/technotes/guides/jni/spec/jniTOC.html) 框架调用原生库中的函数。如需详细了解 Gradle 和 Android 构建系统，请参阅[配置您的版本](https://developer.android.com/studio/build)。

Android Studio 编译原生库的默认构建工具是 [CMake](https://cmake.org/)。由于很多现有项目都使用 ndk-build 构建工具包，因此 Android Studio 也支持 [ndk-build](https://developer.android.com/ndk/guides/ndk-build)。不过，如果您要创建新的原生库，则应使用 CMake。

本指南提供了在 Android Studio 中设置和运行 NDK 所需的信息。如果您没有最新版 Android Studio，请立即[下载并安装](https://developer.android.com/studio)吧。

>
> **实验性 Gradle 版本的用户注意事项**：如果您符合以下任意一种情况，请考虑[迁移到插件版本 2.2.0 或更高版本](http://tools.android.com/tech-docs/new-build-system/gradle-experimental/migrate-to-stable)，并使用 CMake 或 ndk-build 构建原生库：您的原生项目已经使用 CMake 或者 ndk-build；您想要使用稳定版本的 Gradle 构建系统；或者您希望支持插件工具，例如 [CCache](https://ccache.samba.org/)。否则，您可以继续[使用实验性版本的 Gradle 和 Android 插件](http://tools.android.com/tech-docs/new-build-system/gradle-experimental)。
>

## 下载 NDK 和工具

要为您的应用编译和调试原生代码，您需要以下组件：

- Android 原生开发套件 (NDK)：这套工具使您能在 Android 应用中使用 C 和 C++ 代码。
- CMake：一款外部构建工具，可与 Gradle 搭配使用来构建原生库。如果您只计划使用 ndk-build，则不需要此组件。
- LLDB：Android Studio 用于调试原生代码的调试程序。

如需了解如何安装这些组件，请参阅[安装及配置 NDK 和 CMake](https://developer.android.com/studio/projects/install-ndk)。
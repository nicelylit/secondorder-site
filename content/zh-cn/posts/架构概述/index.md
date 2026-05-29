---
title: "架构概述"
date: 2023-01-28
categories: 
  - "阅读笔记"
---

原文链接：[https://source.android.google.cn/docs/core/architecture](https://source.android.google.cn/docs/core/architecture)

> The Android Open System Platform (AOSP) is publicly available and modifiable Android source code. Anyone can download and modify AOSP for their device. AOSP provides a complete and fully functional implementation of the Android mobile platform.

要点：

1. 安卓开源系统平台。

3. 代码开源。

5. 下载修改自由。

7. 提供安卓移动平台完整的功能实现。

评论：

先说开放修改的自由精神，再说项目本身专注于提供什么。

> Note: AOSP can't provide support for apps that require backend services, such as a cloud messaging or advanced location services app. AOSP also doesn't include a full set of end-user apps that might be needed for particular types of devices.

要点：

1. 与云端服务交互的应用不在支持的范围之列。

3. 特定设备定制的应用不在支持的范围之列。

评论：

要说清楚不支持什么，既是划清界限，也是免责申明。

> There are two levels of compatibility for devices implementing AOSP: AOSP compatibility and Android compatibility. An AOSP-compatible device must conform to the list of requirements in the Compatibility Definition Document (CDD). An Android-compatible device must conform to the list of requirements in the CDD and Vendor Software Requirements (VSR) and tests such as those in the Vendor Test Suite (VTS) and Compatibility Test Suite (CTS). For further information on Android compatibility, refer to the Android compatibility program.

要点：

1. 两个层面的兼容性：开源系统平台的兼容性和安卓兼容性。

3. 第一个层面的兼容性：兼容性的定义文档中的需求。

5. 第二个层面的兼容性：除了兼容性的定义文档外，还有供应商的软件需求、供应商的测试套件、兼容性测试套件。

7. 安卓兼容性的计划。

评论：

系统实现要能在各种各样的设备上运行，对安卓平台的维护者、设备硬件的供应商、设备的制造商而言，需要制定标准化的测试计划。测试是服务提供方站在消费方视角审视系统实现的方式，对功能的集成与产品质量的把控至关重要。系统的架构之初，测试计划应当在讨论之列。讨论测试计划是一种典型的逆向工作法。

> AOSP architecture
> 
> The software stack for AOSP contains the following layers:

要点：

1. 软件堆栈。

评论：

分层的体系结构风格是平台类复杂系统的必然选择。作为平台至少要有三种角色，生产者、消费者和平台搭建者。每一层都能从生产消费的角度看，但每一层所使用的沟通语言不同。上层靠近应用和接口，接近人的自然语言，而下层靠近原理和实现，接近人的逻辑和形式语言。平台要为生产者和消费者搭建起恰当的沟通语言。平台的搭建成本不能超过生产者和消费者从中获得的价值和利益。平台搭建的沟通语言要简单而且有效，不能过度创造，也不能创造不足。要做到恰到好处，要充分探究生产者和消费者的需求。这样想来，深入了解一个应用软件或者平台软件，阅读测试计划比阅读架构文档和源代码来得更加高效。测试计划可谓是需求文档的落地版，也是平台功能的大纲。当然架构文档能把设计中的重点和要点汇总在一起，免去了通过测试计划来推测的麻烦。源代码当然是比测试计划和架构文档都更为准确的东西，但也很容易让人迷失在细节中，找不到方向，甚至还可能看错了东西。源代码是要结合测试结果来看的，不过一般测试结果很少记录得事无巨细，通常需要借助人工测试或者调试。

> Following is a list of definitions for terms used in Figure 1:
> 
> Android app
> 
> An app created solely using the Android API. Google Play Store is widely used to find and download Android apps, though there are many other alternatives. In some cases, a device manufacturer might want to preinstall an Android app to support the core functionality of the device. If you're interested in developing Android apps, refer to developers.android.com

要点：

1. 安卓应用。

3. 只用安卓应用编程接口。

5. 查找和下载应用的商店有谷歌官方的也有其它的。

7. 设备制造商可能会预装应用支持一些设备的核心功能。

9. 应用开发的站点不同于此站点。

评论：

这是消费者最为常见的一类安卓应用，也是开发者最为众多的一类。设备制造商往往也会开发一些这类应用，但更多是一些预装应用。

> Privileged app
> 
> An app created using a combination of the Android and system APIs. These apps must be preinstalled as privileged apps on a device.

要点：

1. 特权应用。

3. 结合使用安卓应用编程接口和系统应用编程接口。

5. 必须预装。

评论：

访问系统应用编程接口需要较高权限，因而叫做特权应用。这种应用是必须的，例如桌面启动器和设置。这类应用消费者往往不能卸载。

> Device manufacturer app
> 
> An app created using a combination of the Android API, system API, and direct access to the Android framework implementation. Because a device manufacturer might directly access unstable APIs within the Android framework, these apps must be preinstalled on the device and can be updated only when the device's system software is updated.

要点：

1. 设备制造商应用。

3. 除了特权应用可访问的两类接口外，这类应用还能直接访问安卓框架的实现。

5. 为了使用一些安卓框架中不稳定的编程接口。

7. 也必须预装。

9. 只能跟着系统软件的升级而升级。

评论：

对照来看，特权应用似乎可以单独升级。

> System API
> 
> The System API represents Android APIs available only to partners and OEMs for inclusion in bundled applications. These APIs are marked as @SystemApi in the source code.

要点：

1. 系统应用编程接口。

3. 仅开放给谷歌的合作商和设备制造商。

5. 源代码中打了特殊标记。

评论：

没有提供文档连接。这类编程接口恐怕只能通过源代码查找了。搜索代码库，找到一个[系统属性类](https://cs.android.com/android/platform/superproject/+/master:frameworks/base/core/java/android/os/SystemProperties.java?q=@SystemApi%20&ss=android)就是这类的接口，还有[应用孵化器钩子](https://cs.android.com/android/platform/superproject/+/master:libcore/dalvik/src/main/java/dalvik/system/ZygoteHooks.java?q=@SystemApi%20&ss=android)。

```java
/**
* Gives access to the system properties store. The system properties
* store contains a list of string key-value pairs.
*
* <p>Use this class only for the system properties that are local. e.g., within
* an app, a partition, or a module. For system properties used across the
* boundaries, formally define them in <code>*.sysprop</code> files and use the
* auto-generated methods. For more information, see <a href=
* "https://source.android.com/devices/architecture/sysprops-apis">Implementing
* System Properties as APIs</a>.</p>
*
* {@hide}
*/
@SystemApi
public class SystemProperties {
```

> Android API
> 
> The Android API is the publicly available API for third-party Android app developers. For information on the Android API, refer to Android API reference.

要点：

1. 安卓应用编程接口。

3. 三方应用开发者。

5. 编程接口文档链接。

评论：

三方应该是站在设备制造商角度说的。设备制造商是一方，消费者是另一方，而开发者是第三方。至于谷歌和硬件的供应商应该同属于设备制造商一方，只做内部区分。这部分编程接口是有文档链接的。

> Android framework
> 
> A group of Java classes, interfaces, and other precompiled code upon which apps are built. Portions of the framework are publicly accessible through the use of the Android API. Other portions of the framework are available only to OEMs through the use of the system APIs. Android framework code runs inside an app's process.

要点：

1. 安卓框架。

3. 应用构建所依赖的Java类、接口和其它的预编译后的代码。

5. 安卓框架皆是通过应用编程接口来被使用的。

7. 安卓框架运行在每一个应用的进程中。

评论：

安卓应用编程接口和系统应用编程接口都是接口，而安卓框架是这些接口的实现。对开发者而言，开发过程中所依赖的安卓库就包含了编程接口和安卓框架，打包生成的构建产物应当是不包含这个安卓框架的，而是从孵化器进程（Zygote）复刻应用时候就从固定位置加载的，甚至可能不需要额外加载就已经在孵化器进程中包含了。具体是如何实现的恐怕要看代码。[兼容性定义文档](https://source.android.google.cn/docs/compatibility/13/android-13-cdd?hl=en#37_runtime_compatibility)中没做要求。

> System services
> 
> System services are modular, focused components such as system\_server, SurfaceFlinger, and MediaService. Functionality exposed by Android framework API communicates with system services to access the underlying hardware.

要点：

1. 系统服务。

3. 模块化、功能集中。

5. 举了三例：system\_server, SurfaceFlinger, and MediaService。

7. 暴露出去的安卓框架的编程接口实际是通过系统服务来跟硬件打交道的。

评论：

system\_server中集成了大部分的系统服务。SurfaceFlinger用于图形显示的缓存服务。MediaService用于多媒体的服务。这些服务是从运行的状态来说的。使用编程接口的应用进程是客户端，而这些服务进程是服务器，所以这些服务进程的名字很多以server结尾。服务的注册和查找要通过ServiceManager来实现。由于这些服务进程实际上提供了安卓框架的功能实现，所以这些服务进程也可以称作框架进程。官方在[使用Binder进行进程间通信的文档](https://source.android.google.cn/docs/core/architecture/hidl/binder-ipc?hl=zh-cn#vndbinder)中就是这么称呼的。

> Android runtime (ART)
> 
> A Java runtime environment provided by AOSP. ART performs the translation of the app's bytecode into processor-specific instructions that are executed by the device's runtime environment.

要点：

1. 安卓运行时。

3. 提供Java运行时环境。

5. 安卓运行时将应用字节码翻译成特定处理器的指令。

7. 在设备的运行时环境执行。

评论：

代码有三种表示，源代码、字节码和机器码。源代码支持Java和Kotlin。字节码支持DEX，需要从Java字节码做变换。机器码取决于设备的处理器。从字节码翻译到机器码，若发生在安装阶段称作AOT，若发生在运行阶段称作JIT。早先的Dalvik方式是以虚拟机的方式直接解释执行DEX码，ART方式能够兼容Dalvik方式。

> Hardware abstraction layer (HAL)
> 
> A HAL is an abstraction layer with a standard interface for hardware vendors to implement. HALs allow Android to be agnostic about lower-level driver implementations. Using a HAL lets you implement functionality without affecting or modifying the higher level system. For further information, see the HAL overview.

要点：

1. 硬件抽象层。

3. 抽象了一层标准的接口给供应商实现。

5. 安卓对底层驱动实现保持不可知。

7. 功能实现不影响上层系统。

评论：

这个抽象层的规定是安卓兼容性计划所覆盖的。兼容性的定义文档中有[对硬件兼容性的要求](https://source.android.google.cn/docs/compatibility/13/android-13-cdd?hl=en#7_hardware_compatibility)。[兼容性测试套件](https://source.android.google.cn/docs/compatibility/cts#areas-covered)中没有特别针对硬件抽象层的，但上层的框架应该对此有依赖。[供应商的软件需求](https://source.android.google.cn/docs/core/tests/vts)没有专门的文档。供应商的测试套件在各种硬件专门的目录下，例如wifi 1.5的功能测试放在[hardware/interfaces/wifi/1.5/vts/functional](https://cs.android.com/android/platform/superproject/+/master:hardware/interfaces/wifi/1.5/vts/functional/)下。所有硬件的抽象层定义放在[hardware/interfaces](https://cs.android.com/android/platform/superproject/+/master:hardware/interfaces/)下。

> Native daemons and libraries
> 
> Native daemons in this layer include init, healthd, logd, and storaged. These daemons interact directly with the kernel or other interfaces and don't depend on a userspace-based HAL implementation.
> 
> Native libraries in this layer include libc, liblog, libutils, libbinder, and libselinux. These Native libraries interact directly with the kernel or other interfaces and don't depend on a userspace-based HAL implementation.

要点：

1. 本地守护进程和库。

3. 本地守护进程举了四例：init、healthd、logd、storaged。

5. 守护进程直接跟内核交互。

7. 守护进程跟其它接口交互并不依赖硬件抽象层实现。

9. 本地库举了五个例子：libc、liblog、libutils、libbinder、libselinux。

11. 本地库直接跟内核交互。

13. 本地库跟其它接口交互并不依赖硬件抽象层实现。

评论：

init进程是用户空间的第一个进程，进程ID通常是1。本地守护进程运行在用户空间，都是从init复刻的。守护进程间的通信方式不是Binder。应用进程和框架进程一样可以访问本地守护进程。举例来说，应用进程通过[android.util.Log](https://developer.android.com/reference/android/util/Log)写运行日志，而底层依赖的[liblog](https://cs.android.com/android/platform/superproject/+/master:system/logging/liblog/)使用了Socket通信向[logd发起写日志](https://cs.android.com/android/platform/superproject/+/master:system/logging/liblog/logd_writer.cpp;l=74-79)的请求。本地库可以被不同的可执行程序所共享，应用进程也可以，比方说应用进程通过JNI在libandroid\_runtime中使用liblog进行写日志。

```java
// android.jar
// frameworks/base/core/java/android/util/Log.java

public static int w(@Nullable String tag, @NonNull String msg) {
    return println_native(LOG_ID_MAIN, WARN, tag, msg);
}

@UnsupportedAppUsage
public static native int println_native(int bufID, int priority, String tag, String msg);
```

```cpp
// libandroid_runtime.so
// frameworks/base/core/jni/android_util_Log.cpp

static jint android_util_Log_println_native(JNIEnv* env, jobject clazz,
        jint bufID, jint priority, jstring tagObj, jstring msgObj)
{
    // 删掉了一些代码

    int res = __android_log_buf_write(bufID, (android_LogPriority)priority, tag, msg);

    if (tag != NULL)
        env->ReleaseStringUTFChars(tagObj, tag);
    env->ReleaseStringUTFChars(msgObj, msg);

    return res;
}

static const JNINativeMethod gMethods[] = {
    /* name, signature, funcPtr */
    { "isLoggable", "(Ljava/lang/String;I)Z", (void*) android_util_Log_isLoggable },
    { "println_native", "(IILjava/lang/String;Ljava/lang/String;)I", (void*) android_util_Log_println_native },
    { "logger_entry_max_payload_native", "()I", (void*) android_util_Log_logger_entry_max_payload_native },
};
```

```cpp
// liblog.so
// system/logging/liblog/logger_write.cpp

static __android_logger_function user_set_logger_function = nullptr;

static __android_logger_function get_logger_function() {
    if (user_set_logger_function != nullptr) {
        return user_set_logger_function;
    }

    static __android_logger_function default_logger_function = []() {
#if __ANDROID__
    if (get_file_logger_path() != nullptr) {
        return file_logger;
    } else {
        return __android_log_logd_logger;
    }
#else
    return file_logger;
#endif
    }();
    return default_logger_function;
}

void __android_log_write_log_message(__android_log_message* log_message) {
    ErrnoRestorer errno_restorer;

    // 删掉了一些代码

    get_logger_function()(log_message);
}

int __android_log_buf_write(int bufID, int prio, const char* tag, const char* msg) {
    ErrnoRestorer errno_restorer;

    if (!__android_log_is_loggable(prio, tag, ANDROID_LOG_VERBOSE)) {
        return -EPERM;
    }

    __android_log_message log_message = {
        sizeof(__android_log_message), bufID, prio, tag, nullptr, 0, msg};
    __android_log_write_log_message(&log_message);
    return 1;
}

void __android_log_logd_logger(const struct __android_log_message* log_message) {
    int buffer_id = log_message->buffer_id == LOG_ID_DEFAULT ? LOG_ID_MAIN : log_message->buffer_id;

    // 删掉了一些代码

    write_to_log(static_cast<log_id_t>(buffer_id), vec, 3);
}

static int write_to_log(log_id_t log_id, struct iovec* vec, size_t nr) {
    // 删掉了一些代码

    ret = LogdWrite(log_id, &ts, vec, nr);
    PmsgWrite(log_id, &ts, vec, nr);
    return ret;
}
```

```cpp
// liblog.so
// system/logging/liblog/logd_writer.cpp

int LogdWrite(log_id_t logId, struct timespec* ts, struct iovec* vec, size_t nr) {
    ssize_t ret;
    static const unsigned headerLength = 1;
    struct iovec newVec[nr + headerLength];
    android_log_header_t header;
    size_t i, payloadSize;
    static atomic_int dropped;

    LogdSocket& logd_socket =
        logId == LOG_ID_SECURITY ? LogdSocket::BlockingSocket() : LogdSocket::NonBlockingSocket();
```

> Kernel
> 
> The kernel is the central part of any operating system and talks to the underlying hardware on a device. Where possible, the AOSP kernel is split into hardware-agnostic modules and vendor-specific modules. For a description, including definitions, of AOSP kernel components, refer to the Kernel overview.

要点：

1. 内核。

3. 内核是任何一个操作系统的核心。

5. 直接跟设备硬件打交道。

7. 安卓开源系统平台内核分为两部分，一部分与硬件无关，另一部分是特定供应商的模块。

9. 内核概述链接。

评论：

依据生产过程，定制内核需要三方参与，AOSP项目维护者、硬件单片机和外设的供应商、原始设备生产商。拿我手上的华为荣耀Play 6T来说，谷歌公司是AOSP项目维护者，联发科和华为是硬件供应商、荣耀终端有限公司是原始设备生产商。依据[通用内核映像（GKI）项目](https://source.android.google.cn/docs/core/architecture)的说法，内核由四部分构成，Linux长期支持版（LTS）内核、安卓公共内核、供应商内核、生产商设备内核。

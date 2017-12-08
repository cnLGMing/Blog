这里借前辈的一句话：

> IDE 和 makefile 代表了两种不同的思想：IDE 强调的是简化计算机与用户的交互；而 makefile 体现的是自动化。

# Android.mk 简介

前段时间，在项目中遇到需要编写 Android.mk 文件，所以学习了下如何编写 Android.mk 。下面我们来看看 `维基百科` 和 `百度百科` 对其的主要解释。

## **维基百科**

> 	在软件开发中，**make** 是一个工具程序，经由读取叫做 **makefile** 的文件，自动化建构软件。它是一种转化文件形式的工具，转换的目标称为 **target**；与此同时，它也检查文件的依赖关系，如果需要的话，它会调用一些外部软件来完成任务。它的依赖关系检查系统非常简单，主要根据依赖文件的修改时间进行判断。大多数情况下，它被用来编译，生成，然后把结果代码连接起来生成可执行文件或者库文件。它使用叫做 **makefile** 的文件来确定一个 target 文件的依赖关系，然后把生成这个 target 的相关命令传给 **shell** 去执行。

## **百度百科**

> 	一个工程中的源文件不计数，其按类型、功能、模块分别放在若干个目录中，makefile 定义了一系列的规则来指定，哪些文件需要先编译，哪些文件需要后编译，哪些文件需要重新编译，甚至于进行更复杂的功能操作，因为 makefile 就像一个 Shell 脚本一样，其中也可以执行操作系统的命令。
>
> 	makefile 带来的好处就是——“自动化编译”，一旦写好，只需要一个 make 命令，整个工程完全自动编译，极大的提高了软件开发的效率。make 是一个命令工具，是一个解释 makefile 中指令的命令工具，一般来说，大多数的 IDE 都有这个命令，比如：Delphi 的 make，Visual C++ 的 nmake，Linux 下 GNU 的 make。可见，makefile 都成为了一种在工程方面的编译方法。
>
> 	Make 工具最主要也是最基本的功能就是通过 makefile 文件来描述源程序之间的相互关系并自动维护编译工作。而 makefile 文件需要按照某种语法进行编写，文件中需要说明如何编译各个源文件并连接生成可执行文件，并要求定义源文件之间的依赖关系。makefile 文件是许多编译器--包括 Windows NT 下的编译器--维护编译信息的常用方法，只是在集成开发环境中，用户通过友好的界面修改 makefile 文件而已。

---

通过上述两个摘要，我们大概能够了解到：Android  使用的是 `GNU` 的 `make` ，而 `Android.mk` 是 makefile 的一小部分，因此 Android.mk 是 GNU 的 makefile 格式。正如上面所说，makefile 很强大的。

我们开始吧！

> 注：因我是开发 **Android TV** ，平台是 **MStar** ，所以以下代码可能跟 Android 原生的有所差异，但绝不影响学习！

# Android.mk 案例讲解

这里先讲解一个源码中的 `Android.mk`，下面是 `SystemUI` 的 `Android.mk` 原文件，进行讲解 :

（注：案例有点长，耐心看完必有收获）

```c++
LOCAL_PATH:= $(call my-dir)
# Android.mk 文件必须以 LOCAL_PATH 开始。目的是在开发树中查找源文件。
# 宏my-dir，则由 Build System 提供。返回包含 Android.mk 的目录路径。

include $(CLEAR_VARS)
# CLEAR_VARS 变量由 Build System 提供。并指定 GNU Makefile 负责清理很多 LOCAL_xxx.
# 例如：LOCAL_MODULE, LOCAL_SRC_FILES, LOCAL_STATIC_LIBRARIES 等等。但不清理 LOCAL_PATH.
# 这个清理动作是必须的，因为所有的编译控制文件由同一个 GNU Makefile 解析和执行，其变量是全局的。
# 所以清理后才能避免相互影响。

LOCAL_MODULE_TAGS := optional
# 这个值将关系到这个模组是否被编译并放进 system.image
# optional		指该模块在所有版本下都编译
# user  		指该模块只在user版本下才编译
# eng   		指该模块只在eng版本下才编译
# tests 		指该模块只在tests版本下才编译

LOCAL_SRC_FILES := $(call all-java-files-under, src) \
    src/com/android/systemui/EventLogTags.logtags
# 若包含 java 源码时，可以调用 all-java-files-under 得到。(可获得 local_path 目录下的所有java文件)
# 若涉及 C/C++ 时，必须包含将要编译进模块中的 C/C++ 源文件。不必列出头文件，build System 会自动找出依赖文件。
# all-java-files-under 宏的定义是在 build/core/definitions.mk 中
  
LOCAL_STATIC_JAVA_LIBRARIES := Keyguard
# APK 程序所需要的静态 jar 包名

LOCAL_JAVA_LIBRARIES := telephony-common \
                        com.mstar.android
# 生成的 jar 包名

LOCAL_PACKAGE_NAME := SystemUI
# 生成的 APK 名和包名，唯一且不能有空格

LOCAL_CERTIFICATE := platform
# 指定签名文件的文件名，这里表示需要 platform key 签名

LOCAL_PRIVILEGED_MODULE := true
# 声明 APK 需要放在 /system/priv-app 下

LOCAL_PROGUARD_FLAG_FILES := proguard.flags
# 指定不需要混淆的 native 方法与变量的 proguard.flags 文件
# Java的字节码非常容易反编译。为了保护 Java 源代码，通常会对 class 文件进行混淆。

LOCAL_RESOURCE_DIR := \
    frameworks/base/packages/Keyguard/res \
    $(LOCAL_PATH)/res
# 指定相应的 res 
  
LOCAL_AAPT_FLAGS := --auto-add-overlay --extra-packages com.android.keyguard
# 引用包外资源

include $(BUILD_PACKAGE)
# include $(BUILD_PACKAGE)        表示编译成 APK
# include $(BUILD_STATIC_LIBRARY) 表示编译成静态库
# include $(BUILD_SHARED_LIBRARY) 表示编译成动态库
# include $(BUILD_EXECUTABLE)     表示编译成可执行程序   
  
include $(call all-makefiles-under,$(LOCAL_PATH))
# 加载当前目录下的所有 makefile 文件。
# all-makefiles-under 会返回一个位于当前 my-dir 路径的子目录中的所有 Android.mk的列表。
```

到此，案例已经解析完。

# 编译

## 编译成一个 APK（无依赖）

```c
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
 
# Build all java files in the java subdirectory-->直译（建立在java子目录中的所有Java文件）
LOCAL_SRC_FILES := $(call all-subdir-java-files)
 
# Name of the APK to build-->直译（创建APK的名称）
LOCAL_PACKAGE_NAME := LocalPackage
 
# Tell it to build an APK-->直译（告诉它来建立一个APK）
include $(BUILD_PACKAGE)
```

## 编译成一个 APK（依赖一个Java Jar包）

```c
# 依赖一个静态Java Jar包
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
 
# List of static libraries to include in the package,
# "static-library"为所需要的静态 jar 包名
LOCAL_STATIC_JAVA_LIBRARIES := static-library	
 
# Build all java files in the java subdirectory
LOCAL_SRC_FILES := $(call all-subdir-java-files)
 
# Name of the APK to build
LOCAL_PACKAGE_NAME := LocalPackage
 
# Tell it to build an APK
include $(BUILD_PACKAGE)
  
# 依赖一个非静态Java Jar包
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
 
# Build all java files in the java subdirectory
LOCAL_SRC_FILES := $(call all-subdir-java-files)
 
# Any libraries that this library depends on
# "android.test.runner"为所需要添加的非静态 Jar 包名
LOCAL_JAVA_LIBRARIES := android.test.runner
 
# The name of the jar file to create
LOCAL_MODULE := sample
 
# Build a static jar file.
include $(BUILD_STATIC_JAVA_LIBRARY)
```

## 编译成一个需要 key 签名的 APK

```c
# 需要用平台的key签名的应用程序
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
 
# Build all java files in the java subdirectory
LOCAL_SRC_FILES := $(call all-subdir-java-files)
 
# Name of the APK to build
LOCAL_PACKAGE_NAME := LocalPackage
 
LOCAL_CERTIFICATE := platform
 
# Tell it to build an APK
include $(BUILD_PACKAGE)

需要用特定key签名的应用程序
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
 
# Build all java files in the java subdirectory
LOCAL_SRC_FILES := $(call all-subdir-java-files)
 
# Name of the APK to build
LOCAL_PACKAGE_NAME := LocalPackage
 
LOCAL_CERTIFICATE := vendor/example/certs/app
 
# Tell it to build an APK
include $(BUILD_PACKAGE)
```

## 添加一个预编译 APK

```c
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
 
# Module name should match apk name to be installed.
LOCAL_MODULE := LocalModuleName
LOCAL_SRC_FILES := $(LOCAL_MODULE).apk
LOCAL_MODULE_CLASS := APPS
LOCAL_MODULE_SUFFIX := $(COMMON_ANDROID_PACKAGE_SUFFIX)
 
include $(BUILD_PREBUILT)
```

在实践后，已完成 **Android.mk** 的编写。希望通过学习，能够帮助你一点点~



参考资料：
[android编译系统的makefile文件Android.mk写法](http://www.cnblogs.com/hesiming/archive/2011/03/15/1984444.html)
[android makefile(android.mk)分析（序）](http://blog.csdn.net/chief1985/article/details/4699831)

---
> **文章若有不对之处，欢迎指正，谢谢~**
> **邮箱: liuguangmingcn@163.com**
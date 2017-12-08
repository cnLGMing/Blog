简单描述 Android 源码的根目录下各文件夹的功能
--- 

|      根目录名称      | 描述                                       |
| :-------------: | :--------------------------------------- |
|       abi       | abi 相关代码。abi：Application Binary Interface 的简称，是应用程序二进制接口 |
|       art       | Android 5.0 之后的运行环境。注意和 Dalvik VM 区分     |
|     bionic      | bionic C 库                               |
|    bootable     | 启动引导相关的代码                                |
|      build      | 存放系统编译规则及 generic 等 基础开发配置包              |
|       cts       | Android 兼容性测试套件标准                        |
|     dalvik      | Dalvik VM                                |
|   development   | 应用程序开发相关                                 |
|     device      | 设备相关代码                                   |
|      docs       | 介绍开源的相关文档                                |
|    external     | Android 使用的一些开源的模组                       |
|    framework    | 核心框架 —— Java 及 C++语言，是Android 应用程序的框架    |
|       gdk       | 即时通信模块                                   |
|    hardware     | 主要是硬件适配层 HAL 代码                          |
|     kernel      | Linux 的内核文件                              |
|     libcore     | 核心库相关                                    |
| libnativehelper | 是 Support functions for Android's class libraries 的缩写，表示动态库，是实现的 JNI 库的基础 |
|       ndk       | ndk 相关代码                                 |
|       out       | 编译完成后的代码输出到此目录                           |
|     package     | 应用程序包                                    |
|       pdk       | Plug Development Kit 的缩写，是本地开发套件         |
|    prebuilts    | x86 和 arm 架构下预编译的一些资源文件                  |
|       sdk       | SDK 及模拟器                                 |
|     system      | 文件系统和应用及组件，是用 C 语言实现的                    |
|      tools      | 工具文件夹                                    |
|     vender      | 厂商定制代码                                   |
|    Makefile     | 全局的 Makefile                             |

---
> **文章若有不对之处，欢迎指正，谢谢~**
> **邮箱: liuguangmingcn@163.com**
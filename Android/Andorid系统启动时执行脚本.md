如何在 Android 开机时，执行一个 Shell 脚本。相信你看了本文章后，会有所所获。

# 前言

这周在做项目时，遇到一个需求：在 Android 开机时，执行一个 Shell 脚本。

在这里假如：在 Android 开机时，在 `data` 目录下创建一个名为 `cnLGMing` 的文件夹。

# 分析

1. 在相应的目录新建一个 Shell 脚本文件。在这里假如 Shell 脚本的文件名为：`myshell.sh`，代码如下：

   ```shell
   #!/system/bin/sh
   mkdir /data/cnLGMing
   ```

2. 需要在 Android 编译时，将这个 Shell 脚本文件复制到相应的目录，并且最终添加到镜像中去

3. 需要在 Android 启动时，执行这个 Shell 脚本文件

基本就这样，感觉写起来应该是挺简单的。可在实现的过程中，还是遇到了坑。在这里总结并分享下，最终实现的方法。

>  注：因我是开发 **Android TV** ，平台是 **MStar** ，所以以下代码可能跟 Android 原生的有所差异，但思路是一样的，不影响学习！

# 步骤

1. 在相应的路径新增 Shell 脚本文件。
   我这边路径的是：`device\mstar\pitaya\` ，**不同的平台，文件路径也是不同的，请按实际情况。** 

   - **第一个大坑：**
     我是在 Windows 下编写的 Shell 脚本后，复制过去的，在后面烧录镜像后始终不执行。
     后来排查发现，Shell 脚本出现 **换行时多一个** `^M` 的换行符问题。解决方法：
     - 新增的 Shell 脚本，建议在 Linux 或 Unix 环境下编写。避免出现 `^M` 
     - 如果在 Windows 下写，换行符需要切换为Unix 格式

2. 在编译 Android 系统时，复制到相应的目录，并且最终添加到镜像中去。

   - 我这边的路径是：`device\mstar\pitaya\device.mk`，找到 `PRODUCT_COPY_FILES` 此字段，添加代码

     ```c
     PRODUCT_COPY_FILES += \
     	device/mstar/pitaya/myshell.sh:system/etc/myshell.sh
     ```

     **解释：**原文件路径(根据实际情况写)`:`目标路径(只需修改你的脚本文件名，即  system/etc/脚本文件名)

3. 在 Android 启动时，自动执行这个脚本文件。

   - 我这边的路径是：`device\mstar\pitaya\init.rc`，**不同的平台，文件路径也是不同的，请按实际情况。**

     - 在最末尾添加 `service` ，代码如下：(注意：脚本名称，在烧录镜像后，可通过 `start 服务名字` 手动执行

       ```c
       service myshell system/etc/myshell.sh
           class main
           user root
           group root
           oneshot
       ```

     - 在 `on init` (若没找到，查找下)，末尾加上权限，代码如下：(注意：脚本名称，权限请根据实际情况分配

       ```c
       chown root shell /system/etc/myshell.sh
       chmod 0550 /system/etc/myshell.sh
       ```

       	当我天真的以为，这样就可以的时候，编译、烧录！结果却是权限不够！好气啊，不是明明写了权限了吗？怎么还是权限不够呢？

   - **第二个大坑：**
     在跟踪 init.rc 时，发现：`system 分区` 是以**只读**的形式进行挂载的。那怎么可能会有执行权限嘛！
     经过 Google 发现：Android 在编译过程中，除了复制文件外，还有一个赋权限的操作。
     路径：`system/core/include/private/android_filesystem_config.h`

     - 在 fs_path_config android_files[] 数组添加：
       (注意：脚本名称， `00755` 权限请根据实际情况分配，根据上下行判断是否需要第4个参数 `0` 

       ```c++
       { 00755, AID_ROOT, AID_SHELL, 0, "system/etc/myshell.sh" },
       ```

4. 编译、烧录、验证。

---


**还有一个问题：** 通常调试，会通过查看 log 信息。可是在 Shell 脚本使用 `echo` 进行信息输出，然而即使脚本成功执行，依然信息依然没有输出。原因不明，若你知道，请留言指教。thx



------

> **文章若有不对之处，欢迎在 `Issues` 指正，谢谢~**
>
> **邮箱：liuguangmingcn@163.com**


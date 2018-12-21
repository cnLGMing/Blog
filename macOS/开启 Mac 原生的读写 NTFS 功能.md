# 前言
换 MacBook Pro 已经有半年多了，始终不习惯 MBP 插入移动硬盘或 U 盘时的操作，不像 Windows 那么简单。为了做到跟 Windows 一样，在 Google 和知乎上面了解了一些相关的知识，现在分享下。
​	
先说明下我自己的情况：
- 公司开发用 Windows
- 自己开发用 Mac
- 一个 Samsung 固态硬盘，用于 Windows 和 Mac （或和同事）之间的数据交换
- 一个 Lenovo 机械硬盘，用于自己的 Mac 备份数据
       经常使用移动硬盘进行数据交换和对 Mac 进行备份（买的是乞丐版，最常做的就是备份）。之前一直使用 [Mounty](http://enjoygineering.com/mounty/) 软件开启 Mac 写 NTFS 的功能，虽然也没有出现知乎上网友说的`丢数据`的情况，但是始终觉得太麻烦，不喜欢。现在提供一种方法进行开启 Mac 读写 NTFS 功能。亲测可用！

**测试系统：**
![系统版本号等信息](https://github.com/cnLGMing/Blog/blob/master/Pictures/version.png?raw=true)

# 步骤
1. 打开 `终端`
2. 输入`sudo nano /etc/fstab`，进行更新 `/etc/fstab` 文件
   - 输入自己电脑的密码（没有密码的会跳过去），输入电脑密码后出现以下内容，进行配置：
     `LABEL=移动硬盘/U盘的名字 none ntfs rw,auto,nobrowse`
     ![配置](https://github.com/cnLGMing/Blog/blob/master/Pictures/config.png?raw=true)
   - 参数解释：
     - `Samsung` 和 `Lenovo` 是移动硬盘/U盘的名字，建议不要有中文。
     - 后面的 `ntfs rw` 表示把这个分区挂载为可读写的 `NTFS格式`，最后 `nobrowse` 非常重要，因为这个代表了在 `Finder` 里不显示这个分区，这个选项非常重要，如果不打开的话挂载是不会成功的。
     - 注意：如果你只有一个，只需添加一个即可，**不能有空行！**
3. 完成后，按 `control + x` 会出现要不要保存的字样，请按 **Y** ，然后回车
4. 输入 `sudo ln -s /Volumes/移动硬盘或U盘的名字 ~/Desktop/移动硬盘或U盘的名字`，创建桌面快捷方式
   - 因为刚刚创建的分区是不会在 Finder 里不显示的
   - 如果你想创建多个不一样的，只需要在执行此命令时，设置不同的名字，即可
5. 重启电脑
6. 插入移动硬盘/U盘，即可。
7. 当然，你也可以选择将其拉到 Finder 的侧边栏
    ![移动](https://github.com/cnLGMing/Blog/blob/master/Pictures/move.png?raw=true)

到此，你就可以像在 Windows 一样操作移动硬盘/U盘了。用这种方式打开的是系统原生的 NTFS 功能，稳定实用，比第三方工具方便多了。

---

> **尚未发现此方法对 Mac 有不良影响! 若出现不良影响, 本人概不负责! 请慎重使用!!!**
>
> **文章若有不对之处，欢迎在 `Issues` 指正，谢谢~**
>
> **邮箱：liuguangmingcn@163.com**
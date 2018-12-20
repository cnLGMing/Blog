[TOC]

# 前言

一直想把公司项目从 SVN 迁到 Git , 无奈后端同事并没有兴趣, 只能先把自己的前端迁到 Git...第一次在开发环境下, 拥有 Root 权限进行所有操作, 毕竟 Linux 不是很好, 有点虚, 总担心会误操作, 那就真的是从删库到跑路了.....

那么, 不多说, 说干就干吧! 

# 步骤

## 安装 Git

1. 查看版本信息

   ```shell
   [root@cnLGMing ~]# git --version
   ```

2. 如果已安装 Git, 并且版本过低, 则移除已有的 Git 版本...CentOS 6.5自带 Git , 版本1.7.x, 所以我选择卸载, 重新安装高版本

    ```shell
    [root@cnLGMing ~]# yum remove git -y
    ```



3. 下载源码包 **(强烈建议, 服务器安装的 Git 版本 和 客户端安装版本 一致)** 

   ```shell
   [root@cnLGMing ~]# wget https://www.kernel.org/pub/software/scm/git/git-2.12.2.tar.gz
   ```

4. 解压源码包

   ```shell
   [root@cnLGMing ~]# tar -zxf git-2.12.2.tar.gz
   ```

5. 进入解压后的文件夹

   ```shell
   [root@cnLGMing ~]# cd git-2.12.2
   ```

6. 执行配置信息

    ```shell
    [root@cnLGMing /root/git-2.12.2]# ./configure
    ```

7. 编译

   ```shell
   [root@cnLGMing /root/git-2.12.2]# make -j4 && make -j4 install
   注: -j4 表示使用 4 个线程进行编译, 根据自己服务器而定. 编译后将会安装在 /usr/local/bin 下面
   ```

   - 若遇到报错信息:  `cache.h:44: error: expected specifier-qualifier-list before ‘z_stream’`, 需要安装 **zlib**

     ```shell
     [root@cnLGMing ~]# yum install zlib-devel
     ```

8. 添加系统关联

   ```shell
   [root@cnLGMing /root/git-2.12.2]# ln -s /usr/local/bin/git /usr/bin/
   ```

9. 查看版本信息, 若能查到, 则表示安装成功


## 创建 Git 用户

1. 创建 Git 用户, 设置用户名 `git`

    ```sh
    [root@cnLGMing ~]# sudo adduser -d /home/git -m git
    ```

2. 根据用户名 `git` 设置密码, 回车输入密码

   ```shell
   [root@cnLGMing ~]# sudo passwd git
   ```

3. 为了安全考虑，禁止 Git 用户通过使用普通的 Bash 或者 XShell 等类似程序登录服务器

   - 查看 `git-shell` 的路径

   ```shell
   [root@cnLGMing ~]# which git-shell
   ```
   - 编辑 `passwd` 文件

   ```shell
   [root@cnLGMing ~]# sudo vim /etc/passwd
   ```
   - 找到刚刚创建的 Git 用户名(这里是 `git`), 将 `/home/git:`  后面的替换为 `git-shell` 的路径

   ```shell
   git:x:500:500::/home/git:/bin/bash
   # 修改为
   git:x:500:500::/home/git:/usr/local/bin/git-shell
   ```

## 配置公钥

1. 客户端创建公钥, 打开 `Git Bash`

    ```shell
    # -t 指定密钥类型，默认即 rsa ，可以省略
    # -C 设置注释文字，通常是你的公司邮箱
    # 接着几个回车 即可
    $ ssh-keygen -t rsa -C  'xxx@xxx.com'
    ```

2. 将公钥复制到 Git 服务器

   - 若 /home/git 目录下, 没有 .ssh 文件, 则创建; 若已存在, 则跳过此步.

      ```shell
      [root@cnLGMing /]# mkdir /home/git/.ssh
      ```

   - 将上一步生成的公钥追加到 `authorized_keys文件` 中

      ```shell
      # 进入 .ssh 目录
      [root@cnLGMing /]# cd /home/git/.ssh
      # 创建一个 authorized_keys 文件
      [root@cnLGMing /home/git/.ssh]# vi authorized_keys
      # 将上一步生成的公钥追加到 authorized_keys 文件中
      [root@cnLGMing /home/git/.ssh]# cat id_rsa.pub >> authorized_keys
      ```

3. 设置权限

   ```shell
   # 进入 .ssh 目录
   [root@cnLGMing /]# cd /home/git
   # 设置 .ssh 的权限
   [root@cnLGMing /home/git]# chmod 700 .ssh
   # 设置 authorized_keys 的权限
   [root@cnLGMing /home/git]# chmod 600 .ssh/authorized_keys
   ```


## 创建 Git 仓库

1. 建立仓库目录

    ```shell
    # 创建版本库目录
    [root@cnLGMing /home/git]# mkdir repo
    # 将文件夹的所有权划分给git用户
    [root@cnLGMing /home/git]# sudo chown -R git:git /home/git/repo
    [root@cnLGMing /home/git]# cd repo/
    # ProjectName 为项目名
    [root@cnLGMing /home/git/repo]# sudo git init --bare ProjectName.git
    [root@cnLGMing /home/git/repo]# sudo chown -R git:git /home/git/repo/ProjectName.git
    ```
      [为什么用 **git init --bare**, 而不是直接用 **git init** ? ](https://github.com/cnLGMing/Blog/issues/20)


# 本地与远程建立关联

## 方法一: 本地没有现成仓库, 那么克隆回本地

1. 将远程仓库克隆回本地, 使用 `Git Bash`

    ```shell
    $ cd 切换本地目标路径
    $ git clone git@服务器IP:/home/git/repo/ProjectName.git
    # 输入密码....
    ```

2. 创建一个文件, 提交, 并创建主分支

   ```shell
   $ touch demo.txt
   $ git add demo.txt
   $ git commit -m "demo"
   $ git push --tags --set-upstream origin master
   ```


## 方法二: 本地已有现成仓库, 那么将本地仓库和远程仓库建立关联

1. 打开 `Git Bash` , 切换到目标路径下, 新建一个文件夹并初始化为 Git 仓库

   ```shell
   # 创建一个文件夹
   $ mkdir ProjectName
   # 进入仓库
   $ cd ProjectName
   # 初始化仓库
   $ git init
   ```

2. 建立连接

   ```shell
   # 注意 服务器 Git 仓库的路径
   $ git remote add origin git@服务器IP:/home/git/repo/ProjectName.git
   ```

## RSA 认证问题

1. 如果每次操作都需要输入密码, 那么请确认 Git 服务器是否开启 RSA 认证

   ```shell
   # 编辑配置文件, 将下面三个注释打开 即可
   [root@cnLGMing /]# vi /etc/ssh/sshd_config
   RSAAuthentication yes
   PubkeyAuthentication yes
   AuthorizedKeysFile  .ssh/authorized_keys
   ```



CentOS 6.5 安装 Git 2.12.2 服务器 到这就结束了...

看似很流畅, 其实我安装卸载重复了至少3次, 才确定无误的记录下来...

在操作过程中也学习到了很多知识, 如文中所说, 在服务器初始化仓库为什么使用`git init --bare` , 而在客户端时有直接用 `git init` , 两者有什么区别?  这个我将在 [下一篇文章](https://github.com/cnLGMing/Blog/issues/20) 进行阐述.



------

> **文章若有不对之处，欢迎在 `Issues` 指正，谢谢~**
>
> **邮箱：liuguangmingcn@163.com**


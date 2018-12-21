# 下载 & 安装 & 启动

## Windows 篇 *(Windows 10 x64)*

1. Nginx [官网下载](http://nginx.org/en/download.html)，建议下载稳定版，目前是 [ nginx/Windows-1.14.2](http://nginx.org/download/nginx-1.14.2.zip)；

2. 将下载的压缩包 `nginx-1.14.2.zip `，解压指定的路径（我解压在 `C:\Develop` 路径下）；

3. 使用 CMD 命令，进入到安装路径 `C:\Develop\nginx-1.14.2` 下，使用以下命令，若可查看到 nginx 的版本号，即表示安装成功。

   ```cmd
   >nginx -v
   ```

4. 使用以下命令，启动 Nginx 服务（会看到一个一闪而过的窗口）。

   ```cmd
   >start nginx
   ```

5. 打开浏览器，输入 [http://localhost](http://localhost/) 回车，若返回以下界面，则表示 Nginx 服务启动成功。

   ># Welcome to nginx!
   >
   >If you see this page, the nginx web server is successfully installed and working. Further configuration is required.
   >
   >For online documentation and support please refer to [nginx.org](http://nginx.org/).
   >Commercial support is available at [nginx.com](http://nginx.com/).
   >
   >*Thank you for using nginx.*

---

**Tips：**

- Nginx 的默认配置文件被命名为 `nginx.conf`，在其安装路径下的 `conf` 文件夹中。

## Linux 篇 *(CentOS 7.2 x64)*

> Linux 有两种安装方式：
>
> 1. 使用 `yum` 从资源库在线下载安装
> 2. 下载源码，自行编译安装

我使用的是第一种方式，理由很简单，方便快捷，不易出错。

1. 处理下载源

   ```shell
   # sudo rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
   ```

2. 安装

   ```shell
   # sudo yum install -y nginx
   ```

3. 启动

   ```shell
   # systemctl nginx start
   ```

4. 打开浏览器，输入 远程服务器的 ip，若返回以下界面，则表示 Nginx 服务启动成功

   ># Welcome to nginx!
   >
   >If you see this page, the nginx web server is successfully installed and working. Further configuration is required.
   >
   >For online documentation and support please refer to [nginx.org](http://nginx.org/).
   >Commercial support is available at [nginx.com](http://nginx.com/).
   >
   >*Thank you for using nginx.*

---

**Tips：**

- Nginx 的默认路径：`whereis nginx`，

  > nginx: /usr/sbin/nginx /usr/lib64/nginx /etc/nginx /usr/share/nginx /usr/share/man/man8/nginx.8.gz

  以下是 `Nginx` 的默认路径： 

  - 使用 `Homebrew` 安装的路径：`/usr/local/Cellar/nginx`

  - Nginx 配置路径：`/etc/nginx/`
  - PID 目录：`/var/run/nginx.pid`
  - 错误日志：`/var/log/nginx/error.log `
  - 访问日志：`/var/log/nginx/access.log `
  - 默认站点目录：`/usr/share/nginx/html`
  - Nginx 配置文件路径：
    - `/etc/nginx/nginx.conf` 
    - `/etc/nginx/conf.d/default.conf` 

- 设置开机自启：

  ```shell
  # sudo systemctl enable nginx.service
  ```


## macOS 篇 *(macOS Mojave 10.14)*

因我的 mbp 之前已经安装了 `Homebrew`，进行软件包管理，所以直接使用 `brew` 搜索并安装 `Nginx`。当然，你也可以下载源码自己编译打包安装。

> [在 macOS 下安装 Homebrew](https://github.com/cnLGMing/Blog/blob/28cdc0966cf674758586c25795b1e6c0a173bed5/macOS/%E5%9C%A8%20macOS%20%E4%B8%8B%E5%AE%89%E8%A3%85%20Homebrew.md)

1. 搜索

   ```bash
   $ brew search nginx 
   ```

2. 在线安装

   ```shell
   $ brew install nginx
   ```

3. 启动

   ```shell
   $ nginx
   ```

4. 打开浏览器，输入 [http://localhost:8080](http://localhost:8080) 回车，若返回以下界面，则表示 Nginx 服务启动成功。

   > # Welcome to nginx!
   >
   > If you see this page, the nginx web server is successfully installed and working. Further configuration is required.
   >
   > For online documentation and support please refer to [nginx.org](http://nginx.org/).
   > Commercial support is available at [nginx.com](http://nginx.com/).
   >
   > *Thank you for using nginx.*

**Tips：**

- 在 Windows、Linux 中，Nginx 默认使用的是 `80 端口`，而在 macOS 中，其默认使用的是 `8080端口`。

- Nginx 的默认路径：

  - Nginx 配置路径：`/usr/local/etc/nginx `
  - PID 目录：`/usr/local/var/run/nginx.pid`
  - 错误日志：`/usr/local/var/log/nginx/error.log `
  - 访问日志：`/usr/local/var/log/nginx/access.log `
  - 默认站点目录：`/usr/local/var/www `
  - Nginx 配置文件路径：`/usr/local/etc/nginx/nginx.conf` 

- 为了方便修改 Nginx 的配置文件，可以[创建软链接](https://github.com/cnLGMing/Blog/blob/master/Linux/创建软链接.md)。

  ```shell
  $ ln -s /usr/local/etc/nginx ~/Develop/nginx-1.14.2
  ```

# 我遇到的问题

> 以下问题及解决方式不一定同样适合你，务必根据实际 `Log` 进行解决错误。

- **问题1：** 在 Linux 中启动 Nginx 服务时，报错。错误信息如下：

    > Redirecting to /bin/systemctl start  nginx.service
    > Job for nginx.service failed because the control process exited with error code. See "systemctl status nginx.service" and "journalctl -xe" for details.

    - **原因：** 通过 `netstat -tuplen` 查看端口号发现，`80 端口` 被占用，而恰恰 Nginx 默认使用的也是 `80 端口`。
    - **解决方式：**
      - 停止占用 `80 端口` 的服务，在确认该服务无关紧要之后，我直接 kill 了该服务进程。
      - 当不能停止占用 `80 端口` 的服务时，只能修改 Nginx 的端口号了。

- **问题2：** 在 Linux 中，修改 Nginx 的配置文件 `nginx.conf` 后，使用 `service nginx -s reload` 重新加载配置文件时，报如下错误：

  > The service command supports only basic LSB actions (start, stop, restart, try-restart, reload, force-reload, status). For other actions, please try to use systemctl.

  - **原因：** CentOS 7.x 开始，CentOS 开始使用 `systemd` 服务来代替 `daemon`，原来管理系统启动和管理系统服务的相关命令全部由 `systemctl 命令` 来代替。`service 命令` 只保留下了极少部分使用，大部分命令都要改用 `systemctl` 使用。 
  - **解决方式：**
    - 使用 `nginx -s reload` 命令，即可重新加载配置文件
    - 使用 `systemctl nginx -s reload` 命令进行重新加载配置文件

---

> **文章若有不对之处，欢迎在 `Issues` 指正，谢谢~**
>
> **邮箱：liuguangmingcn@163.com**
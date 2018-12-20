# Nginx 常用命令

- 启动 Nginx

  ```shell
  $ start nginx
  ```

- 快速关闭 Nginx，并终止web服务

  ``` shell
  $ nginx -s stop
  ```

- 平稳关闭 Nginx，保存相关信息，有安排的结束web服务，优雅关闭

  ``` shell
  $ nginx -s quit
  ```

- 修改 Nginx 相关配置后，需要重新加载配置

  ``` shell
  $ nginx -s reload
  ```

- 重新打开日志文件

  ``` shell
  $ nginx -s reopen
  ```

- 为 Nginx 指定一个配置文件，来代替默认配置文件`nginx.conf`

  ``` shell
  $ nginx -c filename
  ```

- 不运行 Nginx，仅检查配置文件的语法正确性，并尝试打开配置文件中所引用到的文件

  ``` shell
  $ nginx -t
  ```

- 显示 Nginx 的版本号

  ```shell
  $ nginx -v
  ```

- 显示 Nginx 的版本，编译器版本和配置参数

  ```shell
  $ nginx -V
  ```
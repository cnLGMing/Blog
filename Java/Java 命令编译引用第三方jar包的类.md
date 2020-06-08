# Java 命令编译引用第三方 jar 包的类

> 最后更新日期：2020-06-08
>
> 当前环境：Windows 10、CentOS 7.5、Java 8
>
> 前提：默认 Java 环境已配置

写了个定期获取数据的功能，其只有一个 Java 文件，含有 main 函数，引用了多个第三方 jar 包。在 IDEA 中写完并调试完后，丢到 CentOS 服务器中，通过 Java 命令怎么都编译不了、执行不了。太久太久没有用命令行编译执行，已经忘光了(ಥ_ಥ) ....

最终的实现方式：

> 当前路径：在该 Java 文件的路径下，需要引入的第三方 jar 包（aaa.jar、bbb.jar、ccc.jar）

- Linux 环境

  - 若 jar 包与 Java 文件同路径：

    ```shell
    # 编译
    # javac -cp aaa.jar:bbb.jar:ccc.jar App.java
    # 执行
    # java -cp .:aaa.jar:bbb.jar:ccc.jar App
    ```

  - 若 jar 包（在 demo 目录下 ）与 Java 文件不同路径：

    ```shell
    # 编译
    # javac -cp /demo/aaa.jar:/demo/bbb.jar:/demo/ccc.jar App.java
    # 执行
    # java -cp .:/demo/aaa.jar:/demo/bbb.jar:/demo/ccc.jar App
    ```

  > Tips：jar 包之间使用英文冒号:分割

  

- Windows 环境
  - 待写








之前一直都是在自己的电脑(客户端)上建立仓库, 一直以为初始化仓库就直接用 `git init` 这个命令即可. 直至昨天在 [CentOS 6.5 安装 Git 2.12.2 服务器](https://github.com/cnLGMing/Blog/issues/19) 过程中, 才暴露出这个错误. 第一次使用 `git init --bare` 进行创建一个仓库. 然后,  就想弄明白 `git init` 和 `git init --bare` 创建仓库的区别, 便有了这篇文章...

### git init

`git init` 命令用于创建一个已存在项目的仓库或者是一个空项目的仓库. 在创建完成后, 会在项目的根目录新增一个 `.git` 的文件夹, 并不会影响其他工作目录.  通常我们将这样的仓库, 称为 **"普通库"**.  用户可以在该仓库进行几乎所有的 Git 相关操作. 

在一个空项目的 Git 普通库目录结构, 如下: 

![空-普通库](https://github.com/cnLGMing/Blog/blob/master/Pictures/git-init.png?raw=true)

### git init --bare

`git init --bare` 命令用于创建一个没有工作目录的仓库.  也就是说, 其没有像我们常见的本地工作目录层级. 通常我们将这样的仓库, 称为 **"裸库"** .  这是因为该仓库只保存 Git 历史操作信息, 不允许用户在该仓库进行 Git 操作. 假如用户试图在改仓库进行 Git 操作, 例如: 新建一个分支并切换: 

```shell
$ git checkout -b test
# 报错
fatal: This operation must be run in a work tree
```

没错, 你要是执行 Git 操作, 将会得到这个错误 **This operation must be run in a work tree**. 

在一个空项目的 Git 裸库目录结构, 如下:

![裸库](https://github.com/cnLGMing/Blog/blob/master/Pictures/git-init-bare.png?raw=true) 

### 总结

将服务器的 Git 仓库创建为 `裸库 `, 就避免了多人同时修改一个分支并提交时出错的问题了. 为了更好的协同开发, 通常我们将 `服务端 Git 仓库` 创建成 `裸库`，可以将 `裸库` 看成是记录一系列操作的数据库，而不是一个开发环境。也就是说，对于所有的 Git 工作流，`服务端 Git 仓库` 是 `裸库`，客户端本地仓库是 `普通库` .  





------

> **文章若有不对之处，欢迎在 `Issues` 指正，谢谢~**
>
> **邮箱：liuguangmingcn@163.com**




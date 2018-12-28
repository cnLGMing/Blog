# 在 macOS 下安装 Homebrew

> Homebrew 安装程序的过程中需要用到 Xcode，所以请确保电脑已安装 Xcode。如果没有安装，可以到 App Store 中免费下载安装。 

## 安装 Homebrew

```shell
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

## 卸载 Homebrew

```shell
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"
```

## 常用命令

```shell
# 搜索包
$ brew search 软件名称

# 安装包
$ brew install 软件名称

# 查看软件信息
$ brew info 软件名称

# 卸载软件
$ brew uninstall 软件名称

# 显示已安装的软件
$ brew list

# 查看brew的帮助
$ brew –help

# 更新所有软件(含 Homebrew)
$ brew update

# 更新软件
$ brew upgrade 软件名称

# 查看哪些软件可以更新
$ brew outdated

# 查看软件是否有新版本
$ brew outdated 软件名称

# 清理不需要的版本极其安装包缓存
$ brew cleanup 软件名称
```

**Tips：**

- `Homebrew` 安装成功后，会创建一个目录 `/usr/local/Cellar`， `Homebrew` 将程序安装在此路径下。

------

> **文章若有不对之处，欢迎在 `Issues` 指正，谢谢~**
>
> **邮箱：liuguangmingcn@163.com**


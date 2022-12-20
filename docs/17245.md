# 网络开发自制软件

> 原文：<https://dev.to/kasparsd/homebrew-for-web-development-23nj>

[家酿](https://brew.sh/)是一款非常棒的 macOS 命令行软件管理解决方案(类似于 [MacPorts](https://www.macports.org/) )。它可以安装网络开发所需的几乎所有软件，并保持其有序( `/usr/local`下的[)并通过一个命令进行更新。](https://docs.brew.sh/FAQ)

## 为什么牛逼？

*   快速安装软件包，无需通过 web 浏览器下载和运行安装程序。
*   从源头构建一切(除非从木桶安装),使其安全。将下载文件的校验和与已知参考进行比较。
*   将所有内容放在`/usr/local`下，并且不使用 root 权限来增加安全性和限制系统访问。

## 使用它

以下是如何安装一些最流行的软件包。在大多数情况下，你只需要运行`brew install PACKAGENAME`或`brew cask install PACKAGENAME`来获得作为二进制文件发布的包。

## 开发工具

安装 [Node.js](https://nodejs.org/en/) 和 [npm](https://www.npmjs.com/) :

```
brew install node 
```

安装[作曲](https://getcomposer.org/) :

```
brew install composer 
```

## 编辑人员

安装 Visual Studio 代码:

```
brew cask install visual-studio-code 
```

安装 Atom:

```
brew cask install atom 
```

## 虚拟开发环境

安装 VirtualBox:

```
brew cask install virtualbox 
```

安装流浪者:

```
brew cask install vagrant 
```

安装坞站和坞站复合:

```
brew install docker docker-compose 
```

## 有用的命令

更新家酿和包信息:

```
brew update 
```

更新所有已安装的软件包:

```
brew upgrade 
```

移除旧版本的软件包:

```
brew cleanup 
```

列出所有已安装的软件包:

```
brew list 
```
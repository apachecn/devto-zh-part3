# 自动化 PowerRuby 安装

> 原文：<https://dev.to/worksofbarry/automating-powerruby-installation-2ga7>

我一直在与一个客户合作，他一直在使用旧版本的 PowerRuby，最近想升级到 PR CE2(社区版 2.0)。

对于那些不知道什么是 PowerRuby 的人来说，它是为 IBM i 操作系统构建的 Ruby 和 Ruby on Rails。他们编译 Ruby 和一些专门用于 IBM i 的 gems。

*   这里可以看到 [PowerRuby 网站](https://powerruby.com/)
*   你可以在他们的 GitHub 页面上看到 PowerRuby 指南和二进制文件

组织上的[第一指南解释说你需要下载保存文件并手动运行`RSTLICPGM`(恢复许可程序)命令——ew！2018 年，](https://github.com/PowerRuby/DE_train_01)[我们让 IBM i 上的 yum](https://worksofbarry.github.io/rpg-git-book/3-tooling) 用`yum`命令自动安装像 Ruby runtime (Node.js，python 等)这样的包。PowerRuby 没有采用这种方法，这很可悲。

当然，要做到这一点，您需要从默认的 IBM yum 资源库
中获得的`make`和`curl`

```
yum install curl.ppc64 make-gnu.ppc64 
```

我想要一种简单的方法让 PowerRuby 在 IBM i 上运行，所以我创建了一个 makefile 来运行从 web 上安装 PR 许可程序所需的所有命令。此过程将创建保存文件，下载内容并使用`RSTLICPGM` :
恢复程序

```
BIN_LIB=PR_TEST

all: download install

download:
    system "CRTLIB LIB($(BIN_LIB))"
    system "CRTSAVF FILE($(BIN_LIB)/PRUBY_BASE)"
    system "CRTSAVF FILE($(BIN_LIB)/PRUBY_0001)"
    system "CRTSAVF FILE($(BIN_LIB)/PRUBY_0006)"
    curl -L -k -o /QSYS.LIB/$(BIN_LIB).LIB/PRUBY_BASE.FILE https://github.com/PowerRuby/DE_train_01/releases/download/V2R0M0/pruby_base.savf
    curl -L -k -o /QSYS.LIB/$(BIN_LIB).LIB/PRUBY_0001.FILE https://github.com/PowerRuby/DE_train_01/releases/download/V2R0M0/pruby_0001.savf
    curl -L -k -o /QSYS.LIB/$(BIN_LIB).LIB/PRUBY_0006.FILE https://github.com/PowerRuby/DE_train_01/releases/download/V2R0M0/pruby_0006.savf

install:
    system "RSTLICPGM LICPGM(1PRUBY1) DEV(*SAVF) LNG(2924) SAVF($(BIN_LIB)/PRUBY_BASE)"
    system "RSTLICPGM LICPGM(1PRUBY1) DEV(*SAVF) LNG(2924) OPTION(1) SAVF($(BIN_LIB)/PRUBY_0001)"
    system "RSTLICPGM LICPGM(1PRUBY1) DEV(*SAVF) LNG(2924) OPTION(6) SAVF($(BIN_LIB)/PRUBY_0006)" 
```

然后要安装 PowerRuby CE2，开发者所要做的就是运行`make`！

### 可选的临时演员

PowerRuby 还提供了一个 GCC 编译器版本(8.2)，这是他们用来编译 PR CE2 和一些 gem 的版本。如果你想[编译‘原生’宝石](https://github.com/PowerRuby/DE_train_02)也需要它。

```
gcc:
    -system "CRTSAVF FILE($(BIN_LIB)/PRUBY_0002)"
    curl -L -k -o /QSYS.LIB/$(BIN_LIB).LIB/PRUBY_0002.FILE https://github.com/PowerRuby/DE_train_02/releases/download/V2R0M0/pruby_0002.savf
    system "RSTLICPGM LICPGM(1PRUBY1) DEV(*SAVF) LNG(2924) OPTION(2) SAVF($(BIN_LIB)/PRUBY_0002)" 
```
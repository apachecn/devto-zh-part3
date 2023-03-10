# 远离阿尔卑斯山

> 原文：<https://dev.to/asyazwan/moving-away-from-alpine-30n4>

由于 docker 的出现，当容器的移动开始受到重视时，人们对针对单个进程优化的轻量级基础映像有了真正的需求，这与您的典型操作系统不同。进入 Alpine，一个像 3MB 一样小的轻量级 linux 发行版！它还附带了足够好的[包库](https://pkgs.alpinelinux.org/packages)，这对采用有很大帮助。不幸的是，由于我将概述的原因，我们已经停止在可能的时候采用 Alpine。

## 采用高山

在工作中，我们很早就开始在开发、CI 甚至生产中采用 Alpine。一切都很好，除了不顺利的时候。事实证明，要获得 Alpine repository 中不容易获得的包需要做大量的工作。你看，Alpine 使用 [musl](http://www.musl-libc.org/) libc 而不是 [glibc](https://www.gnu.org/software/libc/) 并且大多数流行发行版使用后者。例如，用 Alpine 编译的东西不能在 Ubuntu 上使用，反之亦然。当您需要的所有包都在`main` & `community` alpine 存储库中时，那就太棒了。如果不是这样，你必须自己构建它，并且*希望*依赖项是可用的，或者至少容易构建(相对于 musl)。

## 遗失包裹

有一天，在 docker 映像构建阶段，我们的 CI 开始失败。`mysql` package(只是一个指向 mariadb 的兼容性包)突然不见了。我们发布了 bug 报告:[Bug # 8030:Alpine v 3.3 中 mysql 和 mysql-client 包缺失 x86_64 架构- Alpine Linux - Alpine Linux 开发](https://bugs.alpinelinux.org/issues/8030)。值得称赞的是，这个问题在一天之内就解决了，但是这个问题让我们开始质疑。

## 落选版本

我在[之前的一篇文章](https://ibnusani.com/article/php-curl-segfault/)中提到过这个问题，这基本上是关于在 Alpine 中锁定软件包版本的困难。我会继续，但我认为[这个家伙](https://medium.com/@stschindler/the-problem-with-docker-and-alpines-package-pinning-18346593e891)经历过同样的情况，有着同样的想法。推荐阅读。

## 包不可用时

工作中的开发人员需要为我们的实验分支使用 PHP [V8js](http://php.net/manual/en/book.v8js.php) ,所以我必须为我们基于 alpine 的图像获得扩展。有人为我解决了大部分麻烦，他在这个 Github [要点](https://gist.github.com/tylerchr/15a74b05944cfb90729db6a51265b6c9)中详细描述了他的发现。基本上我们必须编译 [GN](https://gn.googlesource.com/gn/) ，下载 v8 源代码，然后用 musl 编译。即使制定了步骤，这也不是一次顺利的经历。考虑到未来的更新和补丁，这种方法对我们来说是不可持续的。

## 系统日志限制

开发者严重依赖于通过 syslog(挂载`/dev/log`)的应用日志，Alpine 默认使用 busybox [syslog](https://wiki.alpinelinux.org/wiki/Syslog) 。问题是，消息被截断在 1024 个字符的限制，这是非常小的。根本问题是 musl 有 1024 个系统日志缓冲区的硬编码限制，这比最初的 [256](https://git.musl-libc.org/cgit/musl/commit/src/misc/syslog.c?id=3f65494a4cb2544eb16af3fa64a161bd8142f487) (!)限制但还是不够。作为默认设置，这没有意义，而且我也找不到一种简单的方式来配置这个*。*

 *## 最后一根稻草

去年 7 月左右，ubuntu 正式发布了用于云/容器的最小 Ubuntu 映像。

> 这些映像的大小不到标准 Ubuntu 服务器映像的 50%,启动速度提高了 40%。

不仅基本图像只有 29MB 大小，而且你还可以使用所有的 apt 包！我们之前在 Alpine 上遇到的所有问题使得我们很容易切换到 ubuntu 作为我们的基础映像，到目前为止我们对这个切换很满意。这并不是说阿尔卑斯山不好。只是不适合我们。

### 编辑:我需要澄清，这篇文章不是关于什么是更好的选择。相反，这是关于抛弃阿尔卑斯山。ubuntu-minimal 的好替代品肯定不多。:)*
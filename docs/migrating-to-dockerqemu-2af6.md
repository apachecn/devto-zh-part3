# 迁移到 Docker/Qemu

> 原文：<https://dev.to/jeikabu/migrating-to-dockerqemu-2af6>

我们正在检修我们的 CI 测试管道。在[之前的一篇文章](//./reusable-windows-vms-with-vagrant-2h5c)中，我们讨论了如何使用 vagger 来管理 Windows 虚拟机。

这里我们使用 [docker](https://docker.io/) 和 [QEMU](https://www.qemu.org/) 来扩展对 ARM64 的测试。我们的例子包括[锈](https://www.rust-lang.org/)和 C#/。NET，但是它很容易适应您喜欢的任何语言/框架。

## QEMU

我们一直想将测试扩展到更“奇特”的平台，尤其是 ARM64/aarch64。这个有趣的 Travis-CI 问题让我们朝着那个方向前进。他们最初用的是 Debian 的“Jessie”，但是“Stretch”是[第一个用 ARM64 支持的](https://wiki.debian.org/LTS)。

在 Ubuntu 18.04 主机上，首先[在 Ubuntu 上安装 docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/)然后:

```
docker run --rm --privileged multiarch/qemu-user-static:register
docker run -it --rm multiarch/debian-debootstrap:arm64-stretch 
```

Enter fullscreen mode Exit fullscreen mode

在产生的 shell 中运行`uname -a` :

```
root@f190ea8ef8cc:/# uname -a
Linux f190ea8ef8cc 4.15.0-43-generic #46-Ubuntu SMP Thu Dec 6 14:45:28 UTC 2018 aarch64 GNU/Linux 
```

Enter fullscreen mode Exit fullscreen mode

注 **aarch64** 。相当狡猾。

这里的“魔术”是将任意可执行格式转发给用户空间应用程序的[Linux binfmt](https://en.wikipedia.org/wiki/Binfmt_misc)——在本例中是 QEMU。

更多阅读:

*   [https://www . tomaz . me/2013/12/02/running-Travis-ci-tests-on-arm . html](https://www.tomaz.me/2013/12/02/running-travis-ci-tests-on-arm.html)
*   [https://blog . hypriot . com/post/setup-simple-ci-pipeline-for-arm-images/](https://blog.hypriot.com/post/setup-simple-ci-pipeline-for-arm-images/)

### 生锈

我们的第一只豚鼠是我们的铁锈库之一。[这里有一个 Dockerfile 文件](https://github.com/jeikabu/runng/blob/docker_arm64/Dockerfile)用于在 ARM64 环境下安装 Rust:

```
FROM multiarch/debian-debootstrap:arm64-stretch

RUN apt-get update && apt-get install -y \
    build-essential \
    ca-certificates \
    clang \
    cmake \
    curl

ARG RUST_VER=1.32.0

# Make sure rustup and cargo are in PATH
ENV PATH "~/.cargo/bin:$PATH"
# Install rustup, skip latest toolchain and get a specific version
RUN curl https://sh.rustup.rs -sSf | sh -s -- -y --default-toolchain none && \
    ~/.cargo/bin/rustup default $RUST_VER 
```

Enter fullscreen mode Exit fullscreen mode

*   `clang`和`cmake`是[运行](https://github.com/jeikabu/runng)的先决条件
*   `ca-certificates`是处理卷曲衰竭用的:

```
ERROR: The certificate of `XXX' is not trusted 
```

Enter fullscreen mode Exit fullscreen mode

这已经被推送到 Docker Hub 作为 [`jeikabu/debian-rust`](https://cloud.docker.com/u/jeikabu/repository/docker/jeikabu/debian-rust) 所以你可以用
试试

```
docker run -it --rm jeikabu/debian-rust:arm64v8-stretch-1.32.0 
```

Enter fullscreen mode Exit fullscreen mode

使用`cargo`运行我们的测试:

```
docker run -t -v $(pwd):/usr/src jeikabu/debian-rust:arm64v8-stretch-1.32.0 /bin/bash -c "cd /usr/src; cargo test" 
```

Enter fullscreen mode Exit fullscreen mode

注意:如果所有测试成功，退出代码是`0`。

### [T1。网络核心](#net-core)

微软有大量的相关图片。网芯。特别令人感兴趣的是[的 docker 文件。NET Core 3.0 预览版针对 ARM64](https://github.com/dotnet/dotnet-docker/blob/master/3.0/sdk/stretch/arm64v8/Dockerfile) 。使用它通过 qemu 再次启用 ARM64 上的测试:

```
FROM multiarch/debian-debootstrap:arm64-stretch

RUN apt-get update && apt-get install -y \
    curl \
    gnupg \
    icu-devtools

# From:
# https://github.com/dotnet/dotnet-docker/blob/master/3.0/sdk/stretch/arm64v8/Dockerfile
ENV DOTNET_SDK_VERSION 3.0.100-preview-010184
RUN curl -SL --output dotnet.tar.gz https://dotnetcli.blob.core.windows.net/dotnet/Sdk/$DOTNET_SDK_VERSION/dotnet-sdk-$DOTNET_SDK_VERSION-linux-arm64.tar.gz \
    && dotnet_sha512='3fd7338fdbcc194cdc4a7472a0639189830aba4f653726094a85469b383bd3dc005e3dad4427fee398f76b40b415cbd21b462bd68af21169b283f44325598305' \
    && echo "$dotnet_sha512 dotnet.tar.gz" | sha512sum -c - \
    && mkdir -p /usr/share/dotnet \
    && tar -zxf dotnet.tar.gz -C /usr/share/dotnet \
    && rm dotnet.tar.gz \
    && ln -s /usr/share/dotnet/dotnet /usr/bin/dotnet 
```

Enter fullscreen mode Exit fullscreen mode

如果你不在北美，你可能想使用[发布说明](https://github.com/dotnet/core/tree/master/release-notes)中的下载网址。在中国，从 https://download.visualstudio.microsoft.com[的](https://download.visualstudio.microsoft.com)下载比使用 https://dotnetcli.blob.core.windows.net[的](https://dotnetcli.blob.core.windows.net)快很多倍(就像在官方 docker 文件中一样)。YMMV。

这个形象也被推送到 Docker Hub 上成为 [`jeikabu/debian-dotnet-sdk`](https://cloud.docker.com/u/jeikabu/repository/docker/jeikabu/debian-dotnet-sdk) 。

让我们运行我们的测试:

```
docker run -t -v $(pwd):/usr/src jeikabu/debian-dotnet-sdk:arm64v8-stretch /bin/bash -c "cd /usr/src; dotnet test" 
```

Enter fullscreen mode Exit fullscreen mode

`icu-devtools`包裹在那里，否则你会得到:

```
root@79106a1b502f:/# cd /usr/src
root@864d67ab40bb:/usr/src# dotnet clean
qemu: Unsupported syscall: 283
FailFast:
Couldn't find a valid ICU package installed on the system. Set the configuration flag System.Globalization.Invariant to true if you want to run with no globalization support.

   at System.Environment.FailFast(System.String)
   at System.Globalization.GlobalizationMode.GetGlobalizationInvariantMode()
   at System.Globalization.GlobalizationMode..cctor()
   at System.Globalization.CultureData.CreateCultureWithInvariantData()
   at System.Globalization.CultureData.get_Invariant()
   at System.Globalization.CultureData.GetCultureData(System.String, Boolean)
   at System.Globalization.CultureInfo..ctor(System.String, Boolean)
   at System.Reflection.RuntimeAssembly.GetLocale()
   at System.Reflection.RuntimeAssembly.GetName(Boolean)
   at System.Reflection.Assembly.GetName()
   at System.Diagnostics.Tracing.EventPipeController.GetAppName()
   at System.Diagnostics.Tracing.EventPipeController..ctor()
   at System.Diagnostics.Tracing.EventPipeController.Initialize()
   at System.StartupHookProvider.ProcessStartupHooks()
qemu: uncaught target signal 6 (Aborted) - core dumped
Aborted (core dumped) 
```

Enter fullscreen mode Exit fullscreen mode

有几个博客和 StackOverflow 问题包含的解决方案似乎是 RHEL 的[网络文档的变体。我们不想创建学究式的小图片，所以添加`icu-devtools`包就足够了。](https://github.com/dotnet/core/blob/master/Documentation/build-and-install-rhel6-prerequisites.md)

## 特拉维斯

这应该可以在大多数 Linux 系统上运行。让我们试着将它作为我们 [Travis CI](https://travis-ci.org/) 的一部分来运行。

[创建`qemu_arm64.sh`](https://github.com/subor/nng.NETCore/blob/master/scripts/qemu_arm64.sh) 来运行测试:

```
#!/usr/bin/env bash

if [["$TRAVIS_OS_NAME" == "linux"]] || [["$OSTYPE" == "linux-gnu"]]; then docker run --rm --privileged multiarch/qemu-user-static:register --reset
    docker run -t -v $(pwd):/usr/src jeikabu/debian-dotnet-sdk:arm64v8-stretch /bin/bash -c "cd /usr/src && dotnet build && dotnet test --filter 'platform!=windows' --verbosity normal"
fi 
```

Enter fullscreen mode Exit fullscreen mode

`.travis.yml`中的:

```
after_success:
  - ./scripts/qemu_arm64.sh 
```

Enter fullscreen mode Exit fullscreen mode

注意:如果任何测试失败，将返回一个非`0`值，但是 [`after_success`不会使构建](https://docs.travis-ci.com/user/job-lifecycle/#breaking-the-build)失败。如果我们决定保持这一点，一旦 ARM64 稳定下来，我们可以将其转移到更早的阶段，让它在构建中失败。

这是一个使用小项目的非常小的例子，但在 Travis 身上仍然花费了 5 分多钟。较大的项目可能会与[构建超时](https://docs.travis-ci.com/user/customizing-the-build/#build-timeouts)相冲突。再一次，YMMV。
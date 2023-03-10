# 树莓 Pi 无树莓/铁锈底漆

> 原文：<https://dev.to/jeikabu/raspberry-pi-zero-raspbian-rust-primer-3aj6>

有点冲动的我点了一份[树莓派零度“W”](https://www.raspberrypi.org/products/raspberry-pi-zero/)。不知道我怎么到现在才听说这个(Zero 在 2015 年首次推出，2017 年推出带 Wifi/蓝牙的“Zero W”)，但我一看到它就不得不拥有一个:

[![](img/9669c323582d2d0da9eb802844e0c937.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QX68BEp7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/raspi_zero.jpg)

看那个小家伙。

早在 2000 年初，我做了一个项目，用无线“微尘”运行 TinyOS:

[![](img/4877871aaca58c62ba9460d02d792690.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pIDHuOAl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/mote.jpg)

我不记得我们的确切型号了，但它们似乎都有大约 4**KB**的 SRAM 和 512**KB**的闪存。20 年后的今天，Zero 是一个绝对的大块头，拥有 512 MB 内存，微 SD 插槽(这里是 16 GB)，并配有单核 32 位 ARM CPU。

设备:

*   [树莓派零度](https://www.raspberrypi.org/products/raspberry-pi-zero-w/)——最好是“W”或“WH”
*   微型 SD 卡(至少 8GB)
*   微型 SD 读卡器，或带适配器的 SD 读卡器
*   “常规”USB-A(公)到 micro-B(公)电缆
    *   附带许多东西(例如，除了最新的 Android 设备之外，所有设备都使用 USB-C)
    *   从阅读论坛帖子来看，一些充电电缆可能没有数据线，不会工作。如果找不到设备，请尝试另一根电缆。

可选，但偶尔有用:

*   带有 USB micro-B 连接器或其他 USB-A 转 micro-B 电缆的电源适配器，如上所示
    *   您可以通过从 PC 到 Zero 上标有“USB”的微型 USB 端口的电缆为 Zero 供电，但使用单独的电源很方便，因此您可以拔下第一个电源
*   迷你 HDMI 转标准/A 型 HDMI 适配器或电缆(将 Zero 连接到外部显示器)
*   USB-A(母)到 micro-B(公) **OTG** 适配器(用于连接/键盘鼠标到零)

# 设置

Raspbian 的基本安装类似于使用 [Pi 3](//./raspberry-pi-3-rasbian-primer-i6d) :

1.  [下载 Raspbian](https://www.raspberrypi.org/downloads/)
2.  [安装](https://www.raspberrypi.org/documentation/installation/installing-images/README.md)

在带 wifi 的零 W/WH 之前，有一种方法可以配置完全无外设的系统，并将其接入网络，这种方法仍然非常有用:

*   [树莓派 OTG 模式](https://gist.github.com/gbaman/50b6cca61dd1c3f88f41)
*   [建立 OTG 码头的快速通道](https://gist.github.com/gbaman/975e2db164b3ca2b51ae11e45e8fd40a)
*   [水果指南](https://learn.adafruit.com/turning-your-raspberry-pi-zero-into-a-usb-gadget/overview)

对于 macOS:

```
# Write raspbian image to SD in /dev/disk2
diskutil unmountDisk /dev/disk2
sudo dd bs=1m if=~/Downloads/2018-11-13-raspbian-stretch.img of=/dev/rdisk2 conv=sync
cd /Volumes/boot
# Enable SSH
touch ssh
vim config.txt # Add to the bottom: dtoverlay=dwc2
vim cmdline.txt # After `rootwait` add: modules-load=dwc2,g_ether
sudo diskutil eject /dev/rdisk2 
```

Enter fullscreen mode Exit fullscreen mode

将 SD 卡插入 Pi Zero，将您的电脑连接到标有“USB”的微型 USB 端口(最靠近设备中部的端口)以启动电脑。

大约 30 秒后，在**系统偏好>网络**你应该看到: [![](img/f795ac7b9b2d4ac90c3bc1bf975dcdd6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MwwfLgz3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/osx_rndis_gadget.png)

连接方式:

```
# Setup ssh key authentication. Default password is `raspberry`
ssh-copy-id pi@raspberrypi.local
ssh -X pi@raspberrypi.local 
```

Enter fullscreen mode Exit fullscreen mode

如果你有多个设备，或者你在某个时候刷新，你会看到非常可怕的:

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@ WARNING: POSSIBLE DNS SPOOFING DETECTED! @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
The ECDSA host key for raspberrypi.local has changed,
and the key for the corresponding IP address 169.254.28.197
is unknown. This could either mean that
DNS SPOOFING is happening or the IP address for the host
and its host key have changed at the same time.
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@ WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED! @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX.
Please contact your system administrator.
Add correct host key in /Users/XXX/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /Users/XXX/.ssh/known_hosts:37
ECDSA host key for raspberrypi.local has changed and you have requested strict checking.
Host key verification failed. 
```

Enter fullscreen mode Exit fullscreen mode

像`Offending ECDSA key in /Users/XXX/.ssh/known_hosts:37`一样找线。这意味着你应该编辑`~/.ssh/known_hosts`并删除线`37`来修复这个讨厌的东西。

接下来，配置互联网共享:

[![](img/c3fd76fa949d74514f5949b627a7a16b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eBJ36Hgr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/osx_rndis_sharing.png)

要上网，打开**系统偏好设置>共享**。要从共享您的连接，请选择您电脑的互联网连接(可能是局域网或 Wifi)，使用将**连接到电脑，请选择`RNDIS/Ethernet Gadget`。**

请注意，如果 wifi 使用 802.1x 认证(如我们的办公室)，连接共享将无法工作。

## 软件

一个重要的区别是零是 **armv6** (有 VFPv2 但没有氖- [像树莓 Pi 1](https://en.wikipedia.org/wiki/Raspberry_Pi#Specifications) )而不是 **armv7** 像树莓 Pi 3。这意味着可用的。NET Core、PowerShell、VS Code 等二进制不会“就”工作。我们可能会考虑构建 32 位。NET 的源代码，但单核在 1 GHz 和只有 512 MB 内存的情况下，我怀疑 VS 代码能否运行良好。

还记得什么时候[我们提倡学习 vim 或者 emacs](///2019/03/21/raspi_3.html#software) 吗？猜猜在低端设备如 Zero 上有什么好用的？

谢天谢地，铁锈也管用！我们真正需要的是铁锈。

# 交叉编译

很容易通过 rustup 在 Zero 上安装 Rust:

```
curl https://sh.rustup.rs -sSf | sh 
```

Enter fullscreen mode Exit fullscreen mode

让我们建造[running](https://github.com/jeikabu/runng)。

在 2017 年的 MacBook Pro 上`cargo clean; cargo build`大约需要 20 **秒**。

在圆周率零点上，需要 66 **分钟**。*哎哟*！

这看起来像是交叉编译的工作！这个可能是关于用 Rust 交叉编译的最完整的文档，但是它有点过时了。我们将看看如何使用 [musl](https://www.musl-libc.org/) 来创建独立的二进制文件。毕竟，谁想和共享库打交道呢？

最佳方法取决于您的情况:

*   哪些机器/设备/操作系统可用
*   构建环境的复杂性/要求
*   熟悉码头工人/货物(尤其是装箱时)

以下是我将介绍的选项的摘要:

| 方法 | 时间(分:秒) | 笔记 |
| --- | --- | --- |
| 基准 x86_64 | twenty past zero | MBP；双核 2.5 GHz 英特尔酷睿 i7 处理器，带 16 GB 2133 MHz LPDDR3 |
| musl-cross | twenty past zero | MBP；双核 2.5 GHz 英特尔酷睿 i7 处理器，带 16 GB 2133 MHz LPDDR3 |
| 锈十字 | twenty six past zero | i5-7500 上的 hyper-V @ 3.4 GHz；运行 Ubuntu 18.04 的虚拟机，具有 2 个 VCPU 和 4 GB 内存 |
| Pi 3 B | fifteen past two/a quarter past two |  |
| Qemu | fifteen past five/a quarter past five | i5-7500 上的 hyper-V @ 3.4 GHz；运行 Ubuntu 18.04 的虚拟机，具有 2 个 VCPU 和 4 GB 内存 |
| 零 | 66:00 |  |

这些并不意味着可以直接比较，因为我没有相同的硬件来运行所有的东西。但是，它应该让您对便利性/灵活性与构建时间之间的权衡有所了解。

## MacOS

### 十字

在 MAC 上，我们可以使用相同的方法来瞄准 [AWS Lambda](//./rust-on-lambda-4ae8) 或我们的 [Pi 3 pm2.5 监视器](//./raspberry-pi-pm2510-air-quality-monitor-2ede)。让我们从简单的东西开始，一个“hello world”二进制文件。在 Mac 上(不是在 Zero 上):

```
cargo new --bin hello_muscles
cd hello_muscles/ 
```

Enter fullscreen mode Exit fullscreen mode

在`hello_muscles/.cargo/config`(或`~/.cargo/config`)中添加:

```
[target.arm-unknown-linux-musleabihf]
linker = "arm-linux-musleabihf-gcc" 
```

Enter fullscreen mode Exit fullscreen mode

```
# Install arm/x86_64 linux-musl cross-compilers
brew install FiloSottile/musl-cross/musl-cross --with-arm-hf
# Install rust target
rustup target add arm-unknown-linux-musleabihf
# Build
CROSS_COMPILE=arm-linux-musleabihf- cargo build --release --target arm-unknown-linux-musleabihf 
```

Enter fullscreen mode Exit fullscreen mode

检查`target/arm-unknown-linux-musleabihf/release/`的输出:

```
$ file target/arm-unknown-linux-musleabihf/release/hello_muscles

target/arm-unknown-linux-musleabihf/release/hello_muscles: ELF 32-bit LSB executable, ARM, EABI5 version 1 (SYSV), statically linked, with debug_info, not stripped

$ ls -l target/arm-unknown-linux-musleabihf/release/hello_muscles

-rwxr-xr-x 2 jake staff 2674552 Mar 18 16:28 target/arm-unknown-linux-musleabihf/release/hello_muscles 
```

Enter fullscreen mode Exit fullscreen mode

一个~2.7MB 的自含 ARM32 可执行文件。不错，但是我们可以通过剥离可执行文件做得更好:

```
$ arm-linux-musleabihf-strip target/arm-unknown-linux-musleabihf/release/hello_muscles

$ ls -l target/arm-unknown-linux-musleabihf/release/hello_muscles

-rwxr-xr-x 2 jake staff 201760 Mar 28 14:51 target/arm-unknown-linux-musleabihf/release/hello_muscles 
```

Enter fullscreen mode Exit fullscreen mode

200 KB 以上的条子！让我们在零点上试一试:

```
# No passwords needed, thanks key-auth!
scp target/arm-unknown-linux-musleabihf/release/hello_muscles pi@raspberrypi.local:~/
ssh pi@raspberrypi.local

pi$ ./hello_muscles 
Hello, world! 
```

Enter fullscreen mode Exit fullscreen mode

### 十字工具-NG

很多老帖子提到了 [crosstool-NG](http://crosstool-ng.github.io/) ，但是 MacOS 已经不再得到官方支持。我们没有进一步研究它，但是它可能对 Windows/Linux 环境有用。

## Linux

Linux 上有许多不同的方法，我们不打算一一介绍。如果你有这里没有提到的最爱，请随意发表评论。不管怎样，还有一些其他的命令会派上用场:

```
# Get general information about executable
file <path_to_binary>
# More detailed information than `file`, such as whether soft or hard-float, etc.
readelf -A <path to binary> 
```

Enter fullscreen mode Exit fullscreen mode

### 锈十字

如果你喜欢 docker，并且你的项目可以很好的容器化，那么 [rust-musl-cross](https://github.com/messense/rust-musl-cross) 可能是个不错的选择:

```
alias rust-musl-builder='docker run --rm -it -v "$(pwd)":/home/rust/src messense/rust-musl-cross:arm-musleabihf'
rust-musl-builder cargo build --release 
```

Enter fullscreen mode Exit fullscreen mode

还有其他奇特建筑的标签，如`armv7-musleabihf`等。

还有 [rust-musl-builder](https://github.com/emk/rust-musl-builder) :主要针对 x86_64，尽管它对 armv7 有一些有限的支持形式(即 Raspberry Pi 2 和 3，但不是零)。

### 十字

Rust embedded 团队有一个[工具`cross`](https://github.com/rust-embedded/cross) 可能会让一切变得更容易:

```
# Try this first. It's working if `cross build` pulls docker images.
cargo install cross
cross build --target arm-unknown-linux-musleabi

# At the time of this writing, most recent release is from Nov. 2017 and didn't work for me
$ cross -V
cross 0.1.14

# Use HEAD
cd git clone https://github.com/rust-embedded/cross.git
cd cross
cargo build --release
cd ../some_project_source
~/cross/target/release/cross build --target arm-unknown-linux-musleabihf 
```

Enter fullscreen mode Exit fullscreen mode

确保您已经完成了 Linux 的[安装后步骤，因为它假设您不需要`sudo docker`。](https://docs.docker.com/install/linux/linux-postinstall/)

即使您可以进行交叉工作，默认图像也不一定适用于您想要构建的任何东西。其实 Rust 支持的目标三元组并不是都有 docker 镜像:

```
$ cross build --target arm-unknown-linux-musleabihf

Unable to find image 'japaric/arm-unknown-linux-musleabihf:latest' locally
docker: Error response from daemon: pull access denied for japaric/arm-unknown-linux-musleabihf, repository does not exist or may require 'docker login'. 
```

Enter fullscreen mode Exit fullscreen mode

为了解决这个问题，cross 使您能够[指定定制的 docker 图像](https://github.com/rust-embedded/cross#configuration)。

### Docker 和 Qemu

我们使用 Docker 和 Qemu 来简化多平台的构建和测试。

如果上面的解决方案都不适合你，那么使用 docker 和 qemu 就很容易了:

```
# Do this once after starting docker
docker run --rm --privileged multiarch/qemu-user-static:register --reset
# Create interactive console and mount PWD as /usr/src
docker run -it --rm -v $(pwd):/usr/src multiarch/debian-debootstrap:armhf-stretch

docker$ apt-get install -y # Pre-requisites...
# Note that Debian "armhf" is armv7
docker$ cargo build --target arm-unknown-linux-musleabi
# When you're done leave the interactive shell
docker$ exit

# Check target/arm-unknown-linux-muslXXX/YYY/ for binaries 
```

Enter fullscreen mode Exit fullscreen mode

性能会因 docker/qemu/host 资源而异，但应该比交叉编译慢，比零编译快得多。

## 圆周率 3

最后一个选择是在另一个 ARM 设备上进行构建，比如 Raspberry Pi 3，这样你就不必交叉编译了。这可能只有在你有一台低端笔记本电脑或者使用 Pi 作为你的开发 PC 时才有意义。

# 结束？

几乎没有。我们已经在 Zero 上安装并运行了 Linux，并且我们已经有了一个开发环境，因此我们可以开始构建一些东西。现在进行下一步。
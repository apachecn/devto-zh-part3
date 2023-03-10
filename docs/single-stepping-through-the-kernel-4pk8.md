# 单步执行内核

> 原文：<https://dev.to/anmolsarma/single-stepping-through-the-kernel-4pk8>

在系统程序员的一生中，可能会有这样一个时刻，她需要离开用户世界的文明安全，去面对居住在内核空间深处的无法形容的恐惧。虽然[高等生物可能会对内核调试器的想法嗤之以鼻](https://lkml.org/lkml/2000/9/6/65),但当河流开始干涸时，我们这些低等生物可能别无选择，只能单步执行内核代码。本指南将帮助你做到这一点。我们希望你永远不必真的这么做。

尽管听起来不太妙，但是为内核调试设置一个虚拟机并没有那么难。它只需要一点准备。如果你只是想要一份意大利面，跳到最后。如果你对其中的困境以及如何应对感兴趣，请继续阅读。

**N.B.:** “但是你说的是哪个内核？”一些异教徒可能总是会问，什么时候带有大写字母 K 的 Kernel 明显是指[一个真正的 Kernel](https://www.kernel.org/) 。

### 建筑内核

使用最小内核配置而不是发行版通常附带的厨房水槽配置将使生活变得容易得多。您首先需要获取您感兴趣的内核的源代码。我们将使用来自 kernel.org 的最新内核版本 tarball，在撰写本文时是 T2 的 4 . 20 . 6 T3。在提取的源目录中，调用以下命令:

```
make defconfig
make kvmconfig
make -j4 
```

这将构建一个可以在 QEMU 中引导的最小内核映像，如下所示:

```
qemu-system-x86_64 -kernel linux-4.20.6/arch/x86/boot/bzImage 
```

这应该会弹出一个看起来很古老的窗口，并显示一条神秘的错误消息:

[![](img/9e3b1400d04c474931f5959b3cc8c85b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ISJwjqYp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.anmolsarma.img/kernel_panic.png)

你可以试着将错误信息粘贴到谷歌搜索引擎中:除了你不能选择窗口中的文本。坦白地说，窗户看起来很讨厌！因此，暂时忽略实际的错误，让我们尝试让 QEMU 打印到控制台，而不是生成一个新的图形窗口:

```
qemu-system-x86_64 -kernel -nographic linux-4.20.6/arch/x86/boot/bzImage 
```

QEMU 吐出一行字:

```
qemu-system-x86_64: warning: TCG doesn't support requested feature: CPUID.01H:ECX.vmx [bit 5] 
```

Htop 告诉我 QEMU 使用了 100%的 CPU，我的笔记本电脑粉丝也同意。但是没有任何输出并且`Ctrl-c`不工作！然而[起作用的](https://superuser.com/a/1211516)是按下`Ctrl-a`然后点击`x` :

```
QEMU: Terminated 
```

原来，通过传递`-nographic`，我们已经插上了 QEMU 的*虚拟*监视器。现在，要真正看到任何输出，我们需要告诉内核写一个[串口](https://www.kernel.org/doc/html/v4.20/admin-guide/serial-console.html) :

```
qemu-system-x86_64 -nographic -kernel linux-4.20.6/arch/x86/boot/bzImage -append "console=ttyS0" 
```

成功了！现在我们可以阅读错误信息的全部荣耀:

```
[1.333008] VFS: Cannot open root device "(null)" or unknown-block(0,0): error -6
[1.334024] Please append a correct "root=" boot option; here are the available partitions:
[1.335152] 0b00 1048575 sr0 
[1.335153] driver: sr
[1.335996] Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0)
[1.337104] CPU: 0 PID: 1 Comm: swapper/0 Not tainted 4.20.6 #1
[1.337901] Hardware name: QEMU Standard PC (i440FX + PIIX, 1996), BIOS 1.10.2-1ubuntu1 04/01/2014
[1.339091] Call Trace:
[1.339437] dump_stack+0x46/0x5b
[1.339888] panic+0xf3/0x248
[1.340295] mount_block_root+0x184/0x248
[1.340838] ? set_debug_rodata+0xc/0xc
[1.341357] mount_root+0x121/0x13f
[1.341837] prepare_namespace+0x130/0x166
[1.342378] kernel_init_freeable+0x1ed/0x1ff
[1.342965] ? rest_init+0xb0/0xb0
[1.343427] kernel_init+0x5/0x100
[1.343888] ret_from_fork+0x35/0x40
[1.344526] Kernel Offset: 0x1200000 from 0xffffffff81000000 (relocation range: 0xffffffff80000000-0xffffffffbfffffff)
[1.345956] ---[end Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0)]--- 
```

所以，内核没有找到一个根文件系统来启动用户模式，并且陷入了恐慌。让我们通过创建根文件系统映像来解决这个问题。

### 创建根文件系统

首先创建一个空图像:

```
qemu-img create rootfs.img 1G 
```

然后格式化为 [`ext4`](https://en.wikipedia.org/wiki/Ext4) 挂载:

```
mkfs.ext4 rootfs.img
mkdir mnt
sudo mount -o loop rootfs.img mnt/ 
```

现在我们可以使用 [`debootstrap`](https://wiki.debian.org/Debootstrap) :
来填充它

```
sudo debootstrap bionic mnt/ 
```

这将创建一个基于 Ubuntu 18.04 Bionic Beaver 的根文件系统。当然，您可以随意用您喜欢的任何版本替换`bionic`。

完成后卸载文件系统。如果你想避免损坏图像，这一点很重要！

```
sudo umount mnt 
```

现在用我们的文件系统引导内核。我们需要告诉 QEMU 使用我们的映像作为虚拟硬盘，我们还需要告诉内核使用硬盘作为根文件系统:

```
qemu-system-x86_64 -nographic -kernel linux-4.20.6/arch/x86/boot/bzImage -hda rootfs.img -append "root=/dev/sda console=ttyS0" 
```

这一次内核不应该死机，您最终应该会看到一个登录提示。我们可以在创建文件系统时设置一个用户，但是每次启动虚拟机时都必须登录，这很烦人。让我们以 root 用户身份启用自动登录。

终止 QEMU ( `Ctrl-a`，`x`)，再次挂载文件系统镜像，然后创建配置文件夹结构:

```
sudo mount -o loop rootfs.img mnt/
sudo mkdir -p mnt/etc/systemd/system/serial-getty@ttyS0.service.d 
```

在`mnt/etc/systemd/system/serial-getty@ttyS0.service.d/autologin.conf`中添加以下几行:

```
[Service]
ExecStart=
ExecStart=-/sbin/agetty --noissue --autologin root %I $TERM
Type=idle 
```

确保卸载文件系统，然后再次引导内核。这一次您应该会自动登录。

正常关闭虚拟机:

```
halt -p 
```

### 附加调试器

让我们在启用调试符号的情况下重建内核:

```
./scripts/config -e CONFIG_DEBUG_INFO
make -j4 
```

现在，再次启动内核，这次传递`-s`标志，这将使 QEMU 监听 TCP 端口 1234:

```
qemu-system-x86_64 -nographic -kernel linux-4.20.6/arch/x86/boot/bzImage -hda rootfs.img -append "root=/dev/sda console=ttyS0" -s 
```

现在，在另一个终端启动 gdb 并连接到 QEMU:

```
gdb ./linux-4.20.6/vmlinux 
...
Reading symbols from ./linux-4.20.6/vmlinux...done.
(gdb) target remote :1234
Remote debugging using :1234
0xffffffff95a2f8f4 in ?? ()
(gdb) 
```

您可以在内核函数上设置断点，例如`do_sys_open()` :

```
(gdb) b do_sys_open 
Breakpoint 1 at 0xffffffff811b2720: file fs/open.c, line 1049.
(gdb) c
Continuing. 
```

现在尝试在 VM 中打开一个文件，这将导致`do_sys_open()`被调用…但什么也没发生？！gdb 中的断点未命中。这要归功于一个叫做 [KASLR](https://lwn.net/Articles/569635/) 的内核安全特性。KASLR 可以在引导时通过在内核命令行参数中添加`nokaslr`来禁用。但是，让我们实际上重建没有 KASLR 的内核。同时，让我们也禁用可加载模块支持，这将省去我们将模块复制到文件系统的麻烦。

```
./scripts/config -e CONFIG_DEBUG_INFO -d CONFIG_RANDOMIZE_BASE -d CONFIG_MODULES
make olddefconfig # Resolve dependencies
make -j4 
```

再次重启内核，附加 gdb，在`do_sys_open()`上设置一个断点，在 guest 中运行`cat /etc/issue`。这一次应该命中断点。但可能不在你预期的地方:

```
Breakpoint 1, do_sys_open (dfd=-100, filename=0x7f96074ad428 "/etc/ld.so.cache", flags=557056, mode=0) at fs/open.c:1049
1049 {
(gdb) c
Continuing.

Breakpoint 1, do_sys_open (dfd=-100, filename=0x7f96076b5dd0 "/lib/x86_64-linux-gnu/libc.so.6", flags=557056, mode=0) at fs/open.c:1049
1049 {
(gdb) c
Continuing.

Breakpoint 1, do_sys_open (dfd=-100, filename=0x7ffe9e630e8e "/etc/issue", flags=32768, mode=0) at fs/open.c:1049
1049 {
(gdb) 
```

恭喜你！从这一点上来说，你可以一步到位，随心所欲。

默认情况下，根文件系统以只读方式挂载。如果您希望能够写入它，请在内核参数
中的`root=/dev/sda`之后添加`rw`

```
qemu-system-x86_64 -nographic -kernel linux-4.20.6/arch/x86/boot/bzImage -hda rootfs.img -append "root=/dev/sda rw console=ttyS0" -s 
```

### 奖励:联网

您可以使用一个 [TAP 接口](https://en.wikipedia.org/wiki/TUN/TAP)在 QEMU VM 和主机之间创建一个点对点链接。

首先安装`tunctl`并创建一个持久 TAP 接口，以避免以 root 身份运行 QEMU:

```
sudo apt install uml-utilities
sudo sudo tunctl -u $(id -u)
Set 'tap0' persistent and owned by uid 1000
sudo ip link set tap0 up 
```

现在启动 QEMU，用一个虚拟的`e1000`接口连接主机的 tap0 接口:

```
qemu-system-x86_64 -nographic -device e1000,netdev=net0 -netdev tap,id=net0,ifname=tap0 -kernel linux-4.20.6/arch/x86/boot/bzImage -hda rootfs.img -append "root=/dev/sda rw console=ttyS0" -s 
```

一旦客户机启动，打开网络接口:

```
ip link set enp0s3 up
ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 52:54:00:12:34:56 brd ff:ff:ff:ff:ff:ff
    inet6 fe80::5054:ff:fe12:3456/64 scope link 
       valid_lft forever preferred_lft forever 
```

QEMU 和主机现在可以使用它们的 IPv6 链路本地地址进行通信。毕竟是 2019 年了。

### 文案要点

```
# Building a minimal debuggable Kernel
make defconfig
make kvmconfig
./scripts/config -e CONFIG_DEBUG_INFO -d CONFIG_RANDOMIZE_BASE -d CONFIG_MODULES
make olddefconfig
make -j4

# Create root filesystem
qemu-img create rootfs.img 1G
mkfs.ext4 rootfs.img
mkdir mnt
sudo mount -o loop rootfs.img mnt/
sudo debootstrap bionic mnt/

# Add following lines to mnt/etc/systemd/system/serial-getty@ttyS0.service.d/autologin.conf
# START
[Service]
ExecStart=
ExecStart=-/sbin/agetty --noissue --autologin root %I $TERM
Type=idle
# END

# Unmount the filesystem
sudo umount mnt

# Boot Kernel with root file system in QEMU
qemu-system-x86_64 -nographic -kernel linux-4.20.6/arch/x86/boot/bzImage -hda rootfs.img -append "root=/dev/sda rw console=ttyS0" -s

# Attach gdb
gdb ./linux-4.20.6/vmlinux 
(gdb) target remote :1234 
```
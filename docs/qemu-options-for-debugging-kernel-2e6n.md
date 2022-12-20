# 用于调试内核的 QEMU 选项

> 原文：<https://dev.to/wataash/qemu-options-for-debugging-kernel-2e6n>

秘书:qemu-doc(1) 中的章节

| 秘书 | [计]选项 | 细节 |
| --- | --- | --- |
| 2.3.1 | `-smp 4` | 模拟具有 4 个 CPU 的 SMP 系统 |
| 2.3.1 | `-m 256` | 内存 256MiB |
| 2.3.2 | `-hda netbsd.qcow2` | 使用`netbsd.qcow2`作为硬盘 0 |
| 2.3.3 | `-cdrom netbsd.iso` | 使用`netbsd.iso`作为光盘镜像 |
| 2.3.4 | `-display curses` | 使用 curses 显示代替 VGA 输出；连接 QEMU 监视器和`q`退出 |
| 2.3.4 | `-display none` | 不显示 VGA 显示 |
| 2.3.4 | `-nographic` | `-display none` + `-parallel null`(如果`-parallel`未指定)+ `-serial mon:stdio`(如果`-serial`和`-monitor`未指定)；无证，由[调试 QEMU](https://github.com/qemu/qemu/blob/f8c3db33a5e863291182f8862ddf81618a7c6194/vl.c#L3081-L3086) 调查 |
| 2.3.6 | `-netdev user,id=n1,hostfwd=tcp::10022-:22 -device e1000,netdev=n1` | 用户态网络栈([VirtualBox](https://www.google.co.jp/search?tbm=isch&q=virtualbox+nat+network)中的 NAT 网络)；将 localhost:10022 转发到来宾的端口 22 |
| 2.3.6 | `-nic user,hostfwd=tcp::10022-:22` | above 的简写；[需要 QEMU ( > = 2.12)](https://wiki.qemu.org/ChangeLog/2.12#Network) |
| 2.3.11 | `-serial telnet::5555,server,nowait` | `telnet localhost 5555`获取串行输入/输出 |
| 2.3.11 | `-monitor telnet::5556,server,nowait` | `telnet localhost 5556`连接 QEMU 监视器 |
| 2.3.11 | `-serial mon:telnet::5555,server,nowait` | 通过`telnet localhost 5555`服务于串行和监视器；`Ctrl-a h`查看帮助 |
| 2.3.11 | `-serial mon:stdio` | 在终端上同时提供串行和监控服务；`Ctrl-a h`查看帮助 |
| 2.3.11 | `-S` | 停止运行机器，直到 gdbserver 接受连接 |
| 2.3.11 | `-gdb tcp::1234` | 监听端口 1234 上的 gdb 连接 |
| 2.3.11 | `-s` | `-gdb tcp::1234`的简写 |
| 2.3.11 | `-enable-kvm` | (仅限 Linux)启用 KVM 支持 |
| 2.3.11 | `-no-reboot` | `reboot`用命令不重新启动就结束 QEMU |
| 2.3.11 | `-daemonize` | Daemonize QEMU 当 QEMU 运行很长时间时很有用，因为 QEMU 不会在终端关闭时被杀死 |
| 2.3.11 | `-rtc base=utc` | 要更正时区 |

我将添加特定于 Linux 的选项(如`-kernel`)
# Raspberry Pi 的 NW.js 端口

> 原文：<https://dev.to/jalbam/nwjs-port-for-raspberry-pi-7lg>

有一个 [NW.js(原 node-webkit)](http://nwjs.io/) 二进制编译为树莓 Pi 使用的 **ARMv6** 。它还可以在 Raspberry Pi 2 和 Raspberry Pi 3 上运行，因为它们向后兼容 ARMv6。其他兼容的硬件也应该能够运行这个二进制文件。

可以在 GitHub 上找到:[https://github.com/jalbam/nwjs_rpi](https://github.com/jalbam/nwjs_rpi)

## 指令

1.  **你需要一个*包. NW*T3 就是一个*。用*压缩*文件。包含您的项目的 nw* 扩展名(至少，它需要一个*index.html*和一个 *package.json* )。附带的*包. nw* 只是使用 [Yasminoku](https://github.com/jalbam/yasminoku) 游戏的一个例子。由于这只是一个端口，如果需要可以去 NW.js 的[官方网站，阅读文档了解更多关于 *package.nw* 、 *package.json* 等。](http://nwjs.io/)**
2.  可选:**用以下命令将 *nw* 和*package . NW*T5】合并成一个文件:**

```
cat nw package.nw > Your_new_binary_file 
```

1.  **编辑 *fix_libudev.so.0* 和*fix _ libudev . so . 1*T5】并用你的二进制文件的真实名称替换在它们代码中找到的 *Your_new_binary_file* (如果你没有将 *nw* 和 *package.nw* 合并在一起，那么只替换为 *nw* )。**
2.  如果你需要的话，**使用 ***chmod*** 命令(作为 root)给 *Your_new_binary_file* (或者 *nw* )可执行权限**(以及其他想要的权限)。
3.  尝试**运行二进制文件**:

```
./Your_new_binary_file 
```

如果您没有将 *nw* 和 *package.nw* 文件合并到一个文件中(如*步骤 2* 中所述)，您应该改为运行以下命令:

```
./nw 
```

1.  在系统报错 ***libudev.so.0*** 的情况下，当你尝试运行二进制时，只需键入以下命令:

```
./fix_libudev.so.0 
```

同样，如果需要，对 ***libudev.so.1*** 运行以下命令:

```
./fix_libudev.so.1 
```

注意:以上两个命令只有在您之前正确遵循了*步骤 3* 的情况下才有效。它们中的每一个只需要被执行一次，并且永远不会再次执行。

1.  如果一切顺利，你可以**发布你的项目**。你将至少需要这些文件(在同一个文件夹中): *libffmpegsumo.so* 、 *nw.pak* 和 *Your_new_binary_file* (或者 *nw* 和 *package.nw* )。如果您认为其他人可能需要它们，我建议您可选地包含 *fix_libudev.so.0* 和 *fix_libudev.so.1* (按照*步骤 3* 中的说明进行修改)。

## 版本

node-webkit(现在称为 NW.js)版本:v.0.7.0-pre

Node.js 版本:v0.10.12

## 测试于

*   **Raspberry Pi 型号 B** 修订版 2.0 安装孔】带 512MB RAM (000e 修订版)使用**Raspbian GNU/Linux 7“wheezy”**(Linux raspberrypi**4 . 1 . 19+# 858 arm V6 l**GNU/Linux)。
*   " **Raspberry Pi 2 Model B** PCB 修订版 1.1 "带 1024MB RAM (a01041 修订版)使用**Raspbian GNU/Linux 8 " Jessie "**(Linux raspberrypi**4 . 9 . 35-V7+# 1014 SMP arm V7 l**GNU/Linux)。
*   " **Raspberry Pi 3 Model B** PCB 修订版 1.2 "带 1024MB RAM (a02082 修订版)使用**Raspbian GNU/Linux 8 " Jessie "**(Linux raspberrypi**4 . 1 . 19-V7+# 858 SMP arm V7 l**GNU/Linux)。
*   " **Raspberry Pi Zero W** PCB 修订版 1.1 "带 512MB RAM (9000c1 修订版)使用**Raspbian GNU/Linux 9 " stretch "**(Linux raspberrypi**4 . 14 . 71+# 1145 SMP arm V6 l**GNU/Linux)。
*   " **Raspberry Pi 3 Model B+** PCB 修订版 1.3 "带 1024MB RAM (a020d3 修订版)使用**Raspbian GNU/Linux 9 " stretch "**(Linux raspberrypi**4 . 14 . 71-V7+# 1145 SMP arm V7 l**GNU/Linux)。
*   **Orange Pi Zero(all winner H2+)**”采用 512MB RAM(硬件 sun8i，0000 修订版)使用**Raspbian GNU/Linux 8“Jessie”**(Linux Orange pizero**3 . 4 . 39 # 2 SMP PREEMPT arm V7 l**GNU/Linux)。
*   **Orange Pi Zero plus 2(all winner H3)**”带 512MB RAM(硬件 sun8i，0000 修订版)使用**Raspbian GNU/Linux 8“Jessie”**(Linux Orange Pi**3 . 4 . 112-opi # 1 SMP PREEMPT arm V7 l**GNU/Linux)。

## 兼容性

*   **Raspberry Pi Zero** ，所有型号
*   **树莓派**，所有型号
*   **Raspberry Pi 2** ，所有型号
*   **树莓派 3** ，所有型号
*   **具有兼容硬件的其他设备**(Orange Pi、Banana Pi 等。)

## 学分

最初的二进制文件是由 Nils msén " pik sel "(又名" spaculo") 在 https://www.youtube.com/watch?v=MqNUYk9Y8jY分享的，非常感谢！ [![🙂](img/1bb31e891282bfa40812655c9c9ace9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DvXCaIjD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11/72x72/1f642.png)
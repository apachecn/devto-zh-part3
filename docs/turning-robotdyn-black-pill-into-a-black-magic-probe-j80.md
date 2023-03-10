# 将 RobotDyn 黑色药丸变成黑色魔法探针

> 原文：<https://dev.to/nickray/turning-robotdyn-black-pill-into-a-black-magic-probe-j80>

`Actual date: 2018-12-18`

今天，我从 RobotDyn 收到的包裹，里面装了太多东西，其中有几个 STM32 ARM Mini 系统(又名“黑药丸”)。它们看起来很棒，售价 2.99 美元，而且没有大多数“蓝色药丸”都有的电阻问题(T2)。由于它们是 F103 板，我将能够测试`mvirkunnen`的 [stm32f103-usb](https://github.com/mvirkkunen/stm32f103xx-usb) 的实现，并最终有望成功实现 L432 MCU 的 usb 设备，我非常需要为 [SoloKeys](https://solokeys.com) 做些努力。

# 把其中一个变成 BMP

我遵循了[这个指南](https://medium.com/@paramaggarwal/programming-an-stm32f103-board-using-usb-port-blue-pill-953cec0dbc86)，它参考了[官方文档](https://github.com/blacksphere/blackmagic/wiki/Debugger-Hardware)(搜索“使用通用 STM32F103 板”)。

### 观察

*   它是为`PROBE_HOST=stlink`打造的，因为 STM32 的 Nucleo 和 Discovery 板上的芯片与 STLink 上的芯片相同
*   我通过我信任的 [FTDI 分支](https://www.digitec.ch/de/s1/product/sparkfun-ftdi-basic-breakout-33v-elektronikmodul-5998762)添加了最初的 BMP DFU 引导程序`blackmagic_dfu.bin`，这可能是我多付了钱。结果是`/dev/ttyUSB0`
*   在 Linux 上，stm32loader 需要 root(或者我没有正确修复 udev
*   然后药丸被很好地计数，识别为`OpenMoko, Inc.`
*   然而:`dfu-util`拒绝上传`blackmagic.bin`因为 F103 说它没有足够的闪光灯。但是:[这不是真的](https://github.com/blacksphere/blackmagic/blob/master/src/platforms/stlink/Flashsize_F103)——廉价版本的芯片只是声称它们的闪存比实际少；)不管怎样，跑`sudo python stm32_mem.py`后来奏效了
*   最后，`/dev/ttyACM0`(GDB 服务器)和`/dev/ttyACM1`(USART 线路)出现了\o/
*   由于`cat /dev/ttyACM0`不起作用，这一次我修正了 udev 规则:

```
% cat /etc/udev/rules.d/99-black-magic-probe.rules 
# Black Magic Probe (on RobotDyn STM32 ARM Mini System aka "Black Pill")
# Bus 00x Device 0yy: ID 1d50:6018 OpenMoko, Inc.
ATTRS{idVendor}=="1d50", ATTRS{idProduct}=="6018", GROUP="uucp", TAG+="uaccess" 
```

*   [再次检查](https://github.com/blacksphere/blackmagic/blob/master/src/platforms/stlink/platform.h)，确认`SWDIO_PORT GPIOB`、`SWDIO_PIN GPIO14`、`SWCLK_PORT GPIOA`、`SWCLK_PIN GPIO5`仍然正确

### 胜负

正在运行

```
nicolas@berta ~/projects % gdb-multiarch -q                                                                                                                                                                       
>>> target extended-remote /dev/ttyACM0                                                                                                                                                                           
Remote debugging using /dev/ttyACM0
>>> monitor
Black Magic Probe (Firmware v1.6.1-224-g14bedcc) (Hardware Version 0)
Copyright (C) 2015  Black Sphere Technologies Ltd.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>

>>> monitor help                                                                                                                                                                                                  
General commands:
        version -- Display firmware version info                                                                                                                                                                  
        help -- Display help for monitor commands
        jtag_scan -- Scan JTAG chain for devices
        swdp_scan -- Scan SW-DP for devices
        targets -- Display list of available targets
        morse -- Display morse error message
        assert_srst -- Assert SRST until:(never(default)| scan | attach)
        halt_timeout -- Timeout (ms) to wait until Cortex-M is halted: (Default 2000)
        hard_srst -- Force a pulse on the hard SRST line - disconnects target
        traceswo -- Start trace capture [(baudrate) for async swo]
>>> monitor targets
Available Targets:
No. Att Driver
No usable targets found.
>>> monitor swdp_scan
Target voltage: unknown
Available Targets:
No. Att Driver
 1      STM32F1 medium density
>>> att 1
Attaching to Remote target 
```

是一次奇怪的令人满足的经历！不再有 OpenOCD！哈利路亚！

# 下一步

*   L432 核子(也许还有 F3DISCOVERY？)不要暴露连接到新创建的 BMP 的引脚:/因此，我接下来会尝试将 BMP 固件直接放在 Nucleo 自己的 F103 上，替换 STLink 固件。
*   测试，终于，锈`usb-device`实现了！
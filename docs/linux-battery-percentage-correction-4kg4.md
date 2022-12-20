# Linux 电池百分比修正

> 原文：<https://dev.to/samerickson/linux-battery-percentage-correction-4kg4>

## 问题

我在 macbook pro 上安装 arch linux 时遇到的问题是电池。充满电后，它显示 84%，如果我立即启动 macOS，它会显示 100%。那里发生了什么？

## 修罗

我做了一些调查，发现很多用户问同样问题的表格，但是没有真正的答案或者任何解决方案。我开始调查我的电脑是如何获取所有电池信息的。

在*i3 块*中，命令是`cat /sys/class/power_supply/BAT0/capacity`。很自然地，我进入那个目录来看看发生了什么。我找到了以下文件:

```
.
├── alarm
├── capacity
├── charge_full
├── charge_full_design
├── charge_now
├── current_avg
├── current_now
├── cycle_count
├── device -> ../../../ACPI0002:00
├── manufacturer
├── model_name
├── power/
├── present
├── status
├── subsystem -> ../../../../../../../../../../class/power_supply
├── technology
├── temp
├── type
├── uevent
├── voltage_min_design
└── voltage_now 
```

Enter fullscreen mode Exit fullscreen mode

我感兴趣的四个文件是:

*   `capacity`
*   `charge_full`
*   `charge_full_design`
*   `charge_now`

将`charge_now`除以`charge_full_design`得到`capacity`，将`charge_now`除以`charge_full`得到 **macOS** 显示的当前电池电量。

所以我修改了我的 *i3blocks 配置*中的命令来运行下面的脚本:

```
#!/bin/bash

charge_current="$(cat /sys/class/power_supply/BAT0/charge_now)"
charge_full="$(cat /sys/class/power_supply/BAT0/charge_full)"
current=$(echo "scale=2;$charge_current/$charge_full" | bc | cut -c 2-)

echo "$current%" 
```

Enter fullscreen mode Exit fullscreen mode

我承认它有点慢，后来我读了 i3blocks 文档并找到了他们建议的电池模块代码:

```
#!/bin/bash

BAT=$(acpi -b | grep -E -o '[0-9][0-9]?%')

# Full and short texts
echo "Battery: $BAT"
echo "BAT: $BAT"

# Set urgent flag below 5% or use orange below 20%
[ ${BAT%?} -le 5 ] && exit 33
[ ${BAT%?} -le 20 ] && echo "#FF8000"

exit 0 
```

Enter fullscreen mode Exit fullscreen mode

这同样有效，但是没有完成时间差，并且多了一个依赖项。这可能会节省我一点时间，但我现在对我的修复很满意。

## 为什么会这样？

不同版本的电池有不同的优点和缺点，就可充电锂离子电池而言，就像笔记本电脑中的电池一样，它们只设计为在用完之前的一定数量的循环。每次充电，你都会失去效力，工厂的最大充电量与*实际的*最大充电量不符。这会导致 linux 在电池电量下降时提供不正确的电池读数。

当你想知道你的电池当前的寿命状态时，这是很有帮助的，
但是如果你想知道在你需要
充电之前你还有多少时间，这就没什么帮助了。

## 总结

在 linux 中有很多方法可以检查电池状态，你只需要
找到适合你需要的那一种。
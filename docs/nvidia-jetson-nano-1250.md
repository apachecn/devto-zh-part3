# Nvidia Jetson Nano

> 原文：<https://dev.to/jeikabu/nvidia-jetson-nano-1250>

[![](img/1302a1e3e304ed8f07d2ea812458660d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ay7cD5dh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/nvidia_nano.jpg)

最近发布的 [Jetson Nano](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/jetson-nano/) 是 Jetson TX1 的廉价替代品:

| 平台 | 中央处理器 | 国家政治保卫局。参见 OGPU | 记忆 | 储存；储备 | 厂商建议零售价 |
| --- | --- | --- | --- | --- | --- |
| 杰特森 TX1 ( [泰格拉 X1](https://en.wikipedia.org/wiki/Tegra#Comparison) ) | 4x ARM Cortex A57 @ **1.73 GHz** | **256 x**Maxwell @**998 MHz**(**1 tflop** | 4GB lpddr 4(25.6 GB/秒) | **16 GB eMMC** | $499 |
| 杰特森纳米 | 4x ARM Cortex A57 @ 1.43 GHz | 128 倍 Maxwell @ 921 MHz (472 GFLOPS) | 4GB lpddr 4(25.6 GB/秒) | 微型标清 | **$99** |

基本上，用 1/5 的价格，你就能得到 1/2 的 GPU。[捷信全线详细对比](https://elinux.org/Jetson)。

X1 是 2015 年与 Nvidia Shield TV 一起亮相的 SoC:

[![](img/8c33db483f554f8b8ddf1bbcc0fd3aa1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YN-Q9cBc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/nvidia_x1.jpg)

**有趣的事实**:在 GDC 发布会期间，当詹森和切瓦特一起“玩”孤岛危机 3 时，他们的游戏手柄没有连接任何东西。Nvidia 的 Seth W .和我(Cevat)在后台玩。*“别理窗帘后面的那个人！”*

## 小提醒:内存带宽

25.6 GB/s 的内存带宽有点让人失望。我们对 K1 和 X1 硬件做了一些工作，内存成为了瓶颈。上表中“顺手”漏掉了，不过[Xbox 360 eDRAM/eDRAM-to-main/主存带宽为 256/32/22.4 GB/s](https://en.wikipedia.org/wiki/Xbox_360_technical_specifications#Memory_and_system_bandwidth) 。

换句话说，TX1 的 GPU 达到 1 TFLOP，而[原始 Xbox One GPU 为 1.31 TFLOPS，主存带宽为 68.3 GB/s](https://en.wikipedia.org/wiki/Xbox_One#Internals) (还有超过 100 GB/s 的 ESRAM，*fuged-about-it*)。因此，Xbone 的性能提高了 30%，但内存带宽几乎是 T4 的 2.7 倍。

当我听说任天堂 Switch 使用“定制”的 X1 时，我以为这种定制涉及到一种新的内存解决方案。没有。相同的 lpddr 4(imho)将更适合 1/4-1/2 性能的 GPU。我们没有做任何开关开发，但我不会感到惊讶，如果许多标题是内存瓶颈。如果过度依赖 1-2 个线程，下一个最有可能的罪魁祸首是 CPU，而不是 GPU。

看来要撑到 TX2 才能拿到“大男孩裤”了。它的速度为 1.3 万亿次浮点运算，带宽为 58.3 GB/秒(几乎是 X1 的 2.3 倍)。

## 设置

遵循官方指示。在苹果电脑上:

```
# For the SD card in /dev/disk2
sudo diskutil partitionDisk /dev/disk2 1 GPT "Free Space" "%noformat%" 100%
unzip -p ~/Downloads/jetson-nano-dev-kit-sd-card-image | sudo dd of=/dev/rdisk2 bs=1m
# Wait 10-20 minutes 
```

Enter fullscreen mode Exit fullscreen mode

操作系统安装本身超过 9 GB，Nvidia 演示非常大，16 GB 的 SD 卡很快就填满了。我们建议至少 32 GB 的 SD 卡。

开机进入 Nvidia 定制的 Ubuntu 18.04。

安装其他软件要有品味，记得抢 **arm64** / **aarch64** 版本的二进制而不是 arm/arm32。

## 基准

对于我们的[乌鸦岭式](https://en.wikipedia.org/wiki/Ryzen#Raven_Ridge) APU 和[织女星 GPU](https://en.wikipedia.org/wiki/Graphics_Core_Next#Vega) ，我们运行了一系列基准测试:

*   3DMARK、PCMARK、Cinebench
*   [宇宙](https://unigine.com/)(天、谷)
*   几款有基准/试玩模式的游戏(如[《盗墓者崛起》、《魔多之影》](https://github.com/subor/sdk/blob/master/docs/topics/optimization.md)等)。)
*   克莱莫/菲尼克斯矿工
*   其他几个

但是它们要么局限于 Windows 和/或 x86。似乎 ARM 平台的事实标准可能是 Phoronix 测试套件。2018 年秋天， [Phoronix 对一堆单板电脑做了一个比较](https://www.phoronix.com/scan.php?page=article&item=16-armlinux-sep2018&num=1)这并不完全令人惊讶，但仍然很有趣。

纯粹是为了娱乐，也为[树莓派零](//./raspberry-pi-zero-raspbian-rust-primer-3aj6)抛出结果。公平地说，这属于完全不同的设备类别和目标使用情形。

| 试验 | 圆周率为零 | Pi 3 B | 毫微；纤（10 的负九次方） | 笔记 |
| --- | --- | --- | --- | --- |
| glxgears | One hundred and seven | Five hundred and sixty | Two thousand three hundred and fifty | FPS。零使用“全 KMS”，当不使用只管理 7.7 FPS。3B 使用“假 KMS”，“全 KMS”导致显示器停止工作。 |
| glmark2 | Three hundred and ninety-nine | Three hundred and eighty-three | One thousand nine hundred and ninety-six | 在 Pi 上的几个测试运行失败 |
| 构建运行 | Three thousand nine hundred and sixty | One hundred and thirty-five | sixty-eight | 秒。`cargo clean; time cargo build`[就像我们在圆周率零点](//./raspberry-pi-zero-raspbian-rust-primer-3aj6#cross-compiling)上做的那样 |

### Phoronix 测试套件

PTS 挺好看的。它提供了一种简单的方法来基于唯一的标识符(重新)运行一组基准。例如，运行 2018 年秋季 ARM 文章中的测试:

```
sudo apt-get install -y php-cli php-xml
# Download PTS somewhere and run/compare against article
phoronix-test-suite benchmark 1809111-RA-ARMLINUX005
# Wait a few hours...
# Results are placed in ~/.phoronix-test-suite/test-results/ 
```

Enter fullscreen mode Exit fullscreen mode

| 试验 | 圆周率为零 | Pi 3 B | 毫微；纤（10 的负九次方） | TX1 | 笔记 |
| --- | --- | --- | --- | --- | --- |
| Tinymembench (memcpy) | Two hundred and ninety-one | One thousand two hundred and ninety-seven | Three thousand five hundred and four | Three thousand eight hundred and sixty-two |  |
| TTSIOD 3D 渲染器 |  | Fifteen point six six | Forty point eight three | Forty-five point zero five |  |
| 7-Zip 压缩 | Two hundred and five | One thousand eight hundred and sixty-three | Three thousand nine hundred and ninety-six | Four thousand five hundred and twenty-six |  |
| c 射线 |  | Two thousand three hundred and fifty-seven | Nine hundred and forty-three | Eight hundred and fifty-one | 秒(越低越好) |
| 原始筛 |  | One thousand five hundred and forty-three | Four hundred and sixty-six | Four hundred and one | 秒(越低越好) |
| AOBench |  | Three hundred and thirty-three | One hundred and ninety | One hundred and sixty-five | 秒(越低越好) |
| FLAC 音频编码 | Nine hundred and seventy-one point one eight | Three hundred and eighty-seven point zero nine | One hundred and three point five seven | Seventy-eight point eight six | 秒(越低越好) |
| 蹩脚的 MP3 编码 | Seven hundred and eighty | Three hundred and fifty-two point six six | One hundred and forty-three point eight two | One hundred and thirteen point one four | 秒(越低越好) |
| Perl(子 html) | 5.3830 | 1.2945 | 0.7154 | 0.6007 | 秒(越低越好) |
| PostgreSQL(只读) |  | Six thousand six hundred and forty | Twelve thousand four hundred and ten | Sixteen thousand and seventy-nine |  |
| 获取(GET) | Thirty-four thousand five hundred and sixty-seven | Two hundred and thirteen thousand and sixty-seven | Five hundred and sixty-eight thousand four hundred and thirty-one | Four hundred and eighty-four thousand six hundred and eighty-eight |  |
| PyBench |  | Twenty-four thousand three hundred and forty-nine | Seven thousand and thirty | Six thousand three hundred and forty-eight | ms(越低越好) |
| Scikit-Learn |  | Eight hundred and forty-four | Four hundred and ninety-six | Four hundred and thirty-four | 秒(越低越好) |

“Pi 3 B”和“TX1”栏是从 OpenBenchmarking.org 结果中复制的。还有一套更老的基准、`1703199-RI-ARMYARM4104`。

看看这些图表吧。)[![](img/49e9a8a90f97902aca3467c3b579347b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FsWsVS2Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rendered-obsolete.github.io/assets/nano_pts.png)

这些似乎都是主要的 CPU 基准，其中 TX1 可以预见地优于 Nano 10-20%，因为它的 CPU 时钟高 20%。

不要让“TTSIOD 3D 渲染器”这个名字骗了你，它是一个[软件渲染器](https://github.com/ttsiodras/renderer)(即非硬件加速；没有 GPU 受到该测试的损害)。“Socionext Developerbox”的展示进一步证明了这一点。Socionext 并不是什么新兴的 GPU 公司，该设备有一个 **24 核** ARM Cortex A53 @ 1 GHz(是的，*24*——这不是一个错别字)。

Nano 有更多的结果，包括 Nvidia TensorRT 和带风扇和不带风扇的温度监控。但是，GLmark2 可能是唯一一个可以在任何地方运行的东西。

### glxgears

关于纳米:

```
# Need to disable vsync for Nvidia hardware
__GL_SYNC_TO_VBLANK=0 glxgears 
```

Enter fullscreen mode Exit fullscreen mode

关于圆周率:

```
sudo raspi-config 
```

Enter fullscreen mode Exit fullscreen mode

**高级选项> GL 驱动> GL(全 KMS) > Ok** 在`/boot/config.txt`底部增加`dtoverlay=vc4-kms-v3d`。重启并运行`glxgears`。

### GLmark2

让 GLmark2 在 Nano 上工作很容易:

```
sudo apt-get install -y glmark2 
```

Enter fullscreen mode Exit fullscreen mode

圆周率上，[目前破](https://github.com/glmark2/glmark2/issues/80)。

您可以在 [Pi3 支持](https://github.com/glmark2/glmark2/pull/23/commits/97451860dbee23e925cda8924ee576805603bd8a)被合并后立即使用提交:

```
sudo apt-get install -y libpng-dev libjpeg-dev
git clone https://github.com/glmark2/glmark2.git
cd glmark2
git checkout 55150cfd2903f9435648a16e6da9427d99c059b4 
```

Enter fullscreen mode Exit fullscreen mode

有一个构建错误:

```
../src/gl-state-egl.cpp: In member function ‘bool GLStateEGL::gotValidDisplay()’:
../src/gl-state-egl.cpp:448:17: error: ‘GLMARK2_NATIVE_EGL_DISPLAY_ENUM’ was not declared in this scope
                 GLMARK2_NATIVE_EGL_DISPLAY_ENUM, native_display_, NULL);
                 ^ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ 
```

Enter fullscreen mode Exit fullscreen mode

在第 427 行的`src/gl-state-egl.cpp`中添加:

```
#else // Platforms not in the above platform enums fall back to eglGetDisplay.
#define GLMARK2_NATIVE_EGL_DISPLAY_ENUM 0 
```

Enter fullscreen mode Exit fullscreen mode

构建并运行所有东西:

```
# `dispmanx-glesv2` is for the Pi
./waf configure --with-flavors=dispmanx-glesv2
./waf
sudo ./waf install glmark2-es2-dispmanx --fullscreen 
```

Enter fullscreen mode Exit fullscreen mode

如果失败，用`failed to add service: already-in-use?`看一下:

*   [所以](https://stackoverflow.com/questions/40490113/eglfs-on-raspberry2-failed-to-add-service-already-in-use)
*   [论坛帖子](https://www.raspberrypi.org/forums/viewtopic.php?p=1274884)

两者都提到在`/boot/config.txt`中注释掉`dtoverlay=vc4-kms-v3d`——这是我们启用“GL(全 KMS)”时添加的。

## 你好艾世界

在得到你的系统设置后，看一看[“你好 AI 世界”](https://github.com/dusty-nv/jetson-inference)，它进行图像识别，并用 1000 个对象进行预训练。从“从源头构建回购”开始。安装依赖项需要一段时间，但之后一切都构建得相当快。

```
cd jetson-inference/build/aarch64/bin
# Recognize what's in orange_0.jpg and place results in output.jpg
./imagenet-console orange_0.jpg output.jpg

# If you have a camera attached via CSI (e.g. Raspberry Pi Camera v2)
./imagenet-camera googlenet # or `alexnet` 
```

Enter fullscreen mode Exit fullscreen mode
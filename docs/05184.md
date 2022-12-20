# 2019 年装备开发商

> 原文：<https://dev.to/erikthered/outfitting-a-developer-in-2019-8l2>

在我职业生涯的这个时候，我已经换了足够多的工作，我开始看到某些模式的出现。其中一个不太好的问题是如何为开发者选择硬件(有时是软件),而很少或根本没有来自开发者自己的输入。每个人的工作方式都有一点不同，所以为我们每天使用什么工具提供一定程度的选择是有意义的。

虽然现在的情况肯定比几年前要好，但当你和你的非工程同事得到同样的 Windows 笔记本电脑时，我们仍然有很大的改进空间。我的前几个雇主已经满足于扔给我一台配置不错的 MacBook Pro，然后就收工了。虽然 MacOS 肯定是一个不错的操作系统，但我个人更喜欢软件开发的 Linux。显然，操作系统的选择是高度主观的，取决于你对什么感到满意。

幸运的是，就我们目前的情况来看，所有三个主要的操作系统(Windows、MacOS、Linux)都非常适合做一般的开发。显然，如果您正在使用一个专门的堆栈或目标，您可能需要考虑额外的约束。也就是说，给你的开发者尽可能多的选择。

## 核心工作站

大多数工作场所现在默认选择笔记本电脑，这是有道理的，因为便携性给了你更多的灵活性。如果你需要原始动力，台式机永远是王者，但笔记本电脑对于大部分开发人员来说绰绰有余。因此，出于本文的目的，我们只考虑笔记本电脑。

以下是我推荐的最低规格:

*   多核、现代 x86 处理器:英特尔 i5/i7 或锐龙 5/7
*   16GB+ RAM(对于 Docker 之类的产品，8g 是不够的)
*   512B+固态硬盘

这些规格应该可以满足大多数一般开发工作负载，我希望如果你在一个更专业的环境中，你已经有了匹配的设备。

笔记本电脑屏幕上的一句话:1080P 可能是最常见的分辨率，也是最可靠的选择。一些笔记本电脑提供 1440P 或 4K，但这并不是它们被吹捧的全部:你需要调整比例来补偿小的物理屏幕尺寸，驱动更高的分辨率是以电池寿命为代价的。避开 1366x768 的屏幕，没有理由选择这些。

### 超极本

这些笔记本电脑比“标准”笔记本电脑更便携(更薄更轻)，这是通过焊接/粘合组件实现的。因此，您将无法升级或维修大多数部件。对于某些型号来说，热量管理可能是个问题。这些通常最大内存为 16GB。13 英寸-14 英寸屏幕是这种外形规格的典型代表。价格从 1000 美元到 2500 美元不等，符合上述建议的配置约为 1600-2000 美元。

### 传统笔记本电脑

这些产品比超极本更重更大，但在管理热量方面更好，并且通常允许未来进行一些升级。平均屏幕尺寸稍大，约为 14”-15”，RAM 容量最高为 32GB。这些产品的价格范围很广，有些配置甚至超过 3000 美元。合理的配置还是在 1600-1800 美元左右徘徊。

## 外设

### 监视器

这里的底线是提供充足的屏幕空间，确切的配置更多的是个人喜好。至少我会推荐一对 24 英寸左右的 1080P 屏幕或者一个 27-32 英寸的 1440P 屏幕。如果你想要 4K，考虑到像素密度，我只推荐 32 寸及以上的。一般来说，选择 IPS 面板，因为这种技术具有更好的色彩准确度和视角。这里的最低推荐价格仅为 300-400 美元。

### USB-C 集线器/坞站

随着 USB-C 开始取代笔记本电脑上的所有其他连接器，拥有足够的端口来连接所有外围设备非常重要。这个领域有很多选择，一旦混合了视频，兼容性就变得复杂了。我建议选择支持雷电 3 的集线器/坞站，因为这通常是视频信号的传输方式。如果你使用的是通过 USB-C 充电的笔记本电脑，一些基座可以提供电源。您最好的选择可能是检查您的笔记本电脑的特定支持。

### 额外充电器

充电器没那么贵，有两个也极其方便。一个可以放在你的包里，另一个可以挂在你的工作站上。如果你使用的是提供电源的坞站，你就不需要两个。

### 耳机或带有集成麦克风的头戴式耳机

大多数笔记本电脑都内置了网络摄像头，工作正常，但集成的麦克风和扬声器可能不太理想。我的建议是设定一个预算，让人们选择他们想要的。耳机/头戴式耳机有几种不同的类型，所以这是一个非常主观的选择。我知道很多人喜欢入耳式耳机，但我还没有发现一副在使用一个小时后仍然舒适的耳机。

### 外置键盘和鼠标

如果您使用笔记本电脑支架或坞站，有这些是很好的。这些项目也将在很大程度上取决于个人喜好。除非你真的不喜欢电缆，否则我倾向于使用有线键盘和鼠标来消除另一个潜在的麻烦。令人恼火的是，USB3 对 2.4GHz 无线设备有一个已知的干扰问题。

## 样本设置

如果你认为这是推销，我想向你保证这不是。以下链接都不是附属链接，我与戴尔或联想都没有关系。恰好这两家公司生产的机器倾向于很好地支持 Linux 和 Windows，同时被更大的企业所接受。当然还有其他制造商制造出了很棒的机器，所以不要因为我没有把他们包括在内就马上把他们排除在外。

### 超极本

#### 戴尔选项:

戴尔很棒，因为他们有几款*开发者版*笔记本电脑可以预装 Ubuntu 购买(比如链接的那款)。显然，如果你喜欢那个环境，Windows 仍然是可用的。

*   [戴尔 XPS 13 9380 -开发人员版](https://www.dell.com/en-us/work/shop/dell-laptops-and-notebooks/xps-13-developer-edition/spd/xps-13-9380-laptop/cax13w10p1c603subuntu)-1659.99 美元
    *   Intel Core i7-8565U
    *   16GB LPDDR3 2133MHz 内存(升级)
    *   512GB M.2 PCIe NVMe 固态硬盘(升级)
    *   13.3 英寸 FHD (1920x1080)非触摸 InfinityEdge 显示屏
    *   英特尔 UHD 显卡 620
*   戴尔 UltraSharp 27 显示器:u 2719d 1440 p-379.99 美元
*   [戴尔商务坞站-WD15](https://www.dell.com/en-us/work/shop/dell-business-dock-wd15-with-130w-adapter-with-dib-mdp-to-dp-cable-27ft-08m/apd/452-bddv/pc-accessories?ref=p13n_snp_results_mv&c=us&cs=04&l=en&s=bsd)-182.99 美元

估计总额:2322.97 美元(税前)

#### 联想选项:

虽然联想笔记本电脑没有预装 Linux 的选项，但有很多型号都通过了 Ubuntu 认证。

*   [ThinkPad X1 Carbon 第六代](https://www.lenovo.com/us/en/laptops/thinkpad/thinkpad-x/ThinkPad-X1-Carbon-6th-Gen/p/20KH002KUS) $1883.27

    *   Intel Core i7-8650U
    *   16 GB 内存
    *   512 GB SSD PCIe
    *   14.0 英寸 WQHD (2560 x 1440) IPS 显示屏
    *   英特尔 UHD 显卡 620
*   [ThinkVision P27h-10 27 英寸宽 QHD IPS Type-C 显示器](https://www.lenovo.com/us/en/accessories-and-monitors/monitors/professional/P27h-10A16270QP127inch-Monitor-HDMI/p/61AFGAR1US)-268.99 美元

*   [ThinkPad Thunderbolt Dock](https://www.lenovo.com/us/en/accessories-and-monitors/docking/universal-cable-docks-thunderbolt/Thunderbolt-Dock-US/p/40AC0135US)-244.99 美元

估计总额:2397.25 美元(税前)

### 传统笔记本电脑

#### 戴尔选项:

与上面的 XPS 13 类似，这款机型允许你预装 Ubuntu。

*   [戴尔 Precision 5530](https://www.dell.com/en-us/work/shop/workstations-isv-certified-dell/precision-5530/spd/precision-15-5530-laptop/xctop5530hwus3)-2191.74 美元

    *   英特尔酷睿 i7-8850H (6 核)
    *   16 GB DDR4-2666 内存
    *   512 GB m . 2 NVMe PCIe 40 级固态硬盘
    *   15.6 英寸 UltraSharp FHD 显示屏，1920x1080
    *   Nvidia Quadro P1000 带 4GB GDDDR5
*   戴尔 UltraSharp 27 显示器:u 2719d 1440 p-379.99 美元

*   [戴尔商务坞站-WD15](https://www.dell.com/en-us/work/shop/dell-business-dock-wd15-with-130w-adapter-with-dib-mdp-to-dp-cable-27ft-08m/apd/452-bddv/pc-accessories?ref=p13n_snp_results_mv&c=us&cs=04&l=en&s=bsd)-182.99 美元

估计总额:2754.72 美元(税前)

#### 联想选项:

*   [联想 ThinkPad X1 Extreme](https://www.lenovo.com/us/en/laptops/thinkpad/thinkpad-x/ThinkPad-X1-Extreme/p/20MF000BUS)-1871.10 美元

    *   英特尔酷睿 i7-8750H (6 核)
    *   16 GB DDR4 2666MHz
    *   512 GB 固态硬盘，PCIe-NVME OPAL2.0 M.2
    *   15.6 英寸 FHD (1920 x 1080) IPS 显示屏
    *   NVIDIA GeForce GTX 1050Ti 4GB
*   [ThinkVision P27h-10 27 英寸宽 QHD IPS Type-C 显示器](https://www.lenovo.com/us/en/accessories-and-monitors/monitors/professional/P27h-10A16270QP127inch-Monitor-HDMI/p/61AFGAR1US)-268.99 美元

*   [ThinkPad Thunderbolt Dock](https://www.lenovo.com/us/en/accessories-and-monitors/docking/universal-cable-docks-thunderbolt/Thunderbolt-Dock-US/p/40AC0135US)-244.99 美元

估计总额:2385.08 美元(税前)

## 总结

在整篇文章中重复一遍:给开发者选择。在我们的职业中，电脑是高度个人化的物品，我们在上面花费了大量的时间。我们应该有合理的能力来选择最符合我们个性和工作方式的东西。我希望这对外面的人有帮助！请随时留下评论和反馈。
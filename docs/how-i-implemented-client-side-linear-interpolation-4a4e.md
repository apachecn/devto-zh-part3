# 我如何实现客户端线性插值

> 原文：<https://dev.to/webdva/how-i-implemented-client-side-linear-interpolation-4a4e>

由于我致力于为玩家开发一个伟大的游戏，我为我正在制作的新 3D 多人浏览器游戏制作了一个简单的客户端线性插值子系统。

# 背景

在物理运动的情况下，可以对物体的初始位置 *A* 和目标位置 *B* 之间的过渡进行插值，从而在这两个位置之间产生看起来平滑的过渡。线性插值是实现这种平滑过渡效果的一种手段。

以下等式显示了线性插值如何确定其效果，其中*位置*为结果位置，*开始*为初始位置，*结束*为目标位置，*时间*确定效果的时间长度。

[![math](img/dbfd8d1f8e45651511e754eb3bd7e1e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--84gweM2W--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g6ml2y1vms0iejojw4w7.png)

客户端线性插值子系统使用的 JavaScript 函数实现的线性插值:

```
function lerp(start, end, time) {
    return start * (1 - time) + end * time;
} 
```

Enter fullscreen mode Exit fullscreen mode

注意单词 *lerp* 是线性插值的缩写，动词*到 lerp* 的意思是做线性插值。

可以对插值函数进行修改，使其不仅仅使用线性插值，但是线性插值的简单性及其低开发成本使其成为最能满足程序约束的选择。

此外，出于文档目的和为了创建将在未来游戏开发工作中使用的预测模型，请注意，我花了三个小时(如果我没记错)来实现这个客户端线性插值子系统。

# 满足系统要求

我希望在一个对象的当前位置和它的新位置之间有一个平滑的过渡，这个新位置是由游戏服务器在每个服务器上传输游戏状态更新时给出的。

*开始*值将是对象的当前位置值。*结束*值将是服务器发送的它为游戏客户端确定的新位置。

*时间*值将由自上一帧以来经过的时间与任意 lerp 时间因子之间的比率确定，该时间因子将影响运动插值将发生多长时间。

[![math](img/8c88157f5414ede931a16d96f52e059a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fh1zVsWV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/onqolfvbtbg22re8gsf2.png)

使用这个建议的时间比将实现在物体位置之间具有平滑过渡的期望效果，从而满足系统要求。

从理论上讲，使用这些建议的实用程序，如果从上一帧开始时间为零，那么对象的位置将保持不变，如所示

[![math](img/2212e686d0efedf210b26d2cd737096b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PKuMfpga--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lnasi0ql4z5kzz4kthfk.png)

这是所希望的并且符合系统要求。

相反，如果最后一帧之间经过的时间等于 lerp 时间因子，那么对象的新位置将在*结束*值，如所示

[![math](img/5a9c9af77dc4fed5378f22c23e9bd7e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z1TZj3Fv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4s1k7ogk5vi2lejyuvt9.png)

这也是所希望的，并且也符合系统要求。

## 迟帧的偶然性

如果自上一帧以来的时间超过了 lerp 时间因子，则位置简单地瞬时改变，而不是线性内插。一个简单的`if`条件用于确定这一点。

```
if (time_since_last_frame > lerp_time_factor) // do not use linear interpolation
    position = server_updated_position; 
```

Enter fullscreen mode Exit fullscreen mode

# 实现

下面是简单客户端线性插值子系统使用的程式化实现。

```
if (time_since_last_frame <= lerp_time_factor) { // use linear interpolation
    position = lerp(position, server_updated_position, time_since_last_frame / lerp_time_factor);
} else { // do not use linear interpolation
    position = server_updated_position;
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，实际游戏中的物理运动实际上是独立于帧速率的，并且为了读者的缘故，在这里被呈现为依赖于帧速率。

# 结果

下图是未使用线性插值和使用线性插值的对比。左侧显示了不稳定或不连续的运动，因为没有使用线性插值，而右侧显示了根据系统要求使用线性插值时位置之间的平滑过渡。

[![No lerp versus lerp](img/9fd967f4ae76b3bb10527518fd50679c.png "Comparison of no linear interpolation and with linear interpolation")](https://res.cloudinary.com/practicaldev/image/fetch/s--8GxXzKTf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2hmddaxgdop97fmezaab.gif)

# 客户端预测和橡皮筋

以便不在游戏服务器上等待新的坐标被传输，因为网络延迟会导致客户端-服务器通信的延迟，当玩家发出移动命令时，游戏客户端在从游戏服务器接收到正确的位置之前立即改变玩家的位置，预测这将对玩家有利。然后，当客户端从服务器接收到实际位置时，线性插值子系统将在下一帧期间平滑地校正玩家的位置。

结合这种客户端预测的使用，当游戏客户端失去其与游戏服务器的连接时，在玩家移动命令期间出现橡皮带效应，其中玩家的化身表现得好像由于没有接收到服务器位置更新而被橡皮带到其位置，并且因此总是在其当前位置和其预测位置之间学习，直到重新建立与服务器的连接。

[![Rubber banding demonstration](img/1601f5705ad0d0e80d3932545b9ee3ed.png "Rubber banding effect")](https://res.cloudinary.com/practicaldev/image/fetch/s--utfZwXyV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ume8irqqh2qcaj82dor2.gif)

请注意，在网络子系统抖动期间，这种橡皮带效应会发生，但不太明显。这是意料之中的，因为使用线性插值的网络多人游戏很常见。
# Powershell:构建配置对象-第 2 部分，共 3 部分

> 原文：<https://dev.to/theinfradev/powershell-build-configuration-objects---part-2-of-3-11pn>

# **PowerShell 函数和类 Pt 2 -构建配置对象**

注意:这是关于 PowerShell 类和函数的三部分系列的第二部分。

在第一部分中，我们看了如何创建一个类来保存我们的配置对象。然后，我们创建了一个构造函数来在配置中创建一些自动化。在第二部分，我想看看我们如何使用方法来生成我们的对象。例如，让我们以电视游戏电脑为例。

```
 Class PCSystem
{
        [string]$CPU
        [string]$Mainboard   = 'MSI B450M Mortar Titanium'
        [string]$Case        = 'Thermaltake Level 20 VT Chassis'
        [string]$PowerSupply = 'SeaSonic FocusPlus Gold 650W'
        [string]$RAM         = 'Team Delta RGB - 16 GB DDR3000'
        [string]$Storage     = 'Samsung 970 EVO 500 GB'
        [string]$GPU
} 
```

We have deliberately left the CPU and GPU options available to be tweaked as these are the main configuration changes we can make to our performance. I would like to be able to configure it with the following options:*   1 : CPU = 'AMD Ryzen 5 2400G' | GPU = 'No GPU'*   2 : CPU = 'AMD 锐龙 3 2200g ' | GPU = ' EVGA-GeForce GTX 2070 '*   3 : CPU = 'AMD 锐龙 5 2400g ' | GPU = ' EVGA-GeForce GTX 2070 '

我们如何做到这一点？我们可以实例化对象，并在脚本体中添加这些配置。这当然很常见，但我认为这使得剧本难以阅读。最好是让剧本读起来像一个故事。或者，我们可以创建一个接受参数的构造函数，比如一个像“1”、“2”或“3”这样的整数。

对我来说，这看起来更干净，并且允许我们清理我们后来的代码，同时也创建了离散的配置，可以在以后很容易地追加或添加，因为我们在一个地方存放了我们所有的配置。

# 让我们创建我们的类来构建特定类型的计算机。

### 很高兴看到你终于给这个蹩脚的版本添加了一个 GPU。

在我的上一篇文章中，我将 CPU 名称作为构造函数的输入，这一次我们要做的就是取一个整数。(即 1、2 或 3)

让我们看看这是怎么做到的。

```
 Class PCSystem
{
    $CPU; $Mainboard; $Case; $PowerSupply; $RAM; $Storage; $GPU 
```

```
 PCSystem([int32]$Option)
    {
        [string]$this.Mainboard     = 'MSI B450M Mortar Titanium'
        [string]$this.Case          = 'Thermaltake Level 20 VT Chassis'
        [string]$this.PowerSupply   = 'SeaSonic FocusPlus Gold 650W'
        [string]$this.RAM           = 'Team Delta RGB - 16 GB DDR3000'
        [string]$this.Storage       = 'Samsung 970 EVO 500 GB' 
```

```
 switch($Option)
        {
            1{
                [string]$this.CPU  = 'AMD Ryzen 5 2400G'
                [string]$this.GPU  = ''
             }
            2{
                [string]$this.CPU  = 'AMD Ryzen 3 2400G'
                [string]$this.GPU  = 'EVGA - GeForce GTX 2070'  
             }
            3{
                [string]$this.CPU  = 'AMD Ryzen 5 2400G'
                [string]$this.GPU  = 'EVGA - GeForce GTX 2070'
             }
Default 
         {
                [string]$this.CPU  = 'Invalid configuration'
                [string]$this.GPU  = ''
             }
        }
    }
} 
```

这就是了，我们现在有了一种在脚本中创建这个复杂对象的方法，只需用一个整数调用它。为了看看这看起来像什么，我们不妨称之为选项 3:

```
 >_ [PCSystem]::new(3)

CPU          : AMD Ryzen 5 2400G
Mainboard    : MSI B450M Mortar Titanium
Case         : Thermaltake Level 20 VT Chassis
PowerSupply  : SeaSonic FocusPlus Gold 650W
RAM          : Team Delta RGB - 16 GB DDR3000
Storage      : Samsung 970 EVO 500 GB
GPU          : EVGA - GeForce GTX 2070 
```

# **你能想出其他同样适用的配置吗？**

### *你为什么不呢，我读了几百万字都累了*

这种配置构建器方法是处理构建 PowerShell 对象的一种很好的方式。重要的是，它还将这个配置步骤从您的主逻辑中去掉，“分离关注点”。对于许多开发人员来说，这是一种非常标准的方法，但是对于系统工程师来说，这可能是一种全新的思考方式。如果你问自己，我为什么要这样做，也许在我结束这篇文章时会坚持到下一篇文章，但我发现这对于将你的脚本组织成离散的部分是非常好的，它清理了你的配置，同时将它们放在任何工程师都可以找到的一个地方。我们现在已经清楚地了解了每个对象需要什么，因为它的整体是在类的开始定义的，如果我们想添加一个配置，我们可以使用 composition 在构造函数中包含新的配置，然后通过整个脚本过滤。我使用这个配置类的一些对象包括:虚拟机、网络适配器和 AD 用户/计算机配置。

在这个 3 部分系列的最后一部分，我将看看如何在一个函数中使用这个配置对象来提供一个完整的实现，并且，你知道，实际上用这个类对象做一些我花了很长时间演示的事情。

如果你能想到其他类型的配置，请写在下面的评论中，因为我一直在寻找新的想法。

干杯，

基础设施开发

*要获得我的新文章的更新，请在 Twitter 上关注我 [@theinfradev](https://twitter.com/theinfradev)*

Philipp Katzenberger 在 Unsplash 上拍摄的照片
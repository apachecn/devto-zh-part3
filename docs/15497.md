# Powershell:保持优雅和实用-第 1of3 部分

> 原文：<https://dev.to/theinfradev/powershell-keeping-it-classy-and-functional---part-1of3-3272>

# **让我们保持优雅**

### *“48 小时到...哦，闭嘴！”*

在 PowerShell 5.0 中，微软引入了适当的类，给了我们一个非常强大的功能，对于一般的系统管理员来说，这个功能可能没有被使用过。事实上，类的伟大之处在于我们如何将它们的数据和功能与我们的标准 PowerShell 函数结合起来，以创建更干净的代码，并具有定义良好的配置和输出。

> If you have never created a Class in PowerShell or... ever, I suggest trying it out seperately. I won’t go into all the specifics as this is less about how to create a class, and more about what we can do with them, once we are comfortable. I suggest this guy’s blog as a good primer, or at least to bookmark for later.
> 
> [Powershell 类和概念](https://xainey.github.io/2016/powershell-classes-and-concepts/)

在第一篇文章中，我将通过模拟一个物理过程来创建我们的第一个数据类。在这种情况下，我们将为一台小型游戏 PC 构建一个连接到电视的配置。仅使用 AMD 集成 GPU 的主要构建不是特别强大，但它应该能够在 1080p 的中等设置下为沙发游戏(如火箭联盟)提供不错的性能。因为我们使用了类，所以我们可以在以后扩展这个配置，使它(我们的 PC 配置)更加强大。

# **让我们创建第一个类来存储我们的配置**

### 我们就不能买个 Xbox 或者 PS4 吗？似乎更容易。

好了，让我们来定义一下我们想要的电视游戏盒的各个部分。我们需要以下内容作为我们的基本配置:

*   主板:微星 B450M 迫击炮钛(105 美元)
*   内存:Team Delta RGB-16 GB DDR 3000(135 美元)
*   存储:三星 970 EVO 500 GB(128 美元)
*   机箱:Thermaltake 级 VT 机箱(105 美元)
*   PSU: SeaSonic FocusPlus 金 650 瓦(90 美元)

如果我们想增强处理能力，我们将围绕这个平台实现标准化，允许对 CPU 和 GPU 进行细微的更改。

> 我不想详细解释为什么我选择了 AMD 而不是 INTEL，或者 16GB 而不是 8GB，但是我只想说，如果你建造了这台电脑，它应该都能工作。

那么我们如何在 Powershell 中作为一个类做到这一点呢？首先要知道的是 PowerShell 让这变得非常简单，我们基本上只需要列出我们的项目，PowerShell 会担心如何构造对象。这是我们创建配置的最简单的方法。

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

在上面的例子中，我们可以看到我们已经声明了变量类型和变量名。对于一些，这就是我们所声明的，然而对于其他的，我们已经声明了一个缺省值。这意味着当稍后实例化这个对象时，我们已经键入了这些值。因为我想让我们的选项保持开放，所以我将我们的 CPU/GPU 选项保留为未声明的值。

现在我们有了这个基类，我们可以实例化它。

```
$TVPC =  [PCSystem]::new()

```

Now I am going to add a little gem of a Processer, an APU (CPU + GPU) from AMD that will give our box some great processing capacity and due to the integrated GPU gives us a low end gaming system for very little money. (USD$158)

```
$TVPC.CPU = 'AMD Ryzen 5 2400G'

```

Now our CPU is added, we have our base system. If we print this to console we get:

```
CPU         : AMD Ryzen 5 2400G
Mainboard   : MSI B450M Mortar Titanium
Case        : Thermaltake Level 20 VT Chassis
PowerSupply : SeaSonic FocusPlus Gold 650W 
RAM         : Team Delta RGB - 16 GB DDR3000 
Storage     : Samsung 970 EVO 500 GB 
GPU         :

```

Perfect, now we have a simple object which describes our configuration which we can pass around between functions as we like. Later if we decide we need extra storage or any other attributes we can just add them to our class, and they will automatically be instantiated, so we have a very extensible config.

# 让我们制作自己的类构造函数吧！

### 我以为我们会让 PowerShell 帮我们做这件事？

你可能在想，这很酷，任务完成了！嗯，是的。但是我们可以做得更好。

类的伟大之处在于不像哈希表——我们可以通过构造函数给类添加行为(方法)。

> 快速入门:构造函数告诉语言引擎如何构建类对象。

我们可以给出类逻辑，这样如果在创建时没有提供输入，它将是默认的类，或者如果提供了配置选项，创建一个更定制的类版本。

让我们看看构造函数是什么样子的。(我将压缩顶部的变量声明，因为这样看起来更整洁)。

```
Class PCSystem
{
   $CPU; $Mainboard; $Case; $PowerSupply; $RAM; $Storage; $GPU
   PCSystem([string]$CPU)
   {
       [string]$this.CPU           = $CPU 
       [string]$this.Mainboard     = 'MSI B450M Mortar Titanium' 
       [string]$this.Case          = 'Thermaltake Level 20 VT Chassis'
       [string]$this.PowerSupply   = 'SeaSonic FocusPlus Gold 650W'
       [string]$this.RAM           = 'Team Delta RGB - 16 GB DDR3000'
       [string]$this.Storage       = 'Samsung 970 EVO 500 GB'
    }
}

```

So what is the difference here? Well, now we have made a constructor method that has the same name as the class (this is important). PowerShell will now treat this method as its constructor and knows that when an instance of this object is created it must follow the rules provided in this method:

*   首先，我们不是在创建变量时声明缺省值，而是在构造函数中声明，这允许我们根据构造函数而不是缺省值做出决定。
*   第二，我们要求在创建时，给 CPU 一个值。通过这种方式，我们要求尽早做出决定，但是最终会说——没有这个我们就不能继续。
*   Third, in this particular case you will note the GPU is ignored. This particular configuration doesn’t care about GPU so we don’t need to specify it. However the GPU attribute will still be created because it is referenced at the top of the class.

    > 注意:像许多其他语言一样，当你引用一个类的变量时，(比如 CPU)我们需要称它为“$This”。CPU”。参数的“this”部分是方式。NET 处理的是对象的实际实例，而不是我们在这里工作的抽象类。在前面的例子中，我们创建了$TVPC 对象，“$this。“CPU”是对“$TVPC”的类比。CPU”。

    由于这个构造函数，我们现在可以在创建变量时定义我们的 CPU，将我们的代码从 2 行缩减到 1 行，并确保对象从一开始就符合最小规格。

    我们现在可以创建它，就像这样:

    ```
    $TVPC =  [PCSystem]::new('AMD Ryzen 5 2400G')

    ```

    # 下一步是什么？

    ### 等等，你说了些什么？抱歉我正忙着在我刚买的 PS4 上下载红色死亡救赎 2。

    我希望，当我在这里使用一个真实世界的物理对象时，你可以看到如何用它来描述任何复杂函数的配置。通过使用一个简单的构造函数，我们可以创建一些小的配置变量，我们可以根据环境调用这些变量。例如，您想要创建一个虚拟机群，并且您有两种不同的内存配置。有了这些构造函数，只需将构造函数的输入从一种配置更改为另一种配置，就可以节省大量时间和重复的代码，PowerShell 会处理剩下的工作。

    好了，这篇文章到此为止，在我的下一篇文章中，我将看看我们如何进一步使用构造函数以及一般的类方法来将一个脚本构建成一个更复杂但可维护的软件。

    欢呼，

    T2

    *要获得我的新文章的更新，请在 Twitter 上关注我 [@theinfradev](https://twitter.com/theinfradev)*

    照片由亨电影公司在 Unsplash 上拍摄
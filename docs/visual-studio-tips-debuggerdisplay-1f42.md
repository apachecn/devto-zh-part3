# Visual Studio 提示-调试器显示

> 原文：<https://dev.to/hutchcodes/visual-studio-tips-debuggerdisplay-1f42>

当您在“监视”窗口中查看一个对象时，您看到的是 ToString()方法产生的内容。但是，如果您可以控制显示的内容，以便看到一些有意义的价值，那会怎么样呢？你可以的。

如果我们有一个这样定义的`Person`类

```
namespace VSTips.DebuggerDisplay
{
    class Person
    {
        public string FirstName { get; set; }
        public string LastName { get; set; }
    }
} 
```

如果我们创建一个实例并在监视窗口中查看它，我们看到的只是`{VSTips.DebuggerDisplay.Person}`。我们当然可以深入查看单个属性，当您查看单个对象时，这并不是很糟糕，但是当您查看`List<Person>`并看到这一点时，您知道您将花费大量时间单击来找到您正在寻找的对象。

[![alt text](img/a4dc6912f27824cb32ef5e9f7efeb266.png "Ojects dispalyed without DebuggerDisplay attribute")](https://res.cloudinary.com/practicaldev/image/fetch/s--6vA8gD63--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hutchcodes.net/img/2019/NoDebuggerDisplay.jpg)

如果我们回到`Person`类的定义并添加一个属性，我们可以让监视窗口显示我们想要的任何内容。在这种情况下，我们将显示姓氏和名字的前 5 个字符(取前 5 个字符只是为了显示灵活性)。

```
using System.Diagnostics;

namespace VSTips.DebuggerDisplay
{
   [DebuggerDisplay("{LastName,nq}, {FirstName.Length >= 5 ? FirstName.Substring(0, 5) : FirstName,nq}")]
    class Person
    {
        public string FirstName { get; set; }
        public string LastName { get; set; }
    }
} 
```

当您使用这个调试器查看监视列表时，您所看到的会更有帮助。

[![alt text](img/a187ac8a063a07583bb09f00ab96176e.png "Ojects dispalyed without DebuggerDisplay attribute")](https://res.cloudinary.com/practicaldev/image/fetch/s--g2lkxudm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hutchcodes.net/img/2019/WithDebuggerDisplay.jpg)
# 解构再思考

> 原文：<https://dev.to/nicholascloud/destructuring-reconsidered-3jdp>

(本文原载于[nicholascloud.com](https://www.nicholascloud.com/2019/05/destructuring-reconsidered/)。)

在过去五个月使用 React 的过程中，我注意到 React 开发人员大量使用了对象析构，尤其是在函数签名中。我用的 React 越多，我就越不喜欢这种趋势，下面是一些简短的原因。

有无数由明智的行业圣贤撰写的书籍讨论如何编写好的函数。功能应该做一件事，而且只做一件事；它们应该被简洁地命名；它们的参数应该密切相关；等等。我的观察是，被析构的函数参数往往会很快导致违反这些最佳实践。

首先，析构函数参数鼓励“抓取包”函数，其中被析构的参数*彼此不相关*。从实践的角度来看，实际参数*的析构属性被认为是函数的参数。至少，析构函数的签名读起来好像是:* 

```
function foo({ bar, baz }, buzz) {} 
```

Enter fullscreen mode Exit fullscreen mode

开发人员会这样理解，就好像`bar`、`baz`和`buzz`是函数的实际参数(你可以这样重写函数，所以它们也可能是)，但这是不正确的；真正的参数是`buzz`和一些其他的对象，根据最佳实践*应该*与`buzz`相关。但是因为第一个参数(param1)被析构了，我们现在有了属性`bar`和`baz`，它们与`buzz`差了一步，因此 param1 和`buzz`之间的关系变得模糊了。

这有三种方式:

1.  如果 param1 和`buzz` *是*相关，我们不知道为什么；
2.  如果 param1 和`buzz` *与*不相关(但是`bar`和`baz`与`buzz`相关)，那么这个函数写得很差；
3.  如果`bar`、`baz`、param1 和`buzz`都密切相关，那么这个函数仍然写得很差，因为它现在有三个“虚拟参数”，而不是只有两个实际参数。

第二，析构函数鼓励过多的“虚参数”。出于某种原因，开发人员认为这个函数签名写得很好:

```
function sendMail({ firstName, lastName, email}, { address1, city, state, zip}, { sendSnailMail }) {}
// function sendMail(user, address, mailPreferences) {} 
```

Enter fullscreen mode Exit fullscreen mode

“但它只有三个参数！”他们说。虽然在技术上是正确的，但是短功能签名的要点是将功能限定在一个单一的、有形的任务上，并且*减少认知开销*。实际上，这个函数有八个参数。虽然这个函数的用途从它的名字来看是相当明显的，但是表达性较差的函数就更难找到了。

第三，析构使重构变得困难。当然，我们的工具总有一天会赶上的。但是据我所见，现代编辑器和 ide 不能智能地重构带有析构参数的函数签名，尤其是在像 JavaScript 这样的动态/弱类型语言中。IDE 或编辑器需要通过检查代码中其他地方的调用来推断传递给函数的参数，然后推断这些参数的赋值以确定是哪个构造函数或对象文字产生了它们，然后重写这些对象中的属性...你可以看到这是一个几乎不可能的壮举。或者至少，即使是最好的 ide 和编辑器也会在过程中引入如此多的 bug，以至于无论如何都要避免这个特性。

第四。开发人员通常必须跟踪函数的调用到它的定义。根据我的经验，代码库通常有许多在不同上下文中使用的同名函数*。现代工具很聪明，它们会检查函数签名，试图将定义与调用联系起来，但是析构使这个过程变得更加困难。给定下面的函数定义，调用都是有效的(因为 JS 函数是可变的)，但是如果一个代码库有不止一个名为`foo`的函数，确定哪个调用链接到哪个定义是一个特别的噩梦。* 

```
// in the main module
function foo({ bar, baz}, { bin }, { buzz }) {}

// in the bakery module
function foo(bar, { baz }) {}

// invocations
foo({ bar, baz });

foo(anObject, anotherObject);

foo(1, { bin }, null); 
```

Enter fullscreen mode Exit fullscreen mode

相反，带有显式命名参数的函数(通常签名参数的命名与用于调用函数的变量和属性的命名相同)使这些函数更容易跟踪。

第五，被析构的参数模糊了它们所属对象的接口，使得开发人员对可能在函数中使用的*实际*参数的相关属性和方法一无所知。比如:

```
function handle({ code }) {} 
```

Enter fullscreen mode Exit fullscreen mode

除了`code`之外，第一个参数中还有什么可以让我更充分地“处理”我正在处理的东西？这里隐含的假设是`code`将是我完成工作所需要的全部，但是任何开发人员都会故意对这个假设的天真傻笑。为了获得我需要的关于这个参数的信息，我必须搜索文档(哈哈哈哈哈文档),希望它能揭示被传递的实际参数(而不仅仅是记录被析构的属性),或者手动记录这个参数，以弄清楚它拥有哪些其他成员。这就引出了我的最后一点:

伐木。我数不清为了记录传递给函数的完整对象，我不得不析构函数参数的次数，因为我需要知道关于该对象的一些上下文信息。这同样适用于带有断点的调试。(我喜欢 Webpack 不得不重新构建我的客户端代码，因为我只想看看传递给函数的实际参数是什么。美好时光。)

不要误解我——我并不完全反对析构。实际上，我很喜欢以不模糊代码、不妨碍开发或不妨碍调试的方式使用它。就我个人而言，我避免在*签名*中析构函数参数，而是在函数的第一行析构它们，如果我想在函数中用较短的变量名作为属性的别名。

```
function sendEmail(user, address, mailPreferences) {
  const { firstName, lastName, email } = user;
  const { address1, city, state, zip } = address;
  const { sendSnailMail } = preferences;
  //...
} 
```

Enter fullscreen mode Exit fullscreen mode

这种模式既符合定义函数的最佳实践，也为我提供了一种从更广泛的参数中提取所需信息的轻量级方法，而不会使我在需要时从这些参数中获取额外信息变得很困难。

不要仅仅因为所有酷孩子都这么做就用新的闪亮。记住之前的智慧，因为它是以我们不想再次付出的代价换来的。

* * *

1.  [清理代码](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882/ref=sr_1_2)，[代码完成](https://www.amazon.com/Code-Complete-Practical-Handbook-Construction/dp/0735619670/ref=pd_sbs_14_2/140-0462844-1402620)等。*
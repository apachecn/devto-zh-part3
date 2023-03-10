# 如何编写进入过滤方法的函数

> 原文：<https://dev.to/manumagalhaes/how-to-write-functions-that-go-inside-a-filter-method-29b4>

**第二部【我第一个月编码的诡异问题】**

 *<figure>[![](img/26530b2720060cf2e8816039f0035f3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Hcli2yLc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/484/1%2AHJnm3XAOPWQs15B181-5_w.png) 

<figcaption>乡亲们，这就是你们冲泡美味咖啡的方法。过滤它。</figcaption>

</figure>

现在你可能知道方法是伪装的函数。有些方法，如 filter，需要另一个函数才能运行。所以当你使用 filter 时，你实际上是在另一个函数内部使用一个函数(回调)。就像亚马逊森林一样，回调是另一个函数中的一个函数。更准确地说，回调是一个作为参数传递的函数，但是这个精确的定义远远超出了我这个新手的理解。

好吧，阅读文档，咄。是啊，好主意:

<figure>[![](img/af3c407eb285fc02a85c22c6cb032a47.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xXYcltUf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/582/1%2Aeu8Cio4gNqNhTJOXsf8zrw.png) 

<figcaption>嗯……你确定？</figcaption>

</figure>

但事情没必要那么困难。让我们把这个翻译给真正的初学者——说:

> array . filter(function do something(value， *index，array*){无论你需要做什么})

注意斜体的参数是可选的。

在最初的几周，我只是在想我到底应该写什么作为回调，更重要的是:所有这些参数从哪里来？！？！

### 我们潜水吧

我将从一个详细的例子开始，然后重构它，使它更具可读性:

[![](img/e5b735ab83a48323103632d2120df536.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Tbj9lT0t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/388/1%2A5v-CXqxVeieAp_61Jbycxw.png)

看到了吗？不用想太多，想退就退。

好吧，但是*值*从何而来？

方法的好处是它们“知道”你需要什么样的信息，它们会用这些预定义的参数来填充你的函数。在过滤器的情况下，它会自动给你 1) **值**，2) **索引**，3) **数组**信息——并且*总是按照这个顺序*。这意味着，如果您编写 planets . filter(planet =>planet . starts with(" M ")(用 planet 或任何其他单词替换单词 value)，您的函数无论如何都会工作。

让我们看另一个例子，index 参数很有用:

<figure>[![](img/1ffe57d37b3fa9cdfd6ec9e3a9218228.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wrv012ph--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/364/1%2AIpK6oGf1htf_37QDn1i57w.png) 

<figcaption>这里我们不使用 value 参数，但是我们无论如何都要传递它</figcaption>

</figure>

filter 方法知道第二个参数将始终是数组项的索引，无论您如何调用它——只要它是第二个参数。

[![](img/a42086d82d5bedec4647def1cc4a38d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lcY00ppS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/291/1%2AEYbq31M_gj3AU2B0FQXEwg.png)

但我发现你并不相信。您希望删除值参数，因为您不使用它。接下来会发生什么？

[![](img/b36d4047bbb15c513c2db36de5ada0ee.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hB7ylw1g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/368/1%2Aod6Zci2XOPsJCl-fKQ5miA.png)

你会得到一个空数组。为什么？filter 方法将该参数解释为值，不管您如何命名它。当第一个(或单个)参数进入回调时，它将被作为一个值来处理。句号。在这种情况下，由于我们的值是一个字符串，所以说水银小于二是没有意义的。JavaScript 会告诉你结果是假的，你会得到一个空数组。(记住[过滤器只返回](https://medium.com/@manumagalhaes/comparing-js-iteration-methods-map-filter-foreach-reduce-loops-f83323663ccb) [真实评价](https://medium.com/@manumagalhaes/comparing-js-iteration-methods-map-filter-foreach-reduce-loops-f83323663ccb))。

现在是一个数字数组的例子:

<figure>[![](img/58c387ec5cc7ce10c857b61fa627e62e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N_MPXA-m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/387/1%2ApBHV5ac3HduNMx0S8CZXlQ.png) 

<figcaption>尝试重构上面的代码；)</figcaption>

</figure>

如果你对学习如何重构感兴趣，[我的朋友 Bukola 写了一篇关于这个的不错的帖子](https://medium.com/@bkljimoh/e5b3e6955b4d)；)

### **TL；DR；**

。过滤器是一个非常小的东西。万一遇到困难，就简单明了:让回调函数返回数组中为真的位，遵守自动赋予 filter 方法的参数顺序。就是这样。

> *“我编码第一个月的怪问题”系列*
> 
> 第一部分:[数组索引&值如何在 for 循环中工作](https://medium.com/@manumagalhaes/how-array-indexes-values-work-in-for-loops-289163855f87?source=friends_link&sk=72f44ce790e8381c6ff36f69f444abda)
> 
> 第三部分:[比较 JS 迭代方法](https://medium.com/@manumagalhaes/comparing-js-iteration-methods-map-filter-foreach-reduce-loops-f83323663ccb?source=friends_link&sk=73088e4e982adad63f8bdd0df4aa779c)

* * **
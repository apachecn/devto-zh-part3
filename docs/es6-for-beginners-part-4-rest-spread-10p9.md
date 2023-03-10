# 初学者 ES6 第 4 部分(休息、伸展)

> 原文：<https://dev.to/2nit/es6-for-beginners-part-4-rest-spread-10p9>

这是 ES6 教程的第四部分，由我们的 dev Bartosz 创建。所有之前的部分你都可以在这里找到:)。

## 休息

ES6 中的**休止符**是什么？rest 参数语法允许我们表示不定数量的参数。
我们知道在 JavaScript 中函数可以有任意数量的参数。此外，我们不必给每个人一个论点。这在 [JavaScript 第 17 部分](https://www.2n.pl/blog/javascript-part-17)中提到过。
现在，假设我们有一个函数，它有未知数量的参数。当我们不知道参数的数目时，用户简单地添加几个参数的例子。使用 ES6 之前的标准，我们可以按以下方式完成。

[![](img/f729271ccaab0095d98f90c67f380458.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8XZs9cgT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vzv7d0dx0oe7el1uhzci.png)

因为参数不是存储在数组中，而是以类似数组的格式存储，所以我们必须创建一个由参数组成的新数组。ES6 让我们可以使用 **rest** 参数轻松完成这项工作。

[![](img/2d4b7e4b7e58524630f31c35ae4ce6ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hPQYAV3v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jy45935ojdvm8qxysi9t.png)

使用 **myAdd 函数**，我们可以添加无限数量的参数。这样做的原因是通过使用三个点**...**和 **nameOfparameter** ，所有的实参都将被扔进数组。

[![](img/5b0cbfe00aa75b4c8bf7bc3cf89691f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--baYOoqIC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://2n.pl/ckeditor_assets/pictures/92/content_es5-1.png)

此外，我们必须注意正确使用 rest 参数，总是在传递的参数的末尾。这是因为，它不会只是工作。我们收到的错误肯定会提醒我们。

[![](img/28728e1d076eba405eb519f0a140fa4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DSDPzCRo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://2n.pl/ckeditor_assets/pictures/93/content_es5-3.png)

## 传播开来

它允许期望元素调用(对于数组调用)或元素(对于数组文字)的参数数组，或者在期望零个或多个键值对(对于对象文字)的地方扩展对象表达式。听起来很复杂？让我们看几个我们将如何使用它的例子。

[![](img/9f90501677ff093a74db577d226009f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nHsSa-Zm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ik326f5yvqima7xi9ct7.png)

[![](img/c78f3d9feac801a5988c6d66ed2c3ea6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e-ZZ7Jyu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://2n.pl/ckeditor_assets/pictures/94/content_es5-5.png)

正如你可以很容易地观察到的，我们可以组合数组。

在下一篇文章中，我将讨论数组助手方法，敬请关注！感谢您的阅读，请不要犹豫发表您对这篇文章的看法，我们将非常感谢您的所有反馈:)。
# 初学者 ES6 第 1 部分(Let，Const)

> 原文：<https://dev.to/2nit/es6-for-beginners-part-1-2mnm>

我想向你展示由我们的开发人员 Bartosz 撰写的博客文章系列“面向初学者的 ES6”的第一部分。[下一部分即将推出！](https://www.2n.pl/blog)。

# **简介**

首先，我要祝贺每一个读过我的一些关于 [JavaScript](https://www.2n.pl/blog?tag=JavaScript+for+beginners) 帖子的人！

当然，没有什么可以阻止你从这里开始，但我认为理解[前面的部分](https://www.2n.pl/blog?tag=JavaScript+for+beginners)比阅读 ES6 中的新功能重要得多。然而，如果你想马上从这个地方开始，没有什么可以阻挡你

我将涉及的主题有:

1.  let / const
2.  箭头功能
3.  班
4.  扩展运算符/剩余参数
5.  解构
6.  数组助手方法
7.  承诺/获取

### **好了，首先，ES6 是什么？**

ES6 基本就是 ECMAScript 6 / ECMAScript 2015。ECMAScript 不是任何脚本语言，而是 Javascript 所基于的标准。所以，ES6 是 JavaScript 的新版本或新标准。ES6 带来了许多新的特性，比如类的概念，箭头功能等等。几乎所有的现代浏览器都支持 ES6，但是对于旧的浏览器，有许多 trans piler，例如 Babel.js，我们需要将它们包含在我们代码的顶部，以便将 ES6 转换成 ES5(旧标准的 JavaScript)。所有流行的 JavaScript 库和框架如 Node.js、ReactJS 都遵循 ES6。

# **我们将从一些非常简单但也非常有用的东西开始。*让*和*保持不变*。**

我们都知道 *var* 关键字是什么。我们已经用过很多次了。新变量？ *var* ，新增函数表达式？ *var* 等。ES6 引入了另外两个关键字，分别是 *let* 和 *const* 。这并不意味着 *var* 现在会停止工作，但是非常鼓励你使用新的。

### **让**

简而言之，它真的是一个新的 *var* 。Let 被鼓励在任何地方使用，而不是提到的 *var* 。我所说的“任何地方”是指除了当我们想使用 *const* 的时候以外的任何地方(解释如下)。例如，如果你想创建一个简单的变量，使用 *let* 。

[![](img/361b9f76249dd4aee394786805ae42fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qDaHCpTj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/58ol6wi0g4sh2y8zc81q.png)

[![](img/1e999524118dec7942ff12639032c835.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5TQhvke2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/794885u1gomqjrne1c5g.png)

正如你在这个例子中看到的，没有区别。现在一个合乎逻辑的问题是，如果没有区别，为什么要引入 JavaScript？

[![](img/72e8473fb239c6e408c7498031ba7aff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7UPc67hw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t1hl9whtd88gzv7qtsbz.png)

我们再来看一个例子。这里我们可以看到包含两个循环的函数*。唯一不同的正是 *var* 和 *let* 。但是，如果我们想在控制台中检查 *I* 和 *j* 的值，在代码执行的块之外，我们会得到两个不同的结果。*

当 *var i = 0* 时，我们的 *i* 变量就可见了。

[![](img/b8b4b851e1ad9e9da26e50768ae81826.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8YZZGITM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ruqflf769naut4b3uaww.png)

但是，如果我们使用*让 j = 0* ，我们的 *j* 变量将不可见。

[![](img/12700eddd752d3101579972cd7023d3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xhxi9ixU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/s7t46nrvvdgj4aqz04nq.png)

### **常数**

我们希望使用 *const* 关键字的主要原因是我们不希望变量在后面的代码中被重新声明。例如，我强烈建议像使用旧的 *var* 一样使用 treat *const* ，并在需要时更改为 let。让我们看看当我们决定改变使用 *const* 关键字声明的变量时会发生什么。

[![](img/e6615a61bddb097746ab102c6d2f4d9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Sxp8HKSK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/om6bpxlcjb273ckcxqj5.png)

[![](img/bf7cf1ed5192a4304c5ab4d2dab44e87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gOUrIX26--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w4od0spc6huyq825nxvc.png)

错误:)

当然，仍然可以使用诸如 *push* 等方法，但是如上所示，任何对先前声明的值的“手动”更改都将以错误结束。

[![](img/8e1433699d74f61f1021107e0801d9be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5Jj4SPHB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tiqgxfbxdb36uj0dsqxf.png)

[![](img/639710a3fdd7a5015713d5263a89b585.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hJERkmD---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qjetksoce0l88flldmip.png)

我们的数组被修改了。

在下一篇文章中，我们将讨论列表中的另一个主题:箭头函数。

#### [下一篇帖子即将发表！](https://www.2n.pl/blog)
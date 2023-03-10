# JavaScript ES6 中的新语法

> 原文：<https://dev.to/girikon/new-syntax-in-javascript-es6-336e>

据 **[Girikon 的 Salesforce 咨询服务团队](https://www.girikon.com/salesforce-consulting-services/)** 介绍，JavaScript 近年来发展非常迅速，至今仍是一种运行于各种平台的非常强大的编程语言。如果你在 2017 年学习 JavaScript，还没有接触过 ES6，你就错过了一个更简单的方法来读写 JavaScript。

[![SALESFORCE CONSULTANTS](img/be70d3d9b25d1555210fc7aa4d4a124d.png)](https://www.girikon.com/wp-content/uploads/2019/05/img1.png)

ES6 指的是 ECMA 脚本编程语言的版本 6。ECMA 脚本是 JavaScript 的标准化名称，版本 6 是继版本 5 之后的下一个版本，是 JavaScript 的一个主要增强。

**让我们开始**

在 ES6 之前，我们在 JavaScript 中声明变量的唯一方式是使用 var 关键字。当我们在函数中使用 var 关键字声明变量时。这意味着该变量的范围只存在于声明它的函数中。如果我们声明了全局变量(在函数之外),这仍然是有意义的。

让我们看看这个例子:

[![code](img/774495563d10a4b947783764ebdf0aae.png)](https://www.girikon.com/wp-content/uploads/2019/05/img2.png)

你认为它会打印出什么？1 还是 2？

它将在函数中打印两个值(1 和 2)，首先是 2，然后是 1。这是因为调用函数时会打印函数作用域 2，而由于是全局作用域，第二次会显示 1。

你们中的大多数人都很容易理解这一点，而且一切都很好，直到我们在 if 语句中遇到代码，如下例所示:

[![ES6](img/275592784a1b3cc2694203ca0848e49f.png)](https://www.girikon.com/wp-content/uploads/2019/05/img3.png)

代码打印 2 次，因为 var 关键字不支持块范围。这个例子对你来说毫无意义。块是花括号内的任何代码。块作用域确保在那些括号中定义的任何变量不会变成全局变量，而是具有局部作用域。这种类型的控制可以防止代码中出现意外行为。

**“Let”是新的 Var**

缺乏块范围给 JavaScript 开发人员带来了很多麻烦，尤其是在 for 循环中的变量声明期间。因此，对于这个 ES6 引入的 let 关键字，任何用 Let 赋值的变量总是有 block 作用域，不能被提升。如果我们使用 let 关键字而不是 var，那么就不容易出错，并避免所有令人困惑的错误。

**常数**

ES6 还引入了另一个关键字 const，当你需要声明一个不能重声明的变量时，这很有用。Const 关键字也在阻止范围内，不能被提升。然而，在使用 const 关键字时需要注意一些事情，因为 const 值不能被重新赋值，它们必须在声明时被初始化。

只是不要忘记常量是不可变的，所以当处理对象或数组时，只有对象本身不能被重新分配。可以更改对象或数组中属性，例如:

[![Function](img/7b6a797edc40153aa7716643f072f7c3.png)](https://www.girikon.com/wp-content/uploads/2019/05/img4.png)

我们可以执行下面的代码，name 属性将被重新分配，而不会抛出错误。

为什么同样的东西要打两次？

开发人员总是试图从数组或对象中获取数据，为此，他们使用这样的代码，其中对象的属性使用如下变量进行初始化:

[![object](img/e9d3f13644b68168b23fd1cd2bee8396.png)](https://www.girikon.com/wp-content/uploads/2019/05/img5.png)

在 ES6 中，如果变量和对象属性名称相同，您不再需要重复。这段代码完成了同样的事情:

[![salesforce consulting](img/e650b23f6c689048c62c6f225801cd0e.png)](https://www.girikon.com/wp-content/uploads/2019/05/img6.png)

我们在这里所做的只是删除了重复的变量名和冒号(:)。当我们的对象包含许多字段时，这非常有用。

ES6 还提供了一种脱离数组或对象更简单的方法。这有助于减少重复的代码行示例:

[![consulting](img/9cfec93b1f7bc6783b4a4e951b90d781.png)](https://www.girikon.com/wp-content/uploads/2019/05/img7.png)

现在，您可以通过变量名来访问数据。因此，这里的数字 1 将被打印到控制台。但是你可以用另一种简化的方法来代替，叫做数组去结构化。

[![structuring](img/92f0b2adc7c864498224d51fe883a703.png)](https://www.girikon.com/wp-content/uploads/2019/05/img8.png)

赋值左边的括号是新的 de 结构语法的一部分。这就像四个变量，分别命名为 1、2、3 和 4，将数字数组中的第一个值赋给变量 1，第二个值赋给变量 2，依此类推。更短，更甜，很好。

我们认为，一旦你开始使用 ES6，你会像我们一样喜欢它们。

Girikon Salesforce Consultant 解释 JavaScript ES6 新语法的帖子首先出现在[sales force Consulting Company | United States | Phoenix，Arizona](https://www.girikon.com/) 上。
# 什么是 ECMAScript？它有什么新特点？

> 原文：<https://dev.to/kartik2406/what-is-ecmascript-what-are-its-new-features-31ck>

根据**维基百科**，*的说法，“ECMAScript(或 ES)是由 Ecma 国际在 ECMA-262 和 ISO/IEC 16262 标准化的注册商标的脚本语言规范。它的创建是为了标准化 JavaScript，以促进多种独立的实现。”*

标准化 ECMAScript 的委员会称为 TC39。其成员是各大浏览器厂商，其他公司。你可以在这里阅读更多关于 TC39 和它是如何工作的[。这些规范奠定了 Javascript 语言及其发展的基础。值得注意的是，Javascript 有一些不属于规范的额外特性。规范的好处是它为语言带来了新的特性，改善了开发人员的体验。](http://2ality.com/2015/11/tc39-process.html)

ECMAScript 的不同版本被命名为 ES# (ES1、ES2、ES3、ES5....)或按它们被添加的年份 ES2016。我们还有一个名为 ESNext 的东西，这些功能目前还没有最终确定并添加到规范中。您可以从这篇维基百科文章中了解更多关于 ECMAScript 的命名和历史。

### 发布的新功能

### ES5

这个版本发布了 array methods.map()，。reduce()，。过滤器()..，以及新的 JSON、string、date 函数。点击查看完整列表[。](https://www.w3schools.com/js/js_es5.asp)

### ES6

这个版本有很多有用的特性，让我们详细介绍其中的几个，

*   让和 const

现在使用这些关键字，您可以创建块范围的变量。使用 const 你可以创建块范围的常量。

*   模板字符串

当你想显示一条包含两种不同内容的消息时，你会怎么做？这很简单，你用一个+操作符把它们连接起来。

```
 'Hello there, ' + username + ' welcome to our app.' 
```

这样做的问题是，随着变量数量的增加，语法变得太大，重复，难以阅读。
模板字符串允许您使用反勾号字符形成一个字符串。您将字符串放在反勾号内，并使用${}在字符串中插入变量的值。上面的字符串会变成这个

```
 `Hello there, ${username} welcome to our app.` 
```

*   解构

通过析构，你可以从一个对象中提取某些属性，并用它们的值创建变量。这减少了您编写的代码量，并使其更具可读性。

```
 //before
  let name = user.name
  let age = user.age
  //now
  let { name, age } = user 
```

在这里，您可以直接提取和创建变量。

*   Promises
    Promises 让异步世界变得简单了一点，把我们从未定义的结果问题和回调地狱中拯救了出来。

*   导入/导出语句
    以前，您必须使用 module.exports 导出和 require()请求模块。
    现在您可以使用 export 关键字从文件中导出任何内容，并使用 import 关键字将模块导入到您的文件中。

我在这里没有涵盖所有的特性，因为列表很大，你可以通过[这篇文章](https://webapplog.com/es6/)来详细了解它们。

### ES7

这个版本带来了两个很酷的东西

*   Array.includes()方法。

在此之前，检查一个元素是否存在于一个数组中，你必须使用
Array.indexOf(element)，它返回-1，这意味着该元素不是数组的一部分，否则它就是数组的一部分。与。include()你可以跳过这个额外的检查。它将返回一个布尔值，如果元素存在则为 true，否则为 false。

*   指数算子

** '是指数运算符。现在不需要使用 Math.pow()可以直接使用这个运算符。

```
 console.log(2 ** 3)
  Output: 8 
```

### ES8

这个版本为对象、字符串类型带来了许多特性。你可以在这里阅读关于他们的所有内容。
我最喜欢的功能是异步/等待。使得异步编程更加简单。

### 2018 年的新特点

我最喜欢的特性是 rest 和 spread 正则表达式捕获组。点击查看完整列表[。](https://medium.freecodecamp.org/es9-javascripts-state-of-art-in-2018-9a350643f29c)

哇哦，太多了，对吧？所有这些都很酷，但是，你可能想知道如何跟踪这些？你可以通过关注一些博客和时事通讯来做到这一点。

1.  一个很棒的 JS 博客。
2.  《JS 周刊》,你会得到很棒的 JS 文章，发表的新闻。
3.  使用 Javascript 标签，你会看到人们写的关于新特性、新技术等等的很棒的东西。
4.  介质上的 FreeCodeCamp 出版物。

通过遵循这些事情并保持规律，你将能够提升你的技能。

如果你喜欢这篇文章，请分享它。此外，检查我的其他职位，我们已经详细讨论了上述几个功能。
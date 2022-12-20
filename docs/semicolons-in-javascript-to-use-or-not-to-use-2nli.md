# JavaScript 中的分号:用还是不用？

> 原文：<https://dev.to/adriennemiller/semicolons-in-javascript-to-use-or-not-to-use-2nli>

在熨斗学校的第一堂 JavaScript 课上，老师提到分号在 JavaScript 中是可选的……除非它们不是可选的🤔

我决定更多地研究 JavaScript 中分号的用法，以真正理解为什么我们会或不想使用它们，并使用这些知识来避免早期产生任何坏习惯。

<figure>[![Peter Arkle semicolon drawing](img/af341c6cdbab598cb6e23d7b5a0ec981.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x0MeKCLk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b9cnfk5ir1u339o34l1t.jpg) 

<figcaption>彼得·阿克尔，[https://opinionator . blogs . nytimes . com/2012/07/02/分号-a-love-story/](https://opinionator.blogs.nytimes.com/2012/07/02/semicolons-a-love-story/)</figcaption>

</figure>

# 自动插入分号(ASI)

分号在 JavaScript 中有时是可选的原因是因为自动分号插入，或 ASI。ASI 并不意味着将实际的分号插入到您的代码中，它更像是 JavaScript 使用的一组规则，这些规则将决定分号是否会在某些地方被解释。我在富栈学院找到了一个关于这个话题的很有帮助的讲座，[你可以在这里查看](https://www.youtube.com/watch?v=B4Skfqr7Dbs)。我还找到了布拉德利·布莱斯维特关于这个话题的[博客帖子](http://www.bradoncode.com/blog/2015/08/26/javascript-semi-colon-insertion/)。下面我重点介绍了这些资源的主要内容。

# 3 自动分号插入规则:

说到 ASI，这里有三个要点需要注意。

1-当遇到语法不正确的行结束符或' } '时，将插入分号。因此，如果在前一行代码之后解析新的一行代码仍然产生有效的 JavaScript，ASI 将不会被触发。

例 1:

```
var beef 
var cheese 
```

会变成:

```
var beef; 
var cheese; 
```

例 2:

```
var a 
    b= 
    3; 
```

在这里，文法不期望在 a 之后看到 b，所以触发了 ASI

```
var a; 
b = 3; 
```

例 3:

```
a = b + c 
(d + e).print() 
```

等于:

```
a = b + c(d + e).print(); 
```

2-如果程序到达了输入的末尾并且没有错误，但是它不是一个完整的程序，一个分号将被添加到末尾。这基本上意味着如果文件末尾缺少一个分号，就会添加一个分号。

3-在语法中的某些地方，如果出现换行符，它会无条件地终止语句，并添加一个分号。这方面的一个例子是 return 语句。

```
 function getCheese() {
    return 
    { 
       cheeseType: "Gouda"
    } 
} 
```

这将触发 ASI 并导致:

```
 function getCheese() {
    return; 
    { 
       cheeseType: "Gouda"
    } 
  } 
```

return 语句中的表达式应该在同一行开始，就像这样:

```
 function getCheese() {
    return { 
       cheeseType: "Gouda"
    } 
  } 
```

# 什么时候不应该用分号？

以下是一些不需要分号的情况:

如果(...) {...}否则{...}
对于(...) {...}
虽然(...) {...}

注意:在:do{...}而(...);

# 最后的想法

如果您打算编写不带可选分号的 JavaScript，至少了解 ASI 正在做什么可能是好的。例如，压缩或缩小可能会导致您的有效代码抛出错误，因为这些程序可能依赖于分号。

此外，如果没有分号，调试会更加困难，因为您的代码可能会在您没有意识到的情况下连接在一起。如果你在不应该换行的地方换行，ASI 可能会跳转到一个分号，即使不应该有分号。

<figure>[![keep calm it's only a semi colon](img/1e382b3f83c74a6fb38665fd1788ba31.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TKo0VQLF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lvvdxjl9hgmgc0y1h4ts.jpg)

<figcaption>[https://www . everywordcounts . co . uk/no-need-to-be-shorted-of-seminals/](https://www.everywordcounts.co.uk/no-need-to-be-scared-of-semicolons/)</figcaption>

</figure>

公司和开源项目可能会有这样或那样的偏好，所以一定要注意它是什么。

最后，如果您认为您可能会因为 ASI 而遇到错误，请查看 Babeljs.io 进行调试——它允许您放入代码并向您显示 ASI 分号插入的位置。

当谈到 JavaScript 中的分号时，你发现什么是正确的？
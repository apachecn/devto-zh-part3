# 编写自己的 Javascript 契约和文档字符串

> 原文：<https://dev.to/stephencweiss/write-your-own-javascript-contracts-and-docstrings-42ho>

我第一次接触代码是在 John V. Guttag 的《使用 Python 进行计算和编程入门》。虽然我没有学习 Python，但我接触了某些概念。

[当我进入 Javascript 时，有些会把我引入歧途](https://stackoverflow.com/questions/51685550/why-do-i-need-length-to-find-the-equivalence-of-array-elements-in-a-for-loop)——比如 Python 能够用索引`-1`访问数组的最后一个元素。

其他的，比如 docstrings，似乎没有被重视。“提供功能规范”的文本，可以使用内置的`help`来访问。

但这就是我错的地方——Javascript*有*docstring(或其等价物),它们是我*喜欢的 VSCode 中的*工具提示。

<figure>[![Array.lastIndexOf](img/5b0233fbee5ef677202ec34bfbf9a730.png)](https://i2.wp.com/www.stephencharlesweiss.com/wp-content/uploads/2018/12/arrayLastIndex.png?ssl=1) 

<figcaption>内置阵法。lastIndexOf()</figcaption>

</figure>

<figure>[![Array.length](img/c9e90a5870dee1a92776e4715e20252e.png)](https://i0.wp.com/www.stephencharlesweiss.com/wp-content/uploads/2018/12/arrayLength.png?ssl=1) 

<figcaption>内置数组属性。</figcaption>

长度()</figure>

这些指南很棒，因为当你使用这种方法或询问房产时，它们清楚地表达了你同意的合同。

这就像你的编辑器里有 MDN 一样。没必要离开去找些东西。事实上，这正是我最初认为会发生的事情——vs code 直接插入 MDN 来下拉定义。

事实证明这不是真的。相反，写自己的描述其实很容易。

# 写自己的合同

通常情况下，Javascript 提供了几种不同的方式来编写注释。

有`//`为单行注释。

有`/* */`用于单行或多行注释。最后，还有`/** */`。这是一个描述。我认为 Javascript 相当于 Python 的 docstring。
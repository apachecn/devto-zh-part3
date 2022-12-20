# Mathjax 可以像 mathquill 一样编辑，可能吗？

> 原文：<https://dev.to/dirkncl/mathjax-can-be-edited-like-mathquill-is-it-possible--2oa7>

对于那些今天仍在学习和研究 html 和 javascript 的伟大之处的人来说，最好多学学原始语法，这样会更有效率，但文件也不会很大..

HTML / javascript 技术的发展如此之快，曾经不可能的事情，现在都有可能了。如果没有 javascript 程序，Mathjax 可以像 Mathquill 一样直接编辑，为了改进编辑，它可以使用一个简单的 JavaScript 程序。

几乎所有的 html 页面都可以用新的 html 语法来编辑，但是我们应该选择重要的元素。

1.  所有 htm 页面的示例都可以编辑:

```
<body contenteditable='true'>
Equation*:

\begin{equation*}
E = mc^2
\end{equation*}

<hr>
Brackets:

\[E = mc^2\]

Brackets tagged:

\[E = mc^2\tag{x}\]

</body> 
```

1.  元素示例:

```
<div contenteditable='true'>\[E = mc^2\tag{x}\] </div>
<span contenteditable='true'>\[E = mc^2\tag{x}\] </span> 
```

1.  用一段 javascript 代码:

```
<span id='mj'> 
\begin {equation}
E = mc ^ 2
\end {equation}
</span>
<script>
document.getElementById ('mj'). ondblclick = function () {return document.getElementById ('mj'). setAttribute ('contenteditable', 'true')};
   document.getElementById ('mj'). onclick = function () {return document.getElementById ('mj'). setAttribute ('contenteditable', 'false')};
</script> 
```

### 结论。

结论是没有结论，因为这对我来说还是一个新事物。

我自己也还在根据自己的推理能力尝试和尝试智胜招数，因为没有论述这个的参考文献(Mathjax 可以编辑(？？？).
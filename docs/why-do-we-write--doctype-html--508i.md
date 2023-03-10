# 我们为什么写作？

> 原文：<https://dev.to/blueturtle/why-do-we-write--doctype-html--508i>

是的，这是给绝对初学者的，也是给那些忘记了的非初学者的。我将回顾过去，通过问“什么”、“为什么”和“如何”来重新发现我们认为理所当然的事情。

* * *

#### 我们为什么要写<！DOCTYPE html >？

```
<!DOCTYPE html>
<html lang="en">
    <head>

    </head>
    <body>

    </body>
</html> 
```

`<! DOCTYPE html>`是写在任何 html 文件开头的第一个 html 标签。它不是元素，没有内容或结束标记。
它的工作是告诉浏览器正在呈现的文档是一个 HTML 文档。这就是它的位置成为浏览器首先读取的内容的原因。

* * *

#### 但是如果我们不写会怎么样呢？

如果浏览器没有找到 [DOCTYPE](https://developer.mozilla.org/en-US/docs/Glossary/Doctype) 声明，它将进入[怪癖模式](https://en.m.wikipedia.org/wiki/Quirks_mode)🤔这是浏览器用来尝试支持为 Navigator 4 和 Internet Explorer 5 开发的旧网页的一种技术。[阅读这里](https://developer.mozilla.org/en-US/docs/Web/HTML/Quirks_Mode_and_Standards_Mode)。这意味着你的网站在不同的浏览器下会有不同的行为和外观，尤其是如果它有新的 HTML 元素和特性。
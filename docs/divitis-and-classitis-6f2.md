# Divitis 和 classitis

> 原文：<https://dev.to/gdiazderadaa/divitis-and-classitis-6f2>

这是一个关于疾病的帖子吗？-你可能想知道。绝对的。我想谈谈在我们的 HTML 中使用 div 和类的弊病，当它们不是必需的或最佳选择时。让我们卷起袖子。

# 除数

大多数网站确实在标记中滥用 div。转到你最喜欢的一个网站，检查 HTML。我很确定你可以很容易地找到这样的东西:

```
<div id="posts">
    <ul>
        <li>My first post!<li>
        <li>I'm committed to write more...</li>
        <li>Quite a few more</li>
    </ul>
</div> 
```

CSS 中的目标:

```
div#posts > ul { background-color:#000; } 
```

然而，实际上并不需要用 div 来包装列表，因为我们可以将 id 分配给列表:

```
<ul id="posts">
    <li>My first post!<li>
    <li>I'm committed to write more...</li>
    <li>Quite a few more</li>
</ul> 
```

并且比以前更加简单:

```
ul#posts { background-color:#000; } 
```

我可以更进一步。我们见过多少次像下面这样的事情？

```
<div class="small-text">
    This is an awesome post about how maniac I am when it comes to semantic markup
</div> 
```

正确的标记应该是:

```
<p class="small-text">
    This is an awesome post about how maniac I am when it comes to semantic markup
</p> 
```

这只是 div 标签误用的两个例子，它使你的代码更大，更难阅读和维护。

# classis is

这是 10 年前不常发生的事情。它代表了在 HTML 中滥用类，我们可以使用 id 来代替。看看下面的例子:

```
<nav class="top-nav">...</nav>
...
<nav class="bottom-nav">...</nav> 
```

听起来很熟悉？我打赌是的。可能大多数人会使用这种方法来定位 css 和 javascript 中的这些元素。但是等一下，有更好的方法吗？

根据 w3schools.com:

> HTML class 属性用于为具有相同类名的元素定义相同的样式。

鉴于:

> id 属性为 HTML 元素指定唯一的 id(该值在 HTML 文档中必须是唯一的)。

如果元素是唯一的，我们为什么不用 id 呢？

```
<nav id="top-nav">...</nav>
...
<nav id="bottom-nav">...</nav> 
```

我并不是说使用类是错误的，但是我确实认为 id 给了标记更好的语义。我们还可以在定位元素时节省一些 Javascript 的麻烦:

```
var topNav = document.getElementsByClassName('top-nav')[0]; 
```

vs

```
var topNav = document.getElementById('top-nav'); 
```

我们可能不会节省几十行代码，但是它看起来更干净，并且正确地使用了 HTML 元素。

我希望你喜欢我的第一个帖子，并发现它很有用！希望看到您的评论！
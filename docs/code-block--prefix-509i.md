# 代码块$前缀

> 原文：<https://dev.to/glennmen/code-block--prefix-509i>

我经常使用数字海洋社区教程，例如如何在 Linux 中设置不同的东西(Nginx，MySQL，防火墙，...)并建议每个人都使用它们。

大约 3 周前，我实际上注意到他们的代码块，其中一些(包含终端命令)以`$`开始，没什么奇怪的，对吗？
实际上是因为你不能复制它，它只会复制`$`前缀后的代码。这方面的一个例子可以在找到[。](https://www.digitalocean.com/community/tutorials/how-to-install-linux-apache-mysql-php-lamp-stack-on-ubuntu-16-04#step-1-install-apache-and-allow-in-firewall)

这引起了我的兴趣，我以前从未注意到这一点，也找不到任何好的例子来说明这是如何做到的。于是我翻遍了页面源代码，找到了这个小艺术品神奇的源代码。

它实际上是一些不错的旧 HTML 与一点 CSS 魔法的结合。我将尝试解释不同的部分，并在最后展示一个工作的 JSFiddle 示例。

**HTML**T3】HTML 部分其实挺简单的。这是正常的代码块语法，其中包含一个无序列表。这个例子展示了一个多行的代码块，但是如果你只想要一个单行的代码块，你也可以只使用一个列表项(`<li>`)。

```
<pre>
    <code>
        <ul class="prefixed">
            <li class="line">sudo apt-get update</li>
            <li class="line">sudo apt-get install apache2</li>
            <li class="line">sudo apache2ctl configtest</li>
        </ul>
    </code>
</pre> 
```

我会把这一部分分割成更小的部分。

第一部分仅用于减少每个列表项之间的间距。没什么特别的。

```
pre .prefixed {
    white-space: normal;
    margin: 0;
} 
```

好了，现在我们开始第一个重要的部分。`list-style-type`用于删除默认列表项样式:`•`

```
pre .line {
    list-style-type: none!important;
} 
```

这最后一部分用于在代码块前面添加前缀`$`。第一部分是确保用户不能复制它。页边距用于在前缀周围增加一点间距，当然你可以随意调整。
最重要的部分是`content: "$";`，它给上面的所有行添加了我们想要的前缀。

```
pre .line::before {
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    -o-user-select: none;
    user-select: none;
    content: "$";
    margin-right: 10px;
    margin-left: 5px;
} 
```

**奖励**
我想把这个作为一点小奖励加进去。
也可以给每一行添加不同的前缀，而不需要为每一行创建不同的 CSS 样式。
添加`prefix`属性，使用您喜欢的任何值。

```
<li class="line" prefix="$">sudo apt-get install apache2</li> 
```

在最后一个 CSS 块中，用这个替换`content: "$";`。它将获取属性值并将其用作前缀。

```
content: attr(prefix); 
```

[https://jsfiddle.net/2ds89evp//embedded/result,html,css//dark](https://jsfiddle.net/2ds89evp//embedded/result,html,css//dark)

这是我第一次教编码教程，所以我希望你会喜欢。当然欢迎任何反馈。
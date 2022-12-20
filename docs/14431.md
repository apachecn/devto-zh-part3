# 如何在 Web 上实现可变字体

> 原文：<https://dev.to/fridanyvall/how-to-implement-variable-fonts-on-the-web-51li>

本文旨在讨论在 web 上实现可变字体时需要考虑的问题，以及如何为网站添加可变字体。

在撰写本文时，大多数主流浏览器都支持可变字体，大约 78%的用户可以使用这些字体。因此，没有理由不利用这项技术。

如果你需要一个快速更新的主题，看看我们的博客文章[“网络上的可变字体”](https://redonion.se/en/variable-fonts-on-the-web/)对可变字体的概述。

* * *

## 表现

***子集化***
可变字体文件往往会变得相当大，因为它们包含了不同字体变化的大量数据。如果可行的话，最常见的做法似乎是设置字体的子集。不过，有一个警告，似乎很多用于创建字体子集的工具有时也会删除保存字体数据可变部分所需的数据。

据说工具 [Glyphhanger](https://www.npmjs.com/package/glyphhanger) 在子集化可变字体时不会删除任何不必要的数据。 [Fonttools](https://github.com/fonttools/fonttools) 是另一种选择。

***woff2***
将文件转换成 woff2 格式也是尽量保持轻量级的一种方式。这项工作的众多工具之一是谷歌的命令行工具，名为 [woff2](https://github.com/google/woff2) 。

***@supports***
现代浏览器只在需要时才下载字体——也就是当它被用于与连接到 DOM 的节点相匹配的 CSS 选择器时。这意味着可以使用`@supports`在支持的浏览器中只下载字体。

* * *

## 坐标轴

这些是默认的或“注册的”轴。请记住，不是所有的字体都会使用所有的轴。即使定义了轴，字体中定义的值可能只是可能值范围的一部分。

**wght** 对应`font-weight`。值是 1 到 999 之间的任意值。也可以使用关键字:

*   薄或发际线(100)
*   超轻型或超轻型(200)
*   灯(300)
*   正常(400)
*   中等(500)
*   半粗体或半粗体(600)
*   粗体(700)
*   超粗体或超粗体(800)
*   黑色或厚重(900)
*   继承/初始/未设置

**wdth** 对应`font-stretch`。价值介于 50%到 200%之间。也可以使用关键字:

*   超浓缩(50%)
*   超浓缩(62.5%)
*   浓缩的(75%)
*   半浓缩(87.5%)
*   正常(100%)
*   半膨胀(112.5%)
*   扩展(125%)
*   额外扩展(150%)
*   超扩展(200%)
*   继承/初始/未设置

**意大利**对应`font-style: italic`。值从倾斜-90 度到倾斜 90 度。

**slnt** 对应`font-style: oblique`。介于-90 度和 90 度之间的值，默认值为 14 度。

**opsz** 对应`font-optical-sizing`。设置为自动或无。初始值是 auto，这意味着如果轴存在于正在使用的可变字体上，它默认是活动的。

***斜体字与斜体字***
斜体字和斜体字的区别在于，斜体版本的字体既有角度，又有稍微调整的字形设计。倾斜的字体是倾斜的，也是这样调整的，但除此之外，与罗马字体(正常和直立)没有区别。

***字体优化调整***
字体优化调整是针对字体大小的优化，以提高字体的可读性。它用于根据使用环境调整字体。例如，可以对字体进行编程，以便在较大的屏幕上查看时添加修饰，删除较小文本上的细微细节，或者在某些情况下增加 x 高度(小写字母的高度，与字母“x”的高度对齐)，以提高可读性。

因此，实现了这个轴的可变字体使网站更容易访问。

***自定义轴***
字体设计师可以创建自定义轴，给它们起一个由任意 4 个字母组成的名字。自定义轴用于控制设计者希望能够逐渐改变的字体的任何其他方面。

* * *

## 给网页添加可变字体

向网页添加可变字体是通过@font-face 完成的。文件格式设置为文件类型。在以前的实现中，使用了后缀“-variations”，但是现在似乎不需要它了。

```
@font-face{
    font-family: "variableFont";
    src: url("variableFont.woff2") format("woff2");
} 
```

***字体-变化-设置***
要控制轴和设置字体变化，可以使用 CSS 属性`font-variation-settings`，也可以单独使用属性`font-weight`、`font-stretch`、`font-style`和`font-optical-sizing`。

根据[规范](https://drafts.csswg.org/css-fonts-4/#font-variation-settings-def)，首选方式是分别使用每种风格的属性。原因是`font-variation-settings`用于定制轴。如果你遇到浏览器兼容性问题，`font-variation-settings`据传跨浏览器更稳定。

无论您使用哪种语法，您都需要找出可变字体支持哪些轴，以及它们的范围在哪些值之间。除非在你能找到的任何关于字体的文档中有规定，否则这些信息应该在字体文件的[字体变化(fvar)表](https://docs.microsoft.com/sv-se/typography/opentype/spec/fvar)中找到。

提取这些信息最简单的方法是将字体放入[wakamaifondue.com](https://wakamaifondue.com/)中，希望你需要的信息会显示出来。如果不是这样，你可能需要结合使用像 [FontView](https://github.com/googlei18n/fontview) 和 [Fonttools](https://www.npmjs.com/package/fonttools) 这样的工具，在这篇[文章](https://webinista.com/updates/how-to-find-variable-font-axis-fonttools/)中有更深入的描述。

***添加轴范围***
指定您希望使用的轴的范围。否则，该属性可能会使用默认回退，这有可能会妨碍可变字体的使用。例如，将来`font-weight`将获得默认的自动回退，这意味着可能不需要该范围。

```
@font-face{
    font-family: "variableFont";
    src: url("variableFont.woff2") format("woff2");
    font-weight: 1 999;
} 
```

***后退***
照常使用`@font-face`添加不可变字体。

```
/*Fallback fonts*/
@font-face {
    font-family: "fallbackFont";
    src: url("fallbackFont-regular.woff2") format("woff2"),
         url("fallbackFont-regular.woff") format("woff");
    font-weight: 400;
}

@font-face {
    font-family: "fallbackFont";
    src: url("fallbackFont-bold.woff2") format("woff2"),
         url("fallbackFont-bold.woff") format("woff");
    font-weight: 700;
} 
```

添加`@supports`规则。如果您在不同的浏览器中遇到不一致的粗细行为，请尝试添加您希望每种字体使用的默认粗细。(建议来自[“如何在现实世界中使用可变字体”](http://clagnut.com/blog/2390/)。)

```
html {
    font-family: "fallbackFont", sans-serif;
    font-weight: 400; /*if inconsistent weight behavior*/
}

strong, b {
    font-weight: 700;
}

@supports (font-variation-settings: normal) {
    html {
        font-family: "variableFont", sans-serif;
        font-weight: 400;
        /*if you run into browser compatability problems,
        try the syntax:
        font-variation-settings: "wght" 400; */
    }

    strong, b {
        font-weight: 625;
    }
} 
```

***未来语法***
未来，语法会发生变化，使其有可能在`format()`部分加入浏览器支持检查。这将使得在`@supports`规则之外使用可变字体成为可能，并且仍然有一个后备解决方案。

```
/*Future syntax*/
@font-face {
    font-family: "futureVariableFont";
    src: url("fallbackFont.woff2") format("woff2"),
          url("fallbackFont.woff") format("woff");
    src: url("futureVariableFont.woff2") format("woff2" supports variations);
} 
```

***忠告***
由于规格可能会改变，技术也在不断发展，所以测试是确保字体在对您最重要的浏览器和浏览器版本中按预期显示和运行的关键。

可能仍有未被发现的怪癖和边缘情况需要解决。如果您遇到了这些问题，请与社区分享您的经验和解决方案。这使得所有人都有可能跟上网络世界的快速变化。

* * *

*最初发表于 [redonion.se](https://redonion.se/en/how-to-implement-variable-fonts-on-the-web/) 。*
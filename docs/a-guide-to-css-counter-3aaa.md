# CSS 计数器指南

> 原文：<https://dev.to/samanthaming/a-guide-to-css-counter-3aaa>

# CSS 指南`counter`

[![Code Tidbit by SamanthaMing.com](img/8fff20090a5dfa0137a0cafbe98f7533.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BAsPS0jh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pam9nhics4czhixtczc3.png)

使用“counter”属性将任何元素转换为编号列表。类似于有序列表标签的工作方式`<ol>`。如果您正在创建一个需要自动编号标题或创建目录的文档网站，这将非常有用👍

```
div {
  /* Define & Initialize Counter */
  counter-reset: tidbit-counter;
}

h2::before {
  /* Increment Counter */
  counter-increment: tidbit-counter;

  /* Display Counter */
  content: counter(tidbit-counter) ": ";
} 
```

```
<div>
  <h2>HTML</h2>
  <h2>CSS</h2>
  <h2>JS</h2>
</div> 
```

* * *

## 计数器属性如何工作

让计数器工作有 3 个步骤:

1.  定义并初始化计数器
2.  增量计数器
3.  显示计数器

### 1。定义&初始化计数器

这一步有两个部分。您需要定义您的计数器并给它一个名称。

**1a。定义计数器**T3 我已经把我的命名为`tidbit-counter`。我们给它一个名字，这样我们可以在后面的步骤中调用它。

```
counter-reset: tidbit-counter; 
```

**1b。初始化计数器**

下一步是初始化你的计数器。默认情况下，该值为`0`。请注意，此数字不会显示。这就是你设置计数器“开始”的地方。所以如果我把这个数设为`20`，那么我的输出将会是`21, 22, 23...etc`。假设我的增量是`1`(这个后面再讲)。

| `counter-reset` | 输出 |
| --- | --- |
| Zero | 1, 2, 3 ...等等 |
| Twenty | 21, 22, 23...等等 |
| Fifty-eight | 59, 60, 61...等等 |

这里有一个例子:

```
div {
  counter-reset: tidbit-counter 58; /* 👈 */
}

h2::before {
  counter-increment: tidbit-counter;
  content: counter(tidbit-counter) ": ";
} 
```

```
<div>
  <h2>HTML</h2>
  <h2>CSS</h2>
  <h2>JS</h2>
</div> 
```

```
59: HTML
60: CSS
61: JS 
```

**在哪里应用`counter-reset`属性？**

您希望在父元素上应用`counter-reset`属性。如果你不把它应用到父母身上，会发生什么。

```
/* ❌ Wrong */
h2 {
  counter-reset: tidbit-counter;
}

h2::before {
  counter-increment: tidbit-counter;
  content: counter(tidbit-counter) ": ";
} 
```

这里是**输出**。正如你所注意到的，它没有正确地增加🙁

```
1: HTML
1: CSS
1: JS 
```

此外，它不一定是直接的父母。只要它是包装计数器列表的 HTML 元素。你很棒。像这样:

```
<section>
  <div>
    <h2>HTML</h2>
    <h2>CSS</h2>
    <h2>JS</h2>
  </div>
</section> 
```

```
/* ✅ This works */
section {
  counter-reset: tidbit-counter;
} 
```

```
1: HTML
2: CSS
3: JS 
```

### 2。增量计数器

一旦你设置好柜台。现在你可以开始增加它。该属性的语法如下:

```
counter-increment: <counter name> <integer> 
```

正如您所注意到的，它接受整数参数。这意味着您不仅限于增加计数器值`1`。下图假设`counter-reset`是`0`。

| `counter-increment` | 输出 |
| --- | --- |
| 1(默认) | 1, 2, 3 ...等等 |
| five | 5, 10, 15...等等 |
| -5 | -5, -10, -15...等等 |

是的，你也可以传入一个负整数来减少计数器。好的，让我们看看这是如何实现的:

```
div {
  counter-reset: tidbit-counter;
}

h2::before {
  counter-increment: tidbit-counter -5; /* 👈 */
  content: counter(tidbit-counter) ": ";
} 
```

```
<div>
  <h2>HTML</h2>
  <h2>CSS</h2>
  <h2>JS</h2>
</div> 
```

```
-5: HTML
-10: CSS
-15: JS 
```

### 3。显示计数器

最后，为了显示计数器，我们需要传递`counter`函数作为我们的`content`属性的值。`content`属性通常是我们通过 CSS 在 HTML 中显示值的方式。下面是我们的`counter`函数的语法。

```
counter(<counter name>, <counter list style>) 
```

默认情况下，我们一直在计算数字。这是默认的`counter list style`或者在文档中，他们称之为`style`。但是你也可以通过其他的`styles`。

| `style` | 输出 |
| --- | --- |
| *默认* | 1, 2, 3 ...等等 |
| 上阿尔法 | 甲、乙、丙...等等 |
| 下罗马 | 一、二、三...等等 |
| 泰国人 | 2010 年 12 月 15 日..etc |

你可以在这里看到完整的风格列表

我们来看一个例子:

```
div {
  counter-reset: tidbit-counter;
}

h2::before {
  counter-increment: tidbit-counter;
  content: counter(tidbit-counter, thai); /* 👈 */
} 
```

```
<div>
  <h2>HTML</h2>
  <h2>CSS</h2>
  <h2>JS</h2>
</div> 
```

```
๑HTML
๒CSS
๓JS 
```

* * *

## 多个计数器

您还可以通过简单地定义另一个计数器名称来设置多个计数器。

```
div {
  counter-reset: counter-one counter-two 100; /* 👈 */
}

h2::before {
  counter-increment: counter-one;
  content: counter(counter-one) ": ";
} 

h3::before {
  counter-increment: counter-two;
  content: counter(counter-two) ": ";
} 
```

```
<div>
  <h2>one</h2>
  <h2>one</h2>

  <h3>two</h3>
  <h3>two</h3>
</div> 
```

```
1: one
2: one

101: two
102: two 
```

* * *

## 嵌套计数器

您也可以设置嵌套计数器。你不用`counter`而是用复数形式`counters`。`counters`接受一个附加参数:

```
counter(<counter name>, <string>, <counter list style>) 
```

string 参数是一个字符串分隔符，用于指示如何分隔嵌套计数器的列表项。

| `string` | 输出 |
| --- | --- |
| `"."` | 1.1, 1.2, 1.3 ...等等 |
| `">"` | 1>1, 1>2, 1>3 ...等等 |
| `":"` | 1:1, 1:2, 1:3 ...等等 |

我们来看一个例子:

```
div {
  counter-reset: multi-counters;
}

h2::before {
  counter-increment: multi-counters;
  content: counters(multi-counters, ".") ": ";
} 
```

```
<div>
  <h2>Frameworks</h2>
  <div>
    <h2>Vue</h2>
    <h2>React</h2>
    <h2>Angular</h2>
  </div>
</div> 
```

```
1: Frameworks
  1.1: Vue
  1.2: React
  1.3: Angular 
```

## 计数器 vs `<ol>`

CSS 计数器不替代`<ol>`。如果你有一个编号的有序列表，那么你绝对应该仍然使用`<ol>`,因为使用正确的语义来构造你的 HTML 是很重要的。语义标记对于可访问性和 SEO 至关重要。

**`<ol>`为胜利**

这里有一个我应该使用`<ol>`的例子。在这个例子中，我只是列出了一些规则。使用有序列表`<ol>`是有语义意义的。

```
<h2>Rules</h2>

<ol>
  <li>You do not talk about Fight Club</li>
  <li>You do not talk about Fight Club</li>
</ol> 
```

**CSS `counter`为了胜利**

这里有一个我会使用 CSS `counter`的例子。在这个例子中，我有一个使用标题`h2`和段落`p`的文档页面。在这个例子中，有一个计数器更多的是一种视觉呈现。这个例子使用 CSS `counter`是有意义的。

```
<article>
  <h2>What is Vue.js?</h2>
  <p>Vue is a progressive framework for building user interfaces.</p>

  <h2>Getting Started</h2>
  <p>Visit Vuejs.org to learn more!</p>
</article> 
```

```
1: What is Vue.js?
Vue is a progressive framework for building user interfaces.

2: Getting Started
Visit Vuejs.org to learn more! 
```

☝️Can，你告诉我，我真的很喜欢 Vue.js😝

* * *

## 浏览器支持

CSS `counter`被所有主流浏览器支持，包括 Internet Explorer 8 和更高版本。

[我可以使用:CSS 计数器](https://caniuse.com/#feat=css-counters)

* * *

## 资源

*   [MDN Web 文档:使用 CSS 计数器](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Lists_and_Counters/Using_CSS_counters)
*   [MDN WebDocs:列表样式类型](https://developer.mozilla.org/en-US/docs/Web/CSS/list-style-type#Values)
*   [w3schools: CSS 反增量](https://www.w3schools.com/CSSref/pr_gen_counter-increment.asp)
*   [w3schools: CSS 计数器重置](https://www.w3schools.com/CSSref/pr_gen_counter-reset.asp)
*   [CSS 招数:反增量](https://css-tricks.com/almanac/properties/c/counter-increment/)
*   [CSS 招数:计数器复位](https://css-tricks.com/almanac/properties/c/counter-reset/)
*   [30 秒的 CSS](https://30-seconds.github.io/30-seconds-of-css/#counter)
*   [计数器和 Calc():解释两个鲜为人知的 CSS 特性](https://creativemarket.com/blog/counters-and-calc-two-little-known-css-features-explained)
*   [前::和后::的可达性](https://thatdevgirl.com/blog/before-after-accessibility)
*   [对 CSS 生成内容的可访问性支持](https://tink.uk/accessibility-support-for-css-generated-content/)

* * *

**感谢阅读❤**
问好！[insta gram](https://www.instagram.com/samanthaming/)|[Twitter](https://twitter.com/samantha_ming)|[脸书](https://www.facebook.com/hisamanthaming) | [媒体](https://medium.com/@samanthaming) | [博客](https://www.samanthaming.com/blog)
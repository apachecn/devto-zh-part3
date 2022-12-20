# CSS 选择器级别 4

> 原文：<https://dev.to/bnevilleoneill/css-selectors-level-4-15go>

[![](img/09beb3387c77103fd2a8412f3d915d05.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mg52qjY_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AeYS388NUetsDByxTD4EEfw.png)

选择器是 CSS 的核心部分。它们允许你选择某种类型的所有元素:

```
div {
  /* some styles to apply to all div elements */
} 
```

或者您可以选择一个元素，它是其父元素的最后一个子元素:

```
ul li:last-child {
  /* some styles to apply to only the last child of a list */
} 
```

当然，它们也允许你做更复杂的事情，比如选择一个列表中除了最后一个子元素之外的所有子元素。

事实上，有不止一种方法可以做到这一点，有些方法比其他方法更复杂。

比如比较这个:

```
ul li {
  /* Styles to apply to all children */
}
ul li:last-child {
  /* Styles to reset the previous styles because they don’t apply to the last child */
} 
```

对此:

```
ul li:nth-last-child(n+2) { 
  /* Styles to apply to all children except the last one */
} 
```

ul li 是 1 级选择器的一个示例。

最后一个孩子和第 n 个最后一个孩子是 3 级选择器的例子。

您可以将级别视为 CSS 选择器规范的[版本](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors#Specifications)，其中每个级别都添加了更强大的选择器。

在这篇文章中，我将根据截至 2019 年 1 月的[编辑草案规范](https://drafts.csswg.org/selectors-4/)在以下几个类别中为您概述最后一代选择器 level 4:

*   逻辑组合
*   属性选择器
*   语言伪类
*   位置伪类
*   用户操作伪类
*   输入伪类
*   树形结构伪类
*   网格结构选择器

在撰写本文时，4 级选择器的规范处于草案状态。在达到官方推荐状态之前可能会有变化，你会发现很多选择器要么是某些浏览器不支持，要么是需要加前缀(带:-moz-或者:-webkit-)。

对于每个选择器，我将给出一个到其[我能使用](https://caniuse.com/#home)页面的链接，这样您就可以看到哪些浏览器支持它(如果可用的话)，一个简短的描述，一个例子，以及一个到 Codepen 的链接，这样您就可以尝试它(即使它现在不工作，因为它将来可能会改变)。

记住这一点，让我们从逻辑组合类别的选择器开始。

[![](img/2063f22e152ef36aa267c13ae9842601.png)](https://logrocket.com/signup/)

### 逻辑组合

这一类别包括通过组合其他选择器来工作的选择器。

#### :非(选择器 1，选择器 2，…)

[浏览器支持](https://caniuse.com/#feat=css-not-sel-list)

它选择不匹配列表中任何选择器的元素，并将其作为参数。例如:

```
p:not(.beginning, .middle) {
  color: red;
} 
```

会给所有没有开始类和中间类的 p 元素一个红色。

该选择器的第 3 级版本只允许一个选择器，而不是两个或更多个选择器的列表。例如，上面的样式可以写成:

```
p:not(.beginning):not(.middle) {
  color: red;
} 
```

[https://codepen.io/eh3rrera/embed/xMGxvY?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/xMGxvY?height=600&default-tab=result&embed-version=2)

#### :是(选择器 1，选择器 2，…)

[浏览器支持](https://caniuse.com/#feat=css-matches-pseudo)

它选择与作为参数的列表中的任何选择器相匹配的元素。例如:

```
p:is(.beginning, .middle) {
  color: blue;
} 
```

将赋予所有具有开始类和中间类的 p 元素蓝色。

自上一个工作草案版本以来，最重要的变化之一是:[: matches()选择器被重命名为:is()并弃用了 Safari](https://github.com/w3c/csswg-drafts/issues/3258) (这是唯一一个完全支持该选择器的[浏览器)，因此它与:not()更好地配对，它的反义词。](https://caniuse.com/#feat=css-matches-pseudo)

如果需要向后兼容，可以将:matches()实现为:is()的别名。但是，:matches()以前叫做:any()，所以大多数浏览器都支持这个带前缀的伪类:

```
p:-webkit-any(.beginning, .middle) {
  color: blue;
}

p:-moz-any(.beginning, .middle) {
  color: blue;
} 
```

[https://codepen.io/eh3rrera/embed/daYRLJ?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/daYRLJ?height=600&default-tab=result&embed-version=2)

#### :其中(选择器 1，选择器 2，…)

在撰写本文时，还没有任何浏览器支持这个选择器。

它与:is()具有相同的语法和功能，但是选择器及其任何参数都不会影响选择器的特异性，后者始终为零。

特异性是应用于 CSS 规则的权重。如果两个选择器应用于同一个元素，那么具有更高特异性的一个会胜出。如果多个规则具有相同的特性，则 CSS 文档中找到的最后一个规则将应用于该元素。

该选择器可用于实现过滤器和覆盖与元素相关联的样式声明。

说明书给出了下面的例子:

```
a:not(:hover) {
  text-decoration: none;
}

nav a {
  /* Has no effect */
  text-decoration: underline;
}

/* With the new :where Level 4 selector */
a:where(:not(:hover)) {
  text-decoration: none;
}

nav a {
  /* Should work */
  text-decoration: underline;
} 
```

[https://codepen.io/eh3rrera/embed/daYzZg?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/daYzZg?height=600&default-tab=result&embed-version=2)

#### :has(相对选择器 1，相对选择器 2，…)

[浏览器支持](https://caniuse.com/#feat=css-has)(在撰写本文时，还没有任何浏览器支持这个选择器。)

该选择器将一个[相对选择器](https://drafts.csswg.org/selectors-4/#relative-selector)列表作为参数。如果任何相对选择器(当作为范围内的元素进行评估时)与元素匹配，则它选择该元素。

例如:

```
p:has(strong, em) {
  color: red;
} 
```

将为所有包含**或*标签的 p 元素赋予红色。
T3T5***

### 属性选择器

这个类别包括处理元素属性的选择器。

#### 【foo =‘bar’和】

[浏览器支持](https://caniuse.com/#feat=css-case-insensitive)

它选择一个 foo 属性值完全等于 bar 的元素，而不考虑其大小写。

例如:

```
p[class="text" i] {
  color: green;
} 
```

将为 class 属性的值为 TEXT、text 或 Text 以及其他组合的所有 p 元素赋予绿色。
[https://codepen.io/eh3rrera/embed/pGjVEg?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/pGjVEg?height=600&default-tab=result&embed-version=2)

#### 【foo = " bar " s】

在撰写本文时，还没有任何浏览器支持这个选择器。

它选择一个元素，该元素的 foo 属性值恰好等于 bar，并且区分大小写。

例如:

```
p[class="text" s] {
  color: green;
} 
```

将为 class 属性的值为 text(例如，不是 Text)的所有 p 元素赋予绿色。
[https://codepen.io/eh3rrera/embed/yZYjbJ?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/yZYjbJ?height=600&default-tab=result&embed-version=2)

### 语言伪类

此类别包括使用语言相关设置的选择器。

#### :方向(ltr)

[浏览器支持](https://caniuse.com/#search=dir)

它选择具有从左到右方向的元素，其中文档语言指定如何确定方向。另一方面，:dir(rtl)表示具有从右向左方向的元素。其他值并非无效，但不匹配任何内容。

例如:

```
p:dir(ltr) {
  background-color: red;
} 
```

将所有从左到右方向的 p 元素的背景色设置为红色。
[https://codepen.io/eh3rrera/embed/GzpdwV?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/GzpdwV?height=600&default-tab=result&embed-version=2)

#### :lang(zh，" *-hant ")

[浏览器支持](https://caniuse.com/#search=%3Alang)(在撰写本文时，任何浏览器都不支持该选择器的第 4 级版本)

它选择标记为中文(或任何其他语言，只需用任何其他语言代码替换 zh)或用繁体中文字符书写(或在任何其他字符系统中，只需用任何其他字符代码替换`*-hant`)的元素。

实际上，从 CSS 2 开始就可以使用这个选择器，但是，通配符语言匹配和逗号分隔列表是第 4 级选择器中的新功能。

它接受一个由一个或多个[语言范围](https://drafts.csswg.org/selectors-4/#language-range)组成的逗号分隔列表作为它的参数。如果语言范围包含星号，它们必须被正确转义(`:lang(es-\*)`)或作为字符串引用(`:lang("es-\*)`)。

例如:

```
p:lang("*-CH") {
  background-color: red;
} 
```

将瑞士使用的语言之一(CH 代表瑞士)的所有 p 元素的背景色设置为红色。
[https://codepen.io/eh3rrera/embed/YByjzp?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/YByjzp?height=600&default-tab=result&embed-version=2)

### 位置伪类

此类别包括与超链接相关的选择器。

#### :任意-链接

没有[我可以使用](https://caniuse.com/#home)页面，但是这个选择器被大多数主流浏览器支持。

它选择所有具有 href 属性的元素(如`<a>`或`<link>`)。换句话说，匹配`:link`或`:visited`伪类的所有元素。

例如:

```
a:any-link {
  color: red;
} 
```

将为所有具有 href 属性的 a 元素赋予红色。
[https://codepen.io/eh3rrera/embed/vbNaeJ?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/vbNaeJ?height=600&default-tab=result&embed-version=2)

#### :本地链接

在撰写本文时，还没有任何浏览器支持这个选择器。

它选择以当前 URL 为目标的元素。如果链接的目标包含一个片段 URL，那么当前 URL 的片段 URL 也必须匹配；如果不匹配，则在比较中不考虑当前 URL 的片段 URL 部分。

例如:

```
a:local-link {
  text-decoration: none;
} 
```

将防止所有具有针对当前页面的 href 属性的 a 元素被加下划线(可能它们是导航菜单的一部分)。
[https://codepen.io/eh3rrera/embed/GzpBwG?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/GzpBwG?height=600&default-tab=result&embed-version=2)

### 用户动作伪类

这个类别包括用户正在操作的元素的选择器。

#### :焦点-内

[浏览器支持](https://caniuse.com/#feat=css-focus-within)

它选择匹配:focus 伪类的元素(当元素有焦点时)或者具有匹配:focus 的子元素。

例如，假设以下形式:

```
<form> 
```

```
<input type="text" id="name" placeholder="Enter your name" /> 
```

```
</form> 
```

当它获得焦点时，将在输入框周围添加边框。

```
form:focus-within {
  border: 2px solid;
} 
```

[https://codepen.io/eh3rrera/embed/BMoGWx?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/BMoGWx?height=600&default-tab=result&embed-version=2)

#### :焦点-可见

[浏览器支持](https://caniuse.com/#feat=css-focus-visible)

如果一个元素当前被聚焦(它匹配:focus 伪类),它就选择该元素，浏览器确定焦点应该在该元素上变得明显，通常带有一个聚焦环。

与:焦点的区别是微妙的。

If :focus-visible 匹配，:focus 也会匹配，但反之则不一定，这取决于浏览器(如果它启用了聚焦环绘制)和元素聚焦的方式(通过鼠标单击还是键盘跳转)。

Firefox 将这个选择器实现为:-moz-focusring 伪类。

例如，在某些情况下，下面的样式:

```
/* Standard Level 4 selector */
:focus-visible {
  background-color: lightgray;
}

/* Level 4 selector for Firefox*/
:-moz-focusring  {
  background-color: lightgray;
} 
```

仅当元素通过键盘跳转获得焦点时才应用。
[https://codepen.io/eh3rrera/embed/KJdbMp?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/KJdbMp?height=600&default-tab=result&embed-version=2)

### 输入伪类

这个类别包括应用于接受用户输入的元素的选择器。

#### :读写；只读

[浏览器支持](https://caniuse.com/#feat=css-read-only-write)

:读写选择其值可变的元素(比如没有 readonly 属性的`<input>`元素)。

:只读选择值不可变的元素(如具有 readonly 属性的`<input>`元素)。

然而，这些选择器不仅仅选择`<input>`或`<textarea>`元素，它们还选择用户可以编辑的任何元素，比如 contenteditable 属性设置为 true 的`<p>`元素。

例如:

```
:read-write {
  background-color: lightyellow;
}

:read-only {
  background-color: lightgray;
}

/* For Firefox */
:-moz-read-write {
  background-color: lightyellow;
}

:-moz-read-only {
  background-color: lightgray;
} 
```

将页面中所有不可变元素的背景色设置为浅黄色和浅灰色。
[https://codepen.io/eh3rrera/embed/JxGXWw?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/JxGXWw?height=600&default-tab=result&embed-version=2)

#### :占位符-显示

[浏览器支持](https://caniuse.com/#feat=css-placeholder-shown)

它选择当前显示占位符文本的输入元素。

例如:

```
input:placeholder-shown {
  color: red;
} 
```

将为`<input>`元素的占位符文本(仅占位符文本)赋予红色。
[https://codepen.io/eh3rrera/embed/jdWrbp?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/jdWrbp?height=600&default-tab=result&embed-version=2)

#### :默认

[浏览器支持](https://caniuse.com/#feat=css-default-pseudo)

它选择一组相关元素中作为默认选项的元素。通常应用于按钮和选择列表/菜单。

例如:

```
input:default {
  box-shadow: 0 0 2px 2px green;
}

input:default + label {
  color: green;
} 
```

会给默认的`<input>`元素一个绿色的阴影和颜色。
[https://codepen.io/eh3rrera/embed/wNMWjx?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/wNMWjx?height=600&default-tab=result&embed-version=2)

#### :不确定

[浏览器支持](https://caniuse.com/#feat=css-indeterminate-pseudo)

它选择值处于不确定状态的元素。例如，不包含选中元素的单选按钮和复选框组，或者完成百分比未知的进度条。

例如:

```
input[type="radio"]:indeterminate + label {
  color: red;
} 
```

如果组中没有选中的元素，将为组中的单选元素的标签提供红色。
[https://codepen.io/eh3rrera/embed/yZeaBL?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/yZeaBL?height=600&default-tab=result&embed-version=2)

#### :有效和:无效

[浏览器支持](https://caniuse.com/#feat=form-validation)

它们根据有效性语义分别选择其内容或值有效或无效的元素。如果语义没有定义(或者不能定义)，那么元素既不是:valid 也不是:invalid。

例如，对于电子邮件类型的输入元素:

```
input:invalid {
  color: red;
}

input:valid {
  color: green; 
```

将根据元素包含有效还是无效的电子邮件，为元素的文本赋予不同的颜色。
[https://codepen.io/eh3rrera/embed/xMZEjp?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/xMZEjp?height=600&default-tab=result&embed-version=2)

#### :范围内和范围外

[浏览器支持](https://caniuse.com/#feat=css-in-out-of-range)

这些选择器仅适用于有范围限制的元素，例如，如果它可以有定义的最小值或最大值。如果没有这样的约束，元素既不是:在范围内也不是:超出范围。

例如，对于最小值为 1、最大值为 5 的输入元素:

```
input:out-of-range {
  color: red;
}

input:in-range {
  color: green;
} 
```

对于任何大于 5 的值，将为元素提供红色，对于 1 到 5 之间的值，将为元素提供绿色。
[https://codepen.io/eh3rrera/embed/RvroLN?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/RvroLN?height=600&default-tab=result&embed-version=2)
在某些情况下，这些选择器会起到与:valid 和:invalid 相同的作用。

#### :必输项，可输项

[浏览器支持:必需](https://caniuse.com/#feat=form-validation)

[浏览器支持:选项 a](https://caniuse.com/#feat=css-optional-pseudo) l

在提交表单之前，这些选择器分别应用于必需或可选的表单元素。不是表单元素的元素既不是必需的，也不是可选的。

例如:

```
input:optional {
  color: gray;
}

input:required {
  color: red;
} 
```

将为元素提供红色的必需元素和灰色的可选元素。
[https://codepen.io/eh3rrera/embed/QYyGRQ?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/QYyGRQ?height=600&default-tab=result&embed-version=2)

### 树形结构伪类

这个类别包括选择器，这些选择器允许基于文档树中的信息进行选择，但这些信息不能由其他选择器表示，例如元素相对于其父元素的位置。

#### :第 n 个孩子(n[选择器]？)和:n-last-child(n[选择器]？)

[浏览器支持](https://caniuse.com/#feat=css-nth-child-of)(在撰写本文时，该选择器的 4 级版本还不被任何浏览器支持)。

:n-child 选择器匹配作为其父元素的第 n 个子元素的每个元素。:n-last-child 执行相同的操作，但从最后一个孩子开始计数。您可以使用[this:n tester](https://css-tricks.com/examples/nth-child-tester/)来了解区别，并了解更多关于 [An+B 符号语法](https://drafts.csswg.org/css-syntax-3/#anb-microsyntax)的信息。

在这篇文章的开始，我给你看了一个例子:n . last-child，我说这是一个三级选择器。但是，对于级别 4，该选择器接受一个可选的 of 选择器子句，该子句只过滤与该选择器匹配的子元素。

除了功能更强大之外，有时，选择器声明的方式也会有所不同。例如:

```
li.item:nth-child(-n+2) 
```

选择前两个`<li>`元素，但前提是它们有类项目。

不一样的是:

```
:nth-child(-n+2 of li.item) 
```

这将选择具有类项目的前两个`<li>`元素，即使它们不是列表中的前两个子元素。

尝试一下(在支持这个选择器的浏览器中，比如 Safari):
[https://codepen.io/eh3rrera/embed/NoxgEE?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/NoxgEE?height=600&default-tab=result&embed-version=2)

### 网格-结构选择器

此类别包括处理表格列的选择器。

#### F | | E

在撰写本文时，还没有任何浏览器支持这个选择器。

列组合选择器(`||`)选择代表表格中一个单元格的 E 类型的元素，该元素属于 f 类型的元素所代表的列。

例如，假设下表:

<colgroup><col span="2"></colgroup> <colgroup><col class="selected"></colgroup>
| IDT5】 | 描述 | 价格 |
| 1T5】 | 电脑 | 999 美元 |
| 2T5】 | 碑 | 499 美元 |

以下风格:

```
col.selected || td {
 background-color: lightyellow;
} 
```

将赋予属于所选列(*价格*)的单元格(`<td>`元素)浅黄色的背景色。
[https://codepen.io/eh3rrera/embed/xMZLQY?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/xMZLQY?height=600&default-tab=result&embed-version=2)

#### :第 n 列(An+B)和第 n 列(An+B)

在撰写本文时，还没有任何浏览器支持这个选择器。

你可以把这些选择器想象成`:nth-child`和`:nth-last-child`的列版本。

`:nth-col(An+B)`选择网格或表格中的一个元素，该元素表示在之前有`An+B-1`列*的列的单元格，对于任意正整数或零值`n`。*

`:nth-last-col(An+B)`选择网格或表格中的一个元素，该元素代表在之后有`An+B-1`列*的列的一个单元格，对于任意正整数或零值`n`。*

例如:

```
col.nth-col(1) {
  background-color: lightyellow;
}

col.nth-last-col(1) {
  background-color: lightgreen;
} 
```

将为表格的第一列提供浅黄色背景色，而最后一列将提供浅绿色背景色。
[https://codepen.io/eh3rrera/embed/RvrLwm?height=600&default-tab=result&embed-version=2](https://codepen.io/eh3rrera/embed/RvrLwm?height=600&default-tab=result&embed-version=2)

### 结论

四级选择器允许你以一种简单的方式声明复杂的选择规则。

我们已经涵盖了 2019 年 1 月的[编辑草案规范](https://drafts.csswg.org/selectors-4/)中定义的大部分选择器，然而，我遗漏了一些选择器，它们有可能很快消失或改变，或者没有太多关于它们的信息(除了不被任何浏览器支持之外):

*   [:目标-在](https://drafts.csswg.org/selectors-4/#target-within-pseudo)内
*   [:作用域](https://drafts.csswg.org/selectors-4/#the-scope-pseudo)(不要和[作用域样式](https://caniuse.com/#feat=style-scoped)混淆)
*   [时间维伪类](https://drafts.csswg.org/selectors-4/#time-pseudos)(点击可以了解更多
*   [:空白](https://drafts.csswg.org/selectors-4/#blank)
*   [:用户无效](https://drafts.csswg.org/selectors-4/#user-pseudos)

但是一定要注意这些和其余的 4 级选择器。

* * *

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *

帖子 [CSS 选择器 4 级](https://blog.logrocket.com/css-selectors-level-4-b5da36bcd54c/)最先出现在[博客](https://blog.logrocket.com)上。
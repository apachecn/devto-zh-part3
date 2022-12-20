# CSS 中 ID 和类选择器的区别

> 原文：<https://dev.to/mateja3m/difference-between-id-and-class-selector-in-css-4g84>

要描述 CSS 选择器之间的区别，首先，我们必须提供关于它们的基本信息。

CSS 中最常用的选择器是“ID”选择器和“class”选择器。

级联样式表或 CSS 选择器，我们用它来定位网页上的 HTML 元素，并对其进行样式化。

通常，当我们设计网页样式时，我们可以选择特定的元素，如`<h1>`、`<p>`和`<ul>`，但是我们的基本标签集并没有涵盖所有可能的页面元素类型或布局选择。

这就是为什么我们需要选择器…

当将 CSS 样式应用于 HTML 文档中的元素时，我们可以使用不同的 CSS 选择器来定位我们的元素。

### ID 选择器

使用“id”选择器，我们可以通过指出用 ID 属性设置的唯一 ID 名称来选择元素。

如果我们使用选择器，重要的是它只能在 HTML 文档中使用一次。如果我们在多个元素上使用选择器，我们的代码将无法通过验证。

ID 选择器由一个散列(#)组成，后跟引用 HTML 元素的唯一 ID 名称。

ID 选择器不能以数字开头，长度必须至少为一个字符。它们也区分大小写，并且必须在 HTML、CSS 和 JavaScript 中完全匹配。

**HTML 文档中的 ID 选择器:**

`<div id=”element”>`

`<p>Content goes here...</p>`

`</div>`

**CSS 文件中的 ID 选择器:**

`#element { /* this is the ID selector */`

`background: blue;`

`}`

### 类选择器

当我们想在一个网页上设计多个对象时，我们应该使用一个类选择器。

很重要的一点是，HTML 文档中的多个元素可以有相同的类值。此外，一个元素可以有多个由空格分隔的类名。

类选择器可以有任何以字母、连字符(-)或下划线(_)开头的名称。我们甚至可以在类名中使用数字。但数字不能是连字符后的第一个字符或第二个字符。

**HTML 文档中的一个类选择器:**

`<div class=”bank”>`

`<p>Content goes here...</p>`

`</div>`

在 CSS 中，类选择器以点(.)，像这样:

`.bank { /* this is the Class selector */`

`text-color: red;`

`}`

## ID 和类选择器的区别

一个网页只能有一个唯一 ID (#)应用于一个特定元素。
看看什么时候使用 ID 的例子:

**HTML**

`<div id="image1"></div>`

`<div id="image2"></div>`

`<div id="image3"></div>`

**CSS**

`#image1 {width: 250px; height: 250px; float: left;}`

`#image2 {width: 250px; height: 250px; float: left;}`

`#image3 {width: 250px; height: 250px; float: left;}`

然而，一个类我们可以使用多个元素:

**HTML**

`<div class="slider"></div>`

`<div class="slider"></div>`

`<div class="slider"></div>`

**CSS**

`.slider {width: 250px; height: 250px; float: right;}`

通过向元素添加类名或 ID，没有浏览器默认值。它们本身都没有任何默认的样式信息。他们需要 CSS 来选择它们并应用于样式。

ID 选择器在浏览器中有一个特殊的能力，是类选择器所没有的。在 URL([http://domain name . com # categories](http://domainname.com#categories))中标记有“hash value (#)”的 ID 选择器，使浏览器尝试在网页中定位 ID 为“categories”的元素，并滚动页面以显示该元素。

我们可以说，在一个元素上同时拥有一个 ID 和一个类选择器是一个非常好的主意，而不是一个结论。

*我们来看一个例子:*

**HTML**

`<ul id=”list” class=”number”></ul>`

使用一个以 ID 和 class 作为选择器的元素，我们可以用相同的 class 来设计所有元素的样式，并用一个特定的 ID 选择器来设计特定元素的样式。
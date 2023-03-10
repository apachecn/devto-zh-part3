# 使用自定义属性⚡️Supercharge 您的 CSS⚡️

> 原文：<https://dev.to/dfockler/using-custom-properties-to-supercharge-your-css-1037>

如果你从事 web 开发已经有一段时间了，你可能听说过 SASS。它可以让你做一些很酷的事情，比如嵌套规则集和使用 mixins。你可能没有意识到的是，普通的旧 CSS 现在有了一个最明显的特征，✨VARIABLES✨！在 CSS 术语中，这些被称为 [*自定义属性*](https://developer.mozilla.org/en-US/docs/Web/CSS/--*) 。

我们知道为什么 SASS 中的变量有用，但是为什么它们在常规 CSS 中有用呢？让我们举几个例子。

#### 主题化

想象一下，你需要在你的每个用户的个人资料页面上显示一种自定义颜色，(ala Twitter)。

如果你使用 SASS，你需要为你的每个用户生成一个定制的 CSS 文件。你会说，哦，我就用 Javascript 吧。如果你有一些需要改变颜色的元素，这是可持续的，但是假设你有很多 CSS！使用自定义属性，您可以很快做到这一点。

```
/* user-theme.css */
:root {
  --custom-color: #FF00FF;
}

/* main-theme.css */
.title {
  color: var(--custom-color);
}

.main-header {
  background-color: var(--custom-color);
}
/* ... lots more css in here */ 
```

自定义属性允许您生成一个微小的 CSS 文件，该文件允许您通过最少的额外工作来显著地改变页面的样式。

#### 互动造型

使用自定义属性可以做的另一件好事是允许 Javascript 轻松修改页面样式。比如，添加不同的颜色主题或更改文本大小。使用自定义属性，这变得微不足道。

```
/* main.css */
:root {
  --body-font-size: 16px;
}

body {
  font-size: var(--body-font-size);
} 
```

```
<!-- Some HTML input -->
<input type="range" onchange="changeFontSize(value)"> 
```

```
// Javascript code
function changeFontSize(value) {
  document.documentElement.style.setProperty("--body-font-size", `${value}px`);
} 
```

#### 有些事情你可能会注意到。

*   根据规范，自定义属性必须有一个以`--`开头的变量名，要引用它们，可以在 CSS 中使用`var()`方法。

*   示例中使用了`:root`元素。您还可以将自定义属性限定在一个规则集的范围内，就像其他 CSS 属性一样，它们将基于级联来限定范围。

*   在`:root`元素上设置自定义属性将允许您在所有 CSS 中使用这些属性。

*   IE 决定不支持自定义属性，但所有其他浏览器，包括 Edge，都支持。

对于使用基本 CSS 和 JS 设计动态主题和样式来说，自定义属性是一个非常酷的特性。快乐造型！💇
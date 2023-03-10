# 通过 JavaScript API 来处理 CSS

> 原文：<https://dev.to/areknawo/messing-with-css-through-its-javascript-api-3ja4>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容。**

除了所有的预处理器、transpiler 或任何你能想到的 web 开发工具，有一个事实仍然是真实的——是 **HTML** 、 **CSS** 和 **JavaScript** 驱动了今天的 web。任何一种工具，语言等等。，所有这一切都依赖于这 3 项技术(如果我们不考虑正在兴起的 WebAssembly)。他们一起工作和互动，为您提供无限的可能性，创造更新，更好，甚至更令人惊叹的东西！

JavaScript 是——如果我们可以这样称呼它的话——交互之王。它本身作为一种脚本语言的能力，结合众多进一步扩展其功能集的 web APIs，确实令人印象深刻。这类 API 的例子包括最广为人知的 WebGL API、Canvas API、DOM API，还有一组不太为人知的 CSS 相关方法，可以(非正式地)称为 **CSS API** 。这就是我们今天要探讨的话题！

虽然通过 JS API 与 DOM 交互的想法由于 JSX 和无数 JS 框架等概念而变得非常流行，但在 CSS 中使用类似的技术似乎并没有受到太多关注。当然， **CSS-in-JS** 解决方案是存在的，但是最流行的是基于 **transpilation** 的，在生产中不需要任何额外的运行时就可以输出 CSS。这对性能肯定有好处，因为 CSS API 的使用可能会导致 [*额外的回流*](https://areknawo.com/dom-performance-case-study/) ，这使得它与 DOM API 的使用一样苛刻。但是，这不是我们要找的。如果我告诉你，你不仅可以操作 DOM 元素的样式和 CSS 类，还可以创建成熟的**样式表**，就像 HTML 一样，只需要使用 JS，会怎么样？

# 基础知识

## 内嵌样式

在我们深入研究这些复杂的东西之前，让我们先提醒自己一些基本知识。比如，你可以通过修改`.style`属性来编辑给定`HTMLElement`的**内嵌样式**。

```
const el = document.createElement("div");

el.style.backgroundColor = "red";
// or
el.style.cssText = "background-color: red";
// or
el.setAttribute("style", "background-color: red"); 
```

Enter fullscreen mode Exit fullscreen mode

直接在`.style`对象上设置你的样式属性需要你使用*驼峰*作为你的属性键，而不是*烤肉串*。如果你有更多的内联样式属性要设置(虽然，在这种情况下，你可以考虑使用 CSS 类)，你可以通过设置`.style.cssText`属性或者`style`属性，以一种更高效的方式来完成。请记住，这将完全重置元素的内联样式，因此，要求您在字符串参数中包含**所有属性**(甚至是未更改的属性)。如果你对这样的微优化不感兴趣(他们真的不应该)，并且你的目标是现代浏览器，你可以考虑使用`.style`和`Object.assign()`，一次设置多个样式属性。

```
// ...
Object.assign(el.style, {
    backgroundColor: "red",
    margin: "25px"
}); 
```

Enter fullscreen mode Exit fullscreen mode

这些*【基础】*比你可能想到的要多一点。`.style`对象实现了`CSSStyleDeclaration`接口。这意味着它带有一些有趣的属性和方法！这包括我们所知的`.cssText`，也包括`.length`(集合属性的数量)，以及像`.item()`、`.getPropertyValue()`和`.setPropertyValue()`这样的方法，允许你在不使用*came case*的情况下操作内联样式，因此——任何大小写转换。您可以在 MDN 上找到完整的 API [。](https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleDeclaration) 

```
// ...
const propertiesCount = el.style.length;
for(let i = 0; i < propertiesCount; i++) {
    const name = el.style.item(i); // e.g. "background-color"
    const value = el.style.getPropertyValue(name); // e.g. "red"
    const priority = el.style.getPropertyPriority(name); // e.g. "important"

    if(priority === "important") {
        el.style.removeProperty();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

只是一个小花絮——`.item()`方法在迭代过程中最有用，它有另一种语法，即通过索引访问**。** 

```
// ...
el.style.item(0) === el.style[0]; // true 
```

Enter fullscreen mode Exit fullscreen mode

## CSS 类

现在，让我们暂时离开内联样式，看看更高级的结构- **CSS 类**。基本的包括 [`.className`](https://developer.mozilla.org/en-US/docs/Web/API/Element/className) ，它在被检索和设置时有一个字符串的形式。

```
// ...
el.className = "class-one class-two";
el.setAttribute("class", "class-one class-two"); 
```

Enter fullscreen mode Exit fullscreen mode

设置类字符串的另一种方式是通过设置`class`属性(同样用于检索)。但是，就像使用`.style.cssText`属性一样，设置`.className`将要求您在字符串中包含给定元素的所有类，包括已更改和未更改的类。当然，一些简单的字符串操作可以完成这项工作，但是肯定还有其他方法...而且有！它以稍微新一点的`.classList`属性的形式提供给我们。我说的*【稍新】*是指它 [**不被 IE 9**](https://caniuse.com/#feat=classlist) 支持，仅被 IE 10 和 IE 11 部分支持。

`.classList`属性实现了 [`DOMTokenList`](https://developer.mozilla.org/en-US/docs/Web/API/DOMTokenList) ，让您可以访问一大堆有用的方法。像`.add()`、`.remove()`、`.toggle()`和`.replace()`允许你改变当前的 CSS 类集合，而其他的，例如`.item()`、`.entries()`或`.forEach()`简化了这个索引集合的**迭代过程**。

```
// ...
const classNames = ["class-one", "class-two", "class-three"];
classNames.forEach(className => {
    if(!el.classList.contains(className)) {
        el.classList.add(className);
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 样式表

现在我们已经完成了修订，让我们开始创建我们的纯 JS 样式表吧！首先，让我们分析一下这背后的所有细节。

从上到下，我们有 [`StyleSheetList`](https://developer.mozilla.org/en-US/docs/Web/API/StyleSheetList) 接口，由`document.styleSheets`属性实现。它有助于表示标准 HTML 代码中的情况——在一个文档中使用多个样式表。无论是来自外部文件、URL 还是在`<style/>`标签中，`document.styleSheets`都将它们收集在一个索引集合中，实现标准的迭代协议。也就是说，你可以通过一个简单的循环访问所有的 [`CSSStyleSheet`](https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleSheet) 。

```
for(styleSheet of document.styleSheets){
    console.log(styleSheet);
} 
```

Enter fullscreen mode Exit fullscreen mode

因为这就是`StyleSheetList`的全部内容，所以让我们来看看`CSSStyleSheet`本身。这是事情开始变得有趣的地方！`CSSStyleSheet`扩展了 [`StyleSheet`](https://developer.mozilla.org/en-US/docs/Web/API/StyleSheet) 接口，并且，通过这种关系只带来了一些*只读*属性，如`.ownerNode`、`.href`、`.title`或`.type`，这些属性大多直接取自声明给定样式表的地方。只要回忆一下**加载外部 CSS** 文件的标准 HTML 代码，你就知道我在说什么了。

```
<head>
<link rel="stylesheet" type="text/css" href="style.css" title="Styles">
</head> 
```

Enter fullscreen mode Exit fullscreen mode

所以，我们最感兴趣的东西都在`CSSStyleSheet`界面里面。现在，我们知道 HTML 文档可以包含多个样式表，现在...所有这些样式表可以包含不同的规则或者更多的样式表(当使用`@import`时)!这就是我们现在的情况。`CSSStyleSheet`给你两种方法- `.insertRule()`和`.deleteRule()`。

```
// ...
const ruleIndex = styleSheet.insertRule("div {background-color: red}");
styleSheet.deleteRule(ruleIndex); 
```

Enter fullscreen mode Exit fullscreen mode

这些方法操作索引和类似 CSS 的字符串。由于 CSS 规则的顺序对于在冲突的情况下决定使用哪个规则很重要，`.insertRule()`允许您为新规则传递一个可选的索引。知道一些误用可能会导致错误，所以...简单点就好。

`CSSStyleSheet`也有自己的两个属性——`.ownerRule`和`.cssRules`。虽然`.ownerRule`和`@import`有关，但是第二个`.cssRules`最让我们感兴趣。简单来说，它是 [`CSSRule`](https://developer.mozilla.org/en-US/docs/Web/API/CSSRule) s 的 [`CSSRuleList`](https://developer.mozilla.org/en-US/docs/Web/API/CSSRuleList) ，可以用前面提到的`.insertRule()`和`.deleteRule()`方法修改。请记住，某些浏览器可能会**阻止**从不同来源(域)访问外部`CSSStyleSheet`的`.cssRules`属性。

那么，`CSSRuleList`呢？同样，它是一个可迭代的`CSSRule`集合，这意味着你可以迭代它，通过它们的索引或`.item()`方法访问它的`CSSRule`。但是你不能直接修改`CSSRuleList`。只能用前面提到的方法，别无他法。

`CSSRuleList`包含实现`CSSRule`接口的对象。这一个带有诸如`.parentStyleSheet`和最重要的`.cssText`之类的属性，包含给定规则的所有 CSS 代码。还有一个更有趣的属性——`.type`。它指示给定`CSSRule`的类型，根据[指定常量](https://developer.mozilla.org/en-US/docs/Web/API/CSSRule#Type_constants)。你应该记住，除了最常用的*“标准”*样式相关的规则之外，CSS 还可以包含`@import`或`@keyframes`(最明显的)规则。`CSSRule`不同类型的都有对应的接口。因为您不会直接创建它们，而是使用类似 CSS 的字符串，所以您实际上不需要了解这些扩展接口提供的属性。

在 [`CSSStyleRule`](https://developer.mozilla.org/en-US/docs/Web/API/CSSStyleRule) 的情况下，这些属性是`.selectorText`和`.style`。第一个以字符串的形式表示用于规则的选择器，第二个是实现`CSSStyleDeclaration`接口的对象，我们之前已经讨论过了。

```
// ...
const ruleIndex = styleSheet.insertRule("div {background-color: red}");
const rule = styleSheet.cssRules.item(ruleIndex);

rule.selectorText; // "div"
rule.style.backgroundColor; // "red" 
```

Enter fullscreen mode Exit fullscreen mode

# 实现

至此，我认为我们已经对 CSS 相关的 JavaScript APIs 有了足够的了解，可以创建我们自己的、微小的、基于运行时的 CSS-in-JS 实现了。想法是我们将创建一个函数，传递一个简单的样式配置对象，将输出一个新创建的 CSS 类的散列名称供以后使用。

因此，我们的工作流程非常简单。我们需要一个可以访问某种样式表的函数，只需使用`.insertRule()`方法和 phrased style config 就可以让一切正常运行。让我们从样式表部分开始。

```
function createClassName(style) {
  // ...
  let styleSheet;
  for (let i = 0; i < document.styleSheets.length; i++) {
    if (document.styleSheets[i].CSSInJS) {
      styleSheet = document.styleSheets[i];
      break;
    }
  }
  if (!styleSheet) {
    const style = document.createElement("style");
    document.head.appendChild(style);
    styleSheet = style.sheet;
    styleSheet.CSSInJS = true;
  }
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您使用的是 **ESM** 或任何其他类型的 JS 模块系统，您可以在函数之外安全地创建样式表实例**，而不用担心其他人访问它。但是，由于我想尽量简化这个例子，我们将只在样式表上设置`.CSSInJS`属性，作为一种**标志**，通知我们这是否是我们想要使用的标志。**

这就是上面代码片段的第一部分。现在，如果我们必须为我们的目的创建一个新的样式表呢？做这件事没有直截了当的方法。我们最好的办法是创建一个新的`<style/>`标签，并将其附加到 HTML 文档的`<head/>`部分。这将自动向`document.styleSheets`列表添加一个新的样式表，并允许我们通过`<style/>`标签的`.sheet`属性来访问它。很聪明，是吧？

```
function createRandomName() {
  const code = Math.random().toString(36).substring(7);
  return `css-${code}`;
}

function phraseStyle(style) {
  const keys = Object.keys(style);
  const keyValue = keys.map(key => {
    const kebabCaseKey = 
        key.replace(/([a-z])([A-Z])/g, "$1-$2").toLowerCase();
    const value = 
        `${style[key]}${typeof style[key] === "number" ? "px" : ""}`;

    return `${kebabCaseKey}:${value};`;
  });

  return `{${keyValue.join("")}}`;
} 
```

Enter fullscreen mode Exit fullscreen mode

事实上，除了上面的小花絮，真的没有更多类似有趣的事情发生了。自然，我们首先需要一种方法来为我们的 CSS 类生成一个新的随机名。然后，我们需要将我们的样式对象恰当地组织成可行的 CSS 字符串的形式(或者至少是它的一部分)。这包括*驼峰*和*烤肉串*之间的转换，以及可选的**像素单元** (px) **转换**的处理。哦，不要忘记每个键-值对末尾的分号**(`;`)!** 

```
function createClassName(style) {
  const className = createRandomName();
  let styleSheet;
  // ...
  styleSheet.insertRule(`.${className}${phraseStyle(style)}`);
  return className;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们去我们的主要功能，并作出必要的调整。我们生成随机名称并将 CSS 规则插入样式表。因为所有的 out 规则都是关于类的，所以它们都要求在各自的开头有一个点作为**合适的选择器**。相信我，超级容易忘记的！

```
const redRect = createClassName({
  width: 100,
  height: 100,
  backgroundColor: "red"
});

el.classList.add(redRect); 
```

Enter fullscreen mode Exit fullscreen mode

一切就绪后，我们终于可以对代码进行最终测试了！一切都应该工作正常！下面是证明这一点的代码笔。

[https://codepen.io/areknawo/embed/zQjyom?height=600&default-tab=result&embed-version=2](https://codepen.io/areknawo/embed/zQjyom?height=600&default-tab=result&embed-version=2)

# 你怎么看？

如你所见，从 JavaScript 层面操纵 CSS 非常有趣。不管你是否知道这是可能的，你必须承认——这真的很棒。我们上面的小例子只是一个**概念验证**。CSS API(或者更确切地说是 API)有更多的潜力。而且就等着揭开面纱了！

那么，**你觉得这个帖子**怎么样？下面我很想看看你们**的观点**、**的评论**，以及**的反应**！另外，如果你喜欢这样的文章，可以考虑在 Twitter 上关注我的****[**，在我的脸书页面**](https://facebook.com/areknawoblog) 关注我的 [**个人博客**](https://areknawo.com) 。再次感谢你阅读这篇文章，我希望你度过美好的一天！****
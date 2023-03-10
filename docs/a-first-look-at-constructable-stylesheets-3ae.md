# 快速浏览可构造的样式表

> 原文：<https://dev.to/overrideveloper/a-first-look-at-constructable-stylesheets-3ae>

“可构造的样式表”。这可能是你第一次听到这个，你一定在想“这是什么通量？”没关系，这也是我第一次听到它时的反应。

## 什么是可构造的样式表？

简单地说，可构造样式表是在使用 **Shadow DOM** 时创建和分发可重用样式的一种方式。

## 什么是影 DOM？

为了理解可构造样式表是如何工作的，我们需要理解什么是影子 DOM，为此我们需要理解什么是 DOM。

代表**文档对象模型**的 **DOM** 是一个 HTML 文档的表示，它在 Javascript 中被用来修改页面的内容，也被浏览器用来决定在页面上呈现什么。

**阴影 DOM** 是“**DOM**中的 DOM。这是一个完全独立于“**DOM**的 DOM 树，有自己的元素和样式。它是为了封装的目的而创建的，Shadow DOM 的大多数应用程序都围绕着创建复杂的组件/元素，以这种方式，这些组件/元素的样式不受“**DOM**”中其他样式规则的影响。
Ionic 4 UI 组件就是一个很好的例子。

为了更好地理解 DOM 和影子 DOM 是如何工作的，这里有一篇 Ire Aderinokun 的文章[什么是影子 DOM](https://bitsofco.de/what-is-the-shadow-dom) 。

## 为什么要构造样式表？

"为什么我们需要一种创建样式表的新方法？"你可能会问。我也问过同样的问题。众所周知，我们总是能够使用 Javascript 动态地创建样式表，比如:

```
const style = document.createElement('style'); 
```

Enter fullscreen mode Exit fullscreen mode

并通过访问 Sheet 属性获得对底层 **CssStyleSheet** 实例的引用。【T2![Accessing the sheet property](img/860c7fc41bd352fad08a1df2e95eca38.png)

这种方法工作得很好，但是它有一些缺点，其中一些是:

*   这可能导致重复的 CSS 代码，从而导致 CSS 膨胀。

##### 什么是 CSS 臃肿？

CSS 臃肿是不必要的重复 CSS 代码，虽然它不会直接影响你的性能，但它会间接影响你的性能，因为冗余的选择器和规则会增加你的包大小，使你的页面加载更重，呈现更慢。

*   会导致 FOUC。

##### FOUC 是什么？

无样式内容的快速显示是这样一种情况，内容网页加载时没有样式，但很快就显示出样式。当浏览器在完全加载所有必需的资源之前呈现页面时，会出现这种情况。拥有重复的 CSS 代码(CSS 膨胀)可能会导致 FOUC，这反过来又会导致一个更大更重的包，渲染速度很慢。

使用可构造的样式表很容易解决上述问题。

## 如何使用可构造的样式表

#### 创建样式表

为了根据可构造样式表规范创建样式表，我们调用 CSSStyleSheet()构造函数。

```
const sheet = new CSSStyleSheet(); 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，结果对象 **sheet** 有两个方法可以用来添加和更新样式表规则，而不会有 FOUC 的风险。这两种方法都有一个参数，这个参数是一串样式规则。

这些方法是:

*   replace():除了 CSS 规则之外，该方法还允许使用外部引用，即 **@import** ，并且它返回一个承诺，一旦加载了任何导入，该承诺就会解决。

```
 sheet.replace('@import url("app.css"); p { color: #a1a1a1 }').then(sheet => {
      console.log('Imports added and styles added');
  }).catch(error => {
      console.error('Error adding styles: ', error)
  }); 
```

Enter fullscreen mode Exit fullscreen mode

*   replaceSync():该方法不允许 **@import** ，只允许 CSS 规则。

```
 // this works
  sheet.replaceSync('p { color: #a1a1a1 }');

  // this throws an exception
  try {
      sheet.replaceSync('@import url("app.css"); p { color: #a1a1a1 }');
  } catch(error) => {
      console.error(error);
  } 
```

Enter fullscreen mode Exit fullscreen mode

#### 使用构造好的样式表

创建样式表之后，我们当然希望使用它。我们通过使用文档和影子 DOM 拥有的 **adoptedStyleSheets** 属性来使用创建的样式表。
这个属性让我们通过将这个 **adoptedStyleSheets** 属性的值设置为一个样式表数组，显式地将我们在构造的样式表中定义的样式应用于 DOM 子树。

```
// applying the earlier created stylesheet to a document
document.adoptedStyleSheets = [sheet];

// creating an element and applying stylesheet to its shadow root
const el = document.createElement('div');
const shadowRoot = el.attachShadow({ mode: open });
shadowRoot.adoptedStyleSheets = [sheet]; 
```

Enter fullscreen mode Exit fullscreen mode

我们还可以创建新的样式表，并将它们添加到 adoptedStyleSheets 属性中。现在，正常情况下，由于属性是一个数组，使用像 **push()** 这样的突变将是可行的方法。然而，在这种情况下，事实并非如此。

这是因为 adoptedStyleSheets 属性数组是冻结的，因此像 **push()** 这样的就地突变将不起作用。

##### 什么时候一个数组被说成是冻结的？

冻结数组是通过 Object.freeze()方法作为对象冻结的数组。Object.freeze()方法“冻结”一个对象，防止向其添加新属性，防止现有属性的值被更改，还防止对象的原型被更改。

##### 什么是原位突变？

术语“**就地**”用于描述一种算法，该算法在不使用任何附加数据结构的情况下转换给予它的输入。而在转换输入时利用附加数据结构的算法被称为**不合适** / **不合适**。

考虑以下两种反转数组顺序的方法:

页（page 的缩写）这只是出于解释的目的。

```
const reverseOutOfPlace = (input) => {
  const output = [];
  input.forEach((element, index) => {
    output[index] = input[input.length - (index + 1)];
  })
  return output;
}

const reverseInPlace = (input) => {
  const len = input.length;
  for(let i = 0; i <= Math.floor((len-2)/2); i++) {
    const temp = input[i];
    input[i] = input[len - 1 - i];
    input[len - 1 - i] = temp;
  }
  return input;
} 
```

Enter fullscreen mode Exit fullscreen mode

它们都颠倒了给定数组的顺序，然而， **reverseOutOfPlace** 方法使用一个额外的数组**输出**来反转输入，而 **reverseInPlace** 方法不使用任何额外的数组，因此前者是**不合适的**，而后者是**合适的**。

像 **pop** 和 **push** 这样的数组【突变】方法是**在位**，因为它们不使用任何额外的数组，而其他像 **concat** 和 **map** 是**不在位**，因为它们在转换输入数组时使用了额外的数组。

由于 adoptedStyleSheets 属性数组被冻结，其属性值无法更改，因此向数组添加新样式表的最佳方式是使用 **concat()** 或 **spread** 运算符

```
const newSheet = new CSSStyleSheet();
newSheet.replaceSync('p { color: #eaeaea }');

// using concat
shadowRoot.adoptedStyleSheets = shadowRoot.adoptedStyleSheets.concat(newSheet);

// using the spread operator
shadowRoot.adoptedStyleSheets = [...shadowRoot.adoptedStyleSheets, newSheet] 
```

Enter fullscreen mode Exit fullscreen mode

## 我能用可构造的样式表做什么

可构造的样式表有广泛的用途，下面是其中的一些:

*   动态创建共享的 CSS 样式，并将其应用于文档或多个阴影根，而不会造成 CSS 膨胀。
*   当共享 CSS 样式表应用于元素时，对它的任何更新都会反映在它所应用到的所有元素上。这可用于实现影子 DOM 内的热替换样式。[![Hot replacement of styles](img/0995da6d8fd01ade01c86ccbdf267481.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XpBM4rXz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x1iluonyee98ewcp9iy4.gif)

*   动态更改特定 DOM 子树的 CSS 自定义属性。

*   创建一个由多个组件使用/应用的中心主题。

*   作为浏览器解析器的直接接口来预加载样式表。

这是我做的一支笔，它显示了这篇文章中的大部分内容。

有关可构造样式表的更多信息，请查看以下帖子:[可构造样式表:无缝可重用样式](https://developers.google.com/web/updates/2019/02/constructable-stylesheets)和[可构造样式表对象](https://wicg.github.io/construct-stylesheets)。

页（page 的缩写）s:在撰写本文时，可构造样式表只在 Chrome 上发布，所以前面提到的笔只能在基于 Chrome 的浏览器上工作。
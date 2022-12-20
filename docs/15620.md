# 用 Unsplash 构建一个猫图像生成器😻

> 原文：<https://dev.to/mandihamza/building-a-cat-image-generator-with-unsplash--4f6j>

封面图片由 [@kivenzhao](https://unsplash.com/@kivenzhao)

你是一个贪玩的学习者吗？你应该害怕！玩耍打开了你的思维，有助于你进入一种心流状态。我是编程新手，所以从好玩的地方学习可以减少挫败感，延长我的练习时间。

我为其他新手写这篇文章，向他们展示如何让一个项目变得有趣。作为一个新手，我喜欢包含进一步阅读的资源的文章，所以我确保从头到尾都包含它们！

在我们深入研究代码之前，请点击下面的“随机播放”按钮来看看“Unsplash 之猫”的运行情况👇

[https://codepen.io/mandihamza/embed/JwGLez?height=600&default-tab=result&embed-version=2](https://codepen.io/mandihamza/embed/JwGLez?height=600&default-tab=result&embed-version=2)

我对这个项目有三个目标:了解 Javascript 函数...循环，并保持它的趣味性，与猫！

计划你的工作。执行你的计划。

猫图像生成器需要四样东西:

**1。你的代码之家** ✔️

Codepen 是一个非常流行的基于浏览器的代码编辑器，对新手非常友好。对于像*Unsplash*的猫这样的项目来说，这是一个完美的地方。此外，它有一个热重装功能，所以每次我改变我的代码，一组新的猫图片出现！这让我即使在挣扎的时候也能保持微笑。

**2 .美丽的猫图片**

Unsplash 拥有令人惊叹的摄影师，他们分享免费的高质量图像。他们的 [Unsplash Source](https://source.unsplash.com/) 工具提供随机图像并提供基本的查询参数。它非常适合小型低流量项目。如果你正在构建一个高流量的应用程序，并且需要健壮的参数，你应该使用他们的[免费 API](https://unsplash.com/developers) 。

**3。一点设计** ✔️

当我小的时候，我喜欢丽莎·弗兰克用的明亮的颜色。我用它作为彩色背景的灵感。我用这个 [CSS 渐变生成工具](https://cssgradient.io/)来帮助选择明亮的颜色。我喜欢让设计变得更简单的工具！这些图片是用 CSS grid 设计的，但是我不会详细讨论这个样式，因为这篇文章的重点是 Javascript。

**4。一些代号** ✔️

HTML 标记需要 3 个带有 src 标签的图像元素和一个按钮元素:

```
<button id="shuffleButton" type="button" class="button" alt="Shuffle Button">Shuffle</button>

<div class="grid">
  <img class="cube" src="" alt="cat picture">
  <img class="cube" src="" alt="cat picture">
  <img class="cube" src="" alt="cat picture">
</div> 
```

Enter fullscreen mode Exit fullscreen mode

我们需要一个从 Unsplash 请求 URL 响应的函数。以下是核心要素:

1.  用一些参数保存来自 Unsplash 源的 URL 的常数。
2.  保存 img 元素的常数。
3.  保存随机数生成器的常数。我们将使用它为每个 URL 请求添加独特的签名。(你马上就会明白我们为什么需要这个。)
4.  A For...of 循环遍历 img 元素并修改它们的 src 值。

首先，让我们声明我们的 URL 常量。这是 Unsplash :
的*猫的完整 URL 常量*

```
const url = "https://source.unsplash.com/collection/139386/200x200/?sig="; 
```

Enter fullscreen mode Exit fullscreen mode

以下是该网址的分类:

1.  来自 Unplash 的基本 URL:[https://source.unsplash.com/](https://source.unsplash.com)
2.  `collection`参数后跟唯一的[集合](https://unsplash.com/collections)号。
    例如，我用了一组由 [@dylka](https://unsplash.com/@dylka) 策划的猫图像。

    这个收藏的网址是:[https://unsplash.com/collections/139386/cats](https://unsplash.com/collections/139386/cats)。

    我们需要我们的网址号码:`139386`

3.  图像尺寸参数:`200x200`。

4.  签名参数:`?sig=`。

接下来，让我们声明保存 img 元素的常量，就像这样:

```
const images = document.querySelectorAll("img"); 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们声明一个常量来保存随机数生成器。我们将使用它在每次发出新请求时，在 sig 参数后面附加一个唯一的数字。

我们需要这样做，因为浏览器试图通过提供来自 Unsplash 的 URL 响应的缓存副本而不是新的响应来帮助 T1。这通常是[有益的行为](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching)，但在这种情况下，它会导致重复的图像。唯一的数字签名迫使浏览器做出新的请求，否则在服务器端会被[忽略。偶尔仍有重复图像，但频率大大降低。](https://github.com/unsplash/unsplash-source-js/issues/9) 

```
const randomNum = () => {
  return Math.floor(Math.random() * 1000);
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们创建一个函数，它遍历 img 元素，并使用我们声明的常量用一个随机的 Unsplash URL 填充每个 img src 属性值。下面是代码的样子:

```
function shufflePics() {
  for (let att of images) {
    att.src = `${url}${randomNum()}`;
    {
      console.log(att.src);
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我用了 For...循环来迭代 img 元素，因为它可以迭代 NodeList 对象，这就是 querySelectorAll()方法为我们的图像常量返回的内容。还有其他方法可以迭代 NodeList 对象，但是作为初学者，我发现...易于阅读的循环。

images 常量保存图像元素，但是要访问它们的 src 属性值，我们需要使用点符号，比如:`att.src`。

现在我们需要将 URL 和 randomNum 常量分配给`att.src`。我们这样包装它们，`${}`——这在 ES6 中叫做表达式。表达式是它们所包含的字符串的占位符(URL &随机数)。然后，表达式用反斜线括起来，组合成一个字符串。

现在，该函数用来自 Unsplash 的惟一 URL 响应填充每个 src 属性值。耶！🥳

最后，我们需要将按钮绑定到 shufflePics 函数，这样每次点击都会获取三张新的猫图片。我们通过用`querySelector()`方法选择按钮并添加一个事件监听器来实现这一点。代码是这样的:

```
document.querySelector("button").addEventListener("click", shufflePics); 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！现在我们可以没完没了地翻阅这个 Unsplash 系列中的猫咪图片了！

下一次你开始一个教程或附带项目时，我邀请你变得有趣！它将打开你的思维，接受新的可能性，如果你和我一样，它将帮助你保持专注。

用 Unsplash 制作*猫非常有趣，我迫不及待地想改进它！我很高兴学习如何使用 Unsplash 的 API，所以我的下一次迭代显示了优秀摄影师的学分。*

感谢您阅读我的第一篇开发帖子！我期待您对我的代码、我们的猫主人以及您如何将游戏性融入工作的反馈。

***—派哈姆扎***

随时给我在 [Twitter 上喊](https://twitter.com/_technicallyme)！

附言:不喜欢猫？试试[不吐不快的狗](https://codepen.io/mandihamza/pen/pqdPWm)🐶

正如我所承诺的，这里有一些可供进一步阅读的资源。

Sheelah Brennan 的这个教程帮助我继续这个项目。强烈推荐去看看！
✨[如何自动从原型的 Unsplash 中提取随机图像](https://sheelahb.com/blog/unsplash-random-image-how-to/)。✨

**理解为...of Loops**
[从 querySelectorAll 中循环遍历 DOM 元素的 5 种方法 JavaScript](https://medium.com/@jsdevray/5-ways-to-loop-over-dom-elements-from-queryselectorall-in-javascript-55bd66ca4128)
[Moz 关于循环和迭代的文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Loops_and_iteration#for...of_statement)

**理解模板文本语法**
[Javascript 模板文本指南](https://flaviocopes.com/javascript-template-literals/)Flavio Copes
[模板字符串](https://wesbos.com/template-strings-html/)Wes Bos
[Moz 关于模板文本的文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)
[切换到 ES6(第 2 部分:字符串插值和模板文本)](https://medium.com/predict/switching-to-es6-part-2-string-interpolation-and-template-literals-2f1b0ee56740)

**理解 const vs . var vs . let**
[Wes Bos 的 JavaScript](https://wesbos.com/javascript-scoping/) 中 let 和 const 的作用域

**misc .**
[JavaScript:新手用箭头函数](https://codeburst.io/javascript-arrow-functions-for-beginners-926947fc0cdc)
[Moz 关于 HTTP 缓存的文档](https://developer.mozilla.org/en-US/docs/Web/HTTP/Caching)
[$(文档)。ready()vs window . onload()vs $(window)。load()](https://www.techiediaries.com/javascript/document-ready-vs-window-onload-vs-window-load/)
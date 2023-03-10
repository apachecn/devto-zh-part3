# 虚拟 DOM 简介

> 原文：<https://dev.to/iggredible/intro-to-virtual-dom-4li1>

[![HTML Image on Screen](img/0c2d99f0b96fc3025cc81e37b60a9be6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gNf-kL1u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v2eqqpibor579mb9jw5a.jpg)

# [T1】简介](#intro)

想象你正在建造你的房子。有一天，你想添加一个新的厨房岛，所以你从头开始重建房子。然后你想重新粉刷房子，所以你再次重建整个房子。然后是时候把你的窗格换成，这样你就可以从头开始重建了...又来了。除非你是修理工，否则这不是更新房子的方法。

[![fix-it-felix](img/532ad21c1fc94ec17062de74aee4f5f5.png)](https://i.giphy.com/media/MtIPR6C5okdt6/giphy.gif)

相反，你应该有一个房子的蓝图。要添加厨房岛，您需要在蓝图上找到受影响的区域，然后只重建该区域。如果你想重新粉刷，从蓝图中计算出墙的周长面积，把墙旁边的东西都搬出去(只是请不要做[这个](https://www.youtube.com/watch?v=EiZoSuNej5U))，然后开始粉刷。如果你想改变你的窗格，从蓝图中找到所有的窗口并替换它们。

DOM 也是如此。把 HTML DOM 想象成一所房子，把虚拟 DOM 想象成房子的蓝图。我们应该使用虚拟 DOM 来帮助我们修改我们的 DOM。
这篇文章的灵感很大程度上来自 Jason Yu 的[从零开始建立一个简单的虚拟 DOM](https://www.youtube.com/watch?v=85gJMUEcnkc)视频(我和他没有关系，但是我发现他的东西非常有用。你应该去看看他！).这是一个缩短和简化的版本。我希望不熟悉虚拟 DOM 的读者能够更好地理解什么是虚拟 DOM。

# 土地的布局

代码可以在[这个 github repo](https://github.com/iggredible/simple-vdom-demo) 中找到。这篇文章分为 6 个步骤:

1.  设置
2.  创建虚拟 DOM
3.  呈现 DOM 节点
4.  装入 HTML 页面
5.  以低效的方式更新 DOM
6.  更新 DOM 的有效方法

我们开始吧！

# 设置

在我们开始之前，确保我们已经准备好[最新节点](https://nodejs.org/en/download/)。创建一个文件夹并将光盘放入其中，启动一个 NPM 项目(`npm init -y`)。在根目录下创建`index.html`和`vdom.js`。对于快速绑定，我们将使用 [`parcel-bundler`](https://github.com/parcel-bundler/parcel) 以便运行`npm install parcel-bundler`。我还喜欢在 package.json 中有“start”:“parcel index . html”。

我的`index.html`长这样:

```
<!DOCTYPE html>
<html lang="en">
  <head> 
    <meta charset="UTF-8"> 
    Basic Virtual Dom Demo
  </head>
  <body> 
    <h1>Virtual Dom Demo</h1> 
    <div id="app"></div> 
    <script src="./vdom.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

只要确保导入`vdom.js`并让类似`<div id="app"></div>`的东西在以后挂载我们的 DOM。

# 创建虚拟 DOM

虚拟 DOM 只不过是一个表示 DOM 节点的 *javascript 对象。如前所述，虚拟 DOM 到 DOM 就像蓝图对于房子一样。房子是有形的，更新起来很贵，而蓝图只是一张纸，更新起来容易得多。*

这就是我们的虚拟 DOM 的样子:

```
const vAppStructure = num => {
  return {
    tagName: "ul",
    text: "",
    attrs: { class: "parent-class", id: `parent-id-${num}` },
    children: [
      {
        tagName: "li",
        attrs: "",
        text: "list 1",
        attrs: { class: "child-class" },
        children: []
      },
      {
        tagName: "li",
        attrs: "",
        text: "list 2",
        attrs: { class: "child-class" },
        children: [{ tagName: "input", attrs: "", text: "", children: [] }]
      }
    ]
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

观察结果:

1.  虚拟 DOM 是一个 JS 对象。
2.  在这个例子中，它是一个函数，因为将来它需要更新。虚拟 DOM 不一定是一个函数，它可以是一个普通的 JS 对象(从技术上讲，你可以只做 const myVDom = {name: "div"}，这将算作一个 VDom！)
3.  该结构表示一个有 2 个孩子的柠檬。
4.  第二个孩子有另一个孩子，一个输入。稍后将在步骤 4 中使用它。

# 渲染 DOM 节点

我们现在有一个虚拟的 DOM 结构。我们应该把它渲染成 DOM 节点。本帖需要的主要 Javascript APIs 有:`document.createElement`、`Element.setAttribute`、`document.createTextNode`和`Element.appendChild`。第一步创建元素，第二步设置属性，第三步处理文本，第四步将任何子元素附加到父元素。你会在整个代码中看到`$`符号——带`$`的变量代表 DOM 节点。

```
const renderer = node => {
  const { tagName, text, attrs, children } = node;
  const $elem = document.createElement(tagName);

  for (const attr in attrs) {
    $elem.setAttribute(attr, attrs[attr]);
  }

  if (text) {
    const $text = document.createTextNode(text);
    $elem.appendChild($text);
  }

  if (children && children.length > 0) {
    for (const child of children) {
      const $child = renderer(child);
      $elem.appendChild($child);
    }
  }

  return $elem;
}; 
```

Enter fullscreen mode Exit fullscreen mode

观察结果:

1.  虚拟 DOM 中的`tagName`是使用`document.createElement`呈现的。
2.  每个`attrs`都被迭代并被设置到新创建的元素上。
3.  如果有一个文本，我们创建它并把它附加到元素中。
4.  如果我们的虚拟 DOM 包含子元素，它将遍历每个子元素，并对每个元素递归地运行 renderer 函数(如果子元素有子元素，它们将经历相同的递归，以此类推，直到找不到子元素)。子元素被追加到原始元素中。

现在我们已经创建了 DOM 节点，附加了属性和文本，呈现和附加了子节点——这些 DOM 节点迫不及待地要附加到我们的 HTML 文件中，所以让我们安装它吧！

# 挂载

把挂载想象成把我们的节点放到 HTML 页面中。我们将使用`document.replaceWith`。

```
const mount = ($nodeToReplace, $nodeTarget) => {
  $nodeTarget.replaceWith($nodeToReplace);
  return $nodeToReplace;
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了所有需要的功能。让我们设置一些选择器并安装它:

```
const app = document.querySelector("#app");
let num = 10;
let currentVApp = vAppStructure(num); 
let $vApp = renderer(currentVApp); 
mount($vApp, app); 
```

Enter fullscreen mode Exit fullscreen mode

你可以运行`parcel index.html`(或者`npm run start`)，看着你的虚拟 DOM 以 HTML 显示！超级酷。您已经使用纯 Javascript 和虚拟 DOM 呈现了自己的 HTML 页面！这是基本的虚拟 DOM，功能强大。接下来，我们将通过定期更新来探索虚拟 DOM 的强大功能。

# 更新(低效方式)

虚拟 DOM 的强大之处在于，无论何时更新 JS 对象，都不需要刷新屏幕。
为了演示更新，我们将使用`setInterval`来增加每秒的数量。

```
let $rootElem = mount($vApp, app);
let newVApp;

setInterval(() => {
  num++;
  newVApp = vAppStructure(num);
  let $newVApp = renderer(newVApp);
  $rootElem = mount($newVApp, $rootElem);

  currentVApp = newVApp;
}, 1000); 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果你打开 devTools，观察`ul`元素的 id-*，它现在增加了 1 个*。太棒了。我们有一个工作的、自我更新的 DOM 节点。漂亮！！

观察结果:

1.  请注意赋值$rootElem = mount($newVApp，$rootElem)。这是必要的，因为我们安装了不同数量的更新的 DOM 节点，并且每秒钟用新节点替换旧节点。挂载会返回更新后的 DOM 节点，所以我们会不断地用新节点替换旧节点。
2.  有一个问题。试着在 input 上输入一些东西，每秒钟都会刷新一次。这是因为整个 DOM 每秒钟都在被替换，包括输入。我们只想更新受影响的组件，而不需要重新呈现整个 DOM。

让我们用正确的方式去做！

# 更新的高效方式

世界上最流行的前端库之一 React 使用虚拟 DOM。React 对待虚拟 DOM 的方式是通过[区别](https://reactjs.org/docs/reconciliation.html)。

1.  React 创建应用程序的虚拟 DOM 并保存一个副本。
2.  当发生变化时(比如有人更新了一个状态)，React 会将 virtualDOM 的前一个副本与 virtual DOM 的最近副本进行比较——它会列出所有的差异。
3.  React 根据发现的差异更新实际的 DOM。

我们将创建一个(非常)简化的 diffing 版本。

```
const diff = (oldVApp, newVApp) => {
  const patchAttrs = diffAttrs(oldVApp.attrs, newVApp.attrs);

  return $node => {
    patchAttrs($node);
    return $node; // important to return $node, because after diffing, we patch($rootElem) and it expects to return some sort of element!
  };
};
export default diff; 
```

Enter fullscreen mode Exit fullscreen mode

观察结果:

1.  它将旧的虚拟 DOM 和新的虚拟 DOM 作为参数。请注意，因为它是简化的，所以它不会试图找出新旧虚拟 DOM 之间的差异，而是简单地将新属性应用到 DOM 元素中。

`diffAttrs`函数看起来像这样；

```
const diffAttrs = (oldAttrs, newAttrs) => {
  const patches = [];

  for (const attr in newAttrs) {
    patches.push($node => {
      $node.setAttribute(attr, newAttrs[attr]);
      return $node;
    });
  }

  for (const attr in oldAttrs) {
    if (!(attr in newAttrs)) {
      patches.push($node => {
        $node.removeAttribute(attr);
        return $node;
      });
    }
  }
  return $node => {
    for (const patch of patches) {
      patch($node);
    }
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

观察结果:

1.  我们只是在区分属性，而不是`text`、`children`、`tagName`。为了简洁起见，我跳过了它们。尽管逻辑是相似的。
2.  当遍历所有属性时，每个新属性都被设置到元素节点中(所以如果新属性有`id="my-id-2"`)，它会将新的 id 设置到元素节点中。
3.  我们检查`oldAttrs`中的每个`attr`。假设如果在`oldAttrs`中发现了一个在`newAttrs`中不存在的属性，那么这个属性一定已经被删除了，所以我们删除了它。
4.  我们返回一个函数，以便稍后执行修补。

我们更新后的 setInterval 将如下所示:

```
setInterval(() => {
 num++;
 newVApp = vAppStructure(num);

 const patch = diff(currentVApp, newVApp);
 $rootElem = patch($rootElem);
currentVApp = newVApp;
}, 1000); 
```

Enter fullscreen mode Exit fullscreen mode

观察结果:

1.  我们在新的 DOM 节点上设置属性，而不是每秒重新安装整个更新的 HTML 元素。这不会重新呈现整个 DOM。现在按预期工作。

# 结论

概括一下，我们了解到的情况如下:

1.  虚拟 DOM 是一个普通的 JS 对象，它描述了 DOM 应该是什么样子，就像一所房子的蓝图(而 DOM 就像一所房子)。
2.  挂载虚拟 DOM 是一个迭代虚拟 DOM 属性并调用`setElement`、`createTextNode`、`setAttribute`、`appendChild`的过程(更复杂的 app 需要更多 API)。

3.  更新我们的应用程序的最好方法不是每次更新都替换整个 DOM 结构(这将迫使其他元素像`input`一样进行不必要的重新渲染)，而是遍历每个元素中的每个属性并设置新属性。这样做不会重新呈现元素。

这远非完美——这是 React/ other 框架所做工作的简化表示。

感谢你阅读这篇文章。感谢你花时间阅读！如果你有任何问题，发现错误，请不吝赐教。让我知道你从中学到了什么新东西！

我发现一些有用的资源:

*   [React 虚拟 DOM](https://www.codecademy.com/articles/react-virtual-dom)
*   [Virtual-DOM 和 diffing 如何在 React 中工作](https://medium.com/@gethylgeorge/how-virtual-dom-and-diffing-works-in-react-6fc805f9f84e)
*   [浏览器如何工作](http://taligarsiel.com/Projects/howbrowserswork1.htm)
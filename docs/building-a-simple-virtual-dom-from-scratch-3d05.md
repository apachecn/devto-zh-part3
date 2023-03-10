# 从头开始构建一个简单的虚拟 DOM

> 原文：<https://dev.to/ycmjason/building-a-simple-virtual-dom-from-scratch-3d05>

[https://www.youtube.com/embed/85gJMUEcnkc](https://www.youtube.com/embed/85gJMUEcnkc)

上周，我在曼彻斯特网络会议#4 上做了一个现场编码的演讲。在演讲期间，我在不到一个小时的时间里从头开始构建了一个虚拟 DOM。这是迄今为止我做过的技术上最复杂的演讲。

我演讲的视频上传到[这里](https://youtu.be/85gJMUEcnkc)。这篇文章基本上是我演讲的打印版本，旨在澄清我在演讲中没有时间提及的额外内容。我建议在看这个之前先看视频。这会让事情变得容易一些。

这是我在演讲中写的代码的 [github repo](https://github.com/ycmjason-talks/2018-11-21-manc-web-meetup-4) 和 [codesandbox](https://codesandbox.io/s/github/ycmjason-talks/2018-11-21-manc-web-meetup-4) 。

## 旁注

*   本文将在所有变量前加上
    *   `$` -指真正的 doms，例如`$div`、`$el`、`$app`
    *   `v` -指虚拟 DOM，如`vDiv`、`vEl`、`vApp`
*   这篇文章将会像一个实际的演讲一样，到处添加渐进式代码。每个部分都有一个显示进度的 codesandbox 链接。
*   这篇文章非常非常长。大概要花你半个多小时才能看完。确保你在阅读前有足够的时间。或者考虑先看视频。
*   如果你发现任何错误，请毫不犹豫地指出来！

## 概述

*   [背景:什么是虚拟 DOM？](#background-what-is-virtual-dom)
*   [设置](#setup)
*   [createElement](#createelement-tagname-options)
*   [渲染](#render-vnode)
    *   [渲染虚拟元素](#rendering-virtual-elements)
    *   [元素节点和文本节点](#elementnode-and-textnode)
    *   [扩展渲染以支持文本节点](#extending-render-to-support-textnode)
*   [挂载](#mount-node-target)
*   [让我们的应用变得更有趣](#lets-make-our-app-more-interesting)
*   [差异](#diff-oldvtree-newvtree)
    *   [差异](#diffattrs-oldattrs-newattrs)
    *   [diffChildren](#diffchildren-oldvchildren-newvchildren)
*   [让我们的应用更复杂](#make-our-app-more-complicated)

## 背景:什么是虚拟 DOM？

虚拟 DOM 通常指的是代表实际 [DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction) 的**普通对象**

> 文档对象模型(DOM)是 HTML 文档的编程接口。

例如，当你这样做时:

```
const $app = document.getElementById('app'); 
```

您将在页面上获得`<div id="app"></div>`的 DOM。这个 DOM 会有一些编程接口供你控制。比如:

```
$app.innerHTML = 'Hello world'; 
```

为了用一个简单的对象来表示`$app`，我们可以这样写:

```
const vApp = {
  tagName: 'div',
  attrs: {
    id: 'app',
  },
}; 
```

### 在谈话中没有提及

对于虚拟 DOM 应该是什么样子，没有严格的规则。你可以叫它`tagLabel`而不是`tagName`，或者`props`而不是`attrs`。只要它表示 DOM，它就是一个“虚拟 DOM”。

虚拟 DOM 没有任何编程接口。这就是为什么与实际的 DOMs 相比，它们是轻量级的。

然而，请记住，由于 DOM 是浏览器的基本元素，大多数浏览器必须对它们进行一些认真的优化。因此，实际的 DOMs 可能没有许多人声称的那么慢。

## 设置

> [https://codesandbox.io/s/7wqm7pv476?expanddevtools=1](https://codesandbox.io/s/7wqm7pv476?expanddevtools=1)

我们首先创建并进入我们的项目目录。

```
$ mkdir /tmp/vdommm
$ cd /tmp/vdommm 
```

然后，我们将启动 git repo，用 [gitignorer](https://www.npmjs.com/package/gitignorer) 创建`.gitignore`文件，并启动 npm。

```
$ git init
$ gitignore init node
$ npm init -y 
```

让我们完成初始提交。

```
$ git add -A
$ git commit -am ':tada: initial commit' 
```

接下来，安装[捆扎机](https://parceljs.org/)真正的零配置捆扎机。它支持开箱即用的各种文件格式。在现场编码讲座中，我总是选择 bundler。

```
$ npm install parcel-bundler 
```

(有趣的事实:你不再需要通过`--save`。)

在安装过程中，让我们在项目中创建一些文件。

**src/index.html**

```
<html>
  <head>
    hello world
  </head>
  <body>
    Hello world
    <script src="./main.js"></script>
  </body>
</html> 
```

**src/main . js**T2】

```
const vApp = {
  tagName: 'div',
  attrs: {
    id: 'app',
  },
};

console.log(vApp); 
```

**package . JSON**T2】

```
{
  ...
  "scripts": {
    "dev": "parcel src/index.html", // add this script
  }
  ...
} 
```

现在，您可以通过执行以下操作来生成开发服务器:

```
$ npm run dev

> vdommm@0.0.1 dev /private/tmp/vdommm

> parcel src/index.html

Server running at http://localhost:1234

Built in 959ms. 
```

转到 [http://localhost:1234](http://localhost:1234) ，您应该会看到页面上的 hello world 和我们在控制台中定义的虚拟 DOM。如果你看到它们，那么你是正确设置！

## createElement (tagName，options)

> [https://codesandbox.io/s/n9641jyo04?expanddevtools=1](https://codesandbox.io/s/n9641jyo04?expanddevtools=1)

大多数虚拟 DOM 实现都会有这个函数叫做`createElement`函数，通常称为`h`。这些函数将简单地返回一个“虚拟元素”。所以让我们来实现它。

**src/vdom/create element . js**

```
export default (tagName, opts) => {
  return {
    tagName,
    attrs: opts.attrs,
    children: opts.children,
  };
}; 
```

对于对象析构，我们可以这样写上面的代码:

**src/vdom/create element . js**

```
export default (tagName, { attrs, children }) => {
  return {
    tagName,
    attrs,
    children,
  };
}; 
```

我们还应该允许创建没有任何选项的元素，所以让我们为 out 选项设置一些默认值。

**src/vdom/create element . js**

```
export default (tagName, { attrs = {}, children = [] } = {}) => {
  return {
    tagName,
    attrs,
    children,
  };
}; 
```

回想一下我们之前创建的虚拟 DOM:

**src/main . js**T2】

```
const vApp = {
  tagName: 'div',
  attrs: {
    id: 'app',
  },
};

console.log(vApp); 
```

现在可以写成:

**src/main . js**T2】

```
import createElement from './vdom/createElement';

const vApp = createElement('div', {
  attrs: {
    id: 'app',
  },
});

console.log(vApp); 
```

回到浏览器，您应该会看到与我们之前定义的相同的虚拟 dom。让我们在来自 giphy 的`div`下面添加一张图片:

**src/main . js**T2】

```
import createElement from './vdom/createElement';

const vApp = createElement('div', {
  attrs: {
    id: 'app',
  },
  children: [
    createElement('img', {
      attrs: {
        src: 'https://media.giphy.com/media/cuPm4p4pClZVC/giphy.gif',
      },
    }),
  ],
});

console.log(vApp); 
```

回到浏览器，您应该会看到更新后的虚拟 DOM。

### 在谈话中没有提及

对象文字(如`{ a: 3 }`)自动继承`Object`。这意味着由对象字面量创建的对象将拥有在 [`Object.prototype`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/prototype) 中定义的方法，如`hasOwnProperty`、`toString`等。

我们可以通过使用`Object.create(null)`让我们的虚拟 DOM 变得更“纯粹”。这将创建一个真正的普通对象，它不是从`Object`继承的，而是从`null`继承的。

**src/vdom/create element . js**

```
export default (tagName, { attrs, children }) => {
  const vElem = Object.create(null);

  Object.assign(vElem, {
    tagName,
    attrs,
    children,
  });

  return vElem;
}; 
```

## [渲染(嵌套)](#render-vnode)

> [https://codesandbox.io/s/pp9wnl5nj0?expanddevtools=1](https://codesandbox.io/s/pp9wnl5nj0?expanddevtools=1)

### 渲染虚拟元素

现在我们有了一个为我们生成虚拟 DOM 的函数。接下来，我们需要一种将虚拟 DOM 转换成真实 DOM 的方法。让我们定义`render (vNode)`，它将接受一个虚拟节点并返回相应的 DOM。

**src/vdom/render.js**

```
const render = (vNode) => {
  // create the element
  //   e.g. <div></div>
  const $el = document.createElement(vNode.tagName);

  // add all attributs as specified in vNode.attrs
  //   e.g. <div id="app"></div>
  for (const [k, v] of Object.entries(vNode.attrs)) {
    $el.setAttribute(k, v);
  }

  // append all children as specified in vNode.children
  //   e.g. <div id="app"><img></div>
  for (const child of vNode.children) {
    $el.appendChild(render(child));
  }

  return $el;
};

export default render; 
```

上面的代码应该是不言自明的。如果有任何要求，我很乐意解释更多。

* * *

### 元素节点和文本节点

在真实的 DOM 中，有 [8 种类型的节点](https://developer.mozilla.org/en-US/docs/Web/API/Node/nodeType)。在本文中，我们将只看两种类型:

1.  `ElementNode`，如`<div>`和`<img>`
2.  `TextNode`、明文

我们的虚拟元素结构`{ tagName, attrs, children }`，只代表 DOM 中的`ElementNode`。所以我们也需要一些`TextNode`的代表。我们将简单地用`String`来代表`TextNode`。

为了演示这一点，让我们向当前的虚拟 DOM 添加一些文本。

**src/main . js**T2】

```
import createElement from './vdom/createElement';

const vApp = createElement('div', {
  attrs: {
    id: 'app',
  },
  children: [
    'Hello world', // represents TextNode
    createElement('img', {
      attrs: {
        src: 'https://media.giphy.com/media/cuPm4p4pClZVC/giphy.gif',
      },
    }),  // represents ElementNode
  ],
}); // represents ElementNode

console.log(vApp); 
```

* * *

### 扩展 render 支持 TextNode

正如我提到的，我们正在考虑两种类型的节点。当前的`render (vNode)`只渲染`ElementNode`。所以让我们扩展一下`render`，让它也支持`TextNode`的渲染。

我们将首先重命名我们现有的函数`renderElem`,就像它所做的那样。我还将添加对象析构来使代码看起来更好。

**src/vdom/render.js**

```
const renderElem = ({ tagName, attrs, children}) => {
  // create the element
  //   e.g. <div></div>
  const $el = document.createElement(tagName);

  // add all attributs as specified in vNode.attrs
  //   e.g. <div id="app"></div>
  for (const [k, v] of Object.entries(attrs)) {
    $el.setAttribute(k, v);
  }

  // append all children as specified in vNode.children
  //   e.g. <div id="app"><img></div>
  for (const child of children) {
    $el.appendChild(render(child));
  }

  return $el;
};

export default render; 
```

让我们重新定义一下`render (vNode)`。我们只需要检查一下`vNode`是否是一个`String`。如果是，那么我们可以使用`document.createTextNode(string)`来渲染`textNode`。否则就叫`renderElem(vNode)`。

**src/vdom/render.js**

```
const renderElem = ({ tagName, attrs, children}) => {
  // create the element
  //   e.g. <div></div>
  const $el = document.createElement(tagName);

  // add all attributs as specified in vNode.attrs
  //   e.g. <div id="app"></div>
  for (const [k, v] of Object.entries(attrs)) {
    $el.setAttribute(k, v);
  }

  // append all children as specified in vNode.children
  //   e.g. <div id="app"><img></div>
  for (const child of children) {
    $el.appendChild(render(child));
  }

  return $el;
};

const render = (vNode) => {
  if (typeof vNode === 'string') {
    return document.createTextNode(vNode);
  }

  // we assume everything else to be a virtual element
  return renderElem(vNode);
};

export default render; 
```

现在我们的`render (vNode)`函数能够呈现两种类型的虚拟节点:

1.  虚拟元素——用我们的`createElement`函数创建
2.  虚拟文本——用字符串表示

* * *

### 渲染我们的`vApp`！

现在让我们试着渲染一下我们的`vApp`和`console.log`吧！

**src/main . js**T2】

```
import createElement from './vdom/createElement';
import render from './vdom/render';

const vApp = createElement('div', {
  attrs: {
    id: 'app',
  },
  children: [
    'Hello world',
    createElement('img', {
      attrs: {
        src: 'https://media.giphy.com/media/cuPm4p4pClZVC/giphy.gif',
      },
    }),
  ],
});

const $app = render(vApp);
console.log($app); 
```

转到浏览器，您会看到控制台显示了:
的 DOM

```
<div id="app">
  Hello world
  <img src="https://media.giphy.com/media/cuPm4p4pClZVC/giphy.gif">
</div> 
```

## 挂载($node，$target)

> [https://codesandbox.io/s/vjpk91op47](https://codesandbox.io/s/vjpk91op47)

我们现在能够创建我们的虚拟 DOM 并将其渲染到真实 DOM。接下来，我们需要将真正的 DOM 放到页面上。

让我们首先为我们的应用程序创建一个挂载点。我会把`src/index.html`上的`Hello world`换成`<div id="app"></div>`。

**src/index.html**

```
<html>
  <head>
    hello world
  </head>
  <body>
    <div id="app"></div>
    <script src="./main.js"></script>
  </body>
</html> 
```

我们现在要做的是用我们渲染的`$app`替换这个空的`div`。如果我们忽略 Internet Explorer 和 Safari，这是非常容易做到的。我们可以只使用 [`ChildNode.replaceWith`](https://developer.mozilla.org/en-US/docs/Web/API/ChildNode/replaceWith) 。

我们来定义一下`mount ($node, $target)`。这个函数将简单地用`$node`替换`$target`并返回`$node`。

**src/vdom/mount.js**

```
export default ($node, $target) => {
  $target.replaceWith($node);
  return $node;
}; 
```

现在，在我们的 **main.js** 中，简单地将我们的`$app`挂载到空的 div 中。

**src/main . js**T2】

```
import createElement from './vdom/createElement';
import render from './vdom/render';
import mount from './vdom/mount';

const vApp = createElement('div', {
  attrs: {
    id: 'app',
  },
  children: [
    'Hello world',
    createElement('img', {
      attrs: {
        src: 'https://media.giphy.com/media/cuPm4p4pClZVC/giphy.gif',
      },
    }),
  ],
});

const $app = render(vApp);
mount($app, document.getElementById('app')); 
```

我们的应用程序现在将显示在页面上，我们应该在页面上看到一只猫。

## 让我们让我们的应用程序更有趣

> [https://codesandbox.io/s/ox02294zo5](https://codesandbox.io/s/ox02294zo5)

现在让我们让我们的应用程序更有趣。我们将把我们的`vApp`包装在一个名为`createVApp`的函数中。然后它会接收一个`count`，然后`vApp`会使用它。

**src/main . js**T2】

```
import createElement from './vdom/createElement';
import render from './vdom/render';
import mount from './vdom/mount';

const createVApp = count => createElement('div', {
  attrs: {
    id: 'app',
    dataCount: count, // we use the count here
  },
  children: [
    'The current count is: ',
    String(count), // and here
    createElement('img', {
      attrs: {
        src: 'https://media.giphy.com/media/cuPm4p4pClZVC/giphy.gif',
      },
    }),
  ],
});

let count = 0;
const vApp = createVApp(count);
const $app = render(vApp);
mount($app, document.getElementById('app')); 
```

然后，我们将`setInterval`每秒递增计数，并在页面上再次创建、渲染和安装我们的应用程序。

**src/main . js**T2】

```
import createElement from './vdom/createElement';
import render from './vdom/render';
import mount from './vdom/mount';

const createVApp = count => createElement('div', {
  attrs: {
    id: 'app',
    dataCount: count, // we use the count here
  },
  children: [
    'The current count is: ',
    String(count), // and here
    createElement('img', {
      attrs: {
        src: 'https://media.giphy.com/media/cuPm4p4pClZVC/giphy.gif',
      },
    }),
  ],
});

let count = 0;
const vApp = createVApp(count);
const $app = render(vApp);
let $rootEl = mount($app, document.getElementById('app'));

setInterval(() => {
  count++;
  $rootEl = mount(render(createVApp(count)), $rootEl);
}, 1000); 
```

注意，我使用了`$rootEl`来跟踪根元素。以便`mount`知道在哪里安装我们的新应用程序。

如果我们现在回到浏览器，我们应该看到计数每秒增加 1，并且工作正常！

我们现在获得了以声明方式创建应用程序的能力。应用程序的呈现是可预测的，并且非常非常容易推理。如果您知道 JQuery 是如何工作的，您会发现这种方法更加简洁。

然而，每秒重新呈现整个应用程序有几个问题:

1.  真实 DOM 比虚拟 DOM 重得多。将整个应用程序呈现为真实的 DOM 可能会很昂贵。
2.  元素将失去它们的状态。例如，每当应用程序重新装载到页面时，`<input>`就会失去焦点。点击查看现场演示[。](https://codesandbox.io/s/6l1v8lwj5k)

我们将在下一节中解决这些问题。

## [diff(old vtree、newVTree)](#diff-oldvtree-newvtree)

> [https://codesandbox.io/s/0xv007yqnv](https://codesandbox.io/s/0xv007yqnv)

假设我们有一个函数`diff (oldVTree, newVTree)`，它计算两个虚拟树之间的差异；返回一个`patch`函数，该函数接收`oldVTree`的真实 DOM，并对真实 DOM 执行适当的操作，使真实 DOM 看起来像`newVTree`。

如果我们有那个`diff`函数，那么我们可以把我们的区间改写成:

**src/main . js**T2】

```
import createElement from './vdom/createElement';
import render from './vdom/render';
import mount from './vdom/mount';
import diff from './vdom/diff';

const createVApp = count => createElement('div', {
  attrs: {
    id: 'app',
    dataCount: count, // we use the count here
  },
  children: [
    'The current count is: ',
    String(count), // and here
    createElement('img', {
      attrs: {
        src: 'https://media.giphy.com/media/cuPm4p4pClZVC/giphy.gif',
      },
    }),
  ],
});

let count = 0;
let vApp = createVApp(count);
const $app = render(vApp);
let $rootEl = mount($app, document.getElementById('app'));

setInterval(() => {
  count++;
  const vNewApp = createVApp(count)
  const patch = diff(vApp, vNewApp);

  // we might replace the whole $rootEl,
  // so we want the patch will return the new $rootEl
  $rootEl = patch($rootEl);

  vApp = vNewApp;
}, 1000); 
```

所以我们来试着实现这个`diff (oldVTree, newVTree)`。让我们从一些简单的案例开始:

1.  `newVTree`是`undefined`
    *   那么我们可以简单地删除传入`patch`的`$node`！
2.  它们都是 TextNode(字符串)
    *   如果它们是相同的字符串，那么什么也不做。
    *   如果不是，用`render(newVTree)`替换`$node`。
3.  一棵树是 TextNode，另一棵树是 ElementNode
    *   在那种情况下，它们显然不是同一个东西，那么我们将把`$node`替换为`render(newVTree)`。
4.  `oldVTree.tagName !== newVTree.tagName`
    *   我们假设在这种情况下，新旧树是完全不同的。
    *   我们不会试图找出两棵树之间的区别，而是将`$node`替换为`render(newVTree)`。
    *   react 中也存在这种假设。([来源](https://reactjs.org/docs/reconciliation.html#motivation))
    *   >两种不同类型的元素会产生不同的树。

**src/vdom/diff.js**

```
import render from './render';

const diff = (oldVTree, newVTree) => {
  // let's assume oldVTree is not undefined!
  if (newVTree === undefined) {
    return $node => {
      $node.remove();
      // the patch should return the new root node.
      // since there is none in this case,
      // we will just return undefined.
      return undefined;
    }
  }

  if (typeof oldVTree === 'string' ||
    typeof newVTree === 'string') {
    if (oldVTree !== newVTree) {
      // could be 2 cases:
      // 1\. both trees are string and they have different values
      // 2\. one of the trees is text node and
      //    the other one is elem node
      // Either case, we will just render(newVTree)!
      return $node => {
         const $newNode = render(newVTree);
         $node.replaceWith($newNode);
         return $newNode;
       };
    } else {
      // this means that both trees are string
      // and they have the same values
      return $node => $node;
    }
  }

  if (oldVTree.tagName !== newVTree.tagName) {
    // we assume that they are totally different and 
    // will not attempt to find the differences.
    // simply render the newVTree and mount it.
    return $node => {
      const $newNode = render(newVTree);
      $node.replaceWith($newNode);
      return $newNode;
    };
  }

  // (A)
};

export default diff; 
```

如果代码到达`(A)`，则意味着:

1.  `oldVTree`和`newVTree`都是虚拟元素。
2.  他们有相同的`tagName`。
3.  它们可能有不同的`attrs`和`children`。

我们将实现两个函数来分别处理属性和子元素，即`diffAttrs (oldAttrs, newAttrs)`和`diffChildren (oldVChildren, newVChildren)`，它们将分别返回一个补丁。正如我们所知，在这一点上，我们不会取代`$node`，我们可以安全地返回`$node`后，应用两个补丁。

**src/vdom/diff.js**

```
import render from './render';

const diffAttrs = (oldAttrs, newAttrs) => {
  return $node => {
    return $node;
  };
};

const diffChildren = (oldVChildren, newVChildren) => {
  return $node => {
    return $node;
  };
};

const diff = (oldVTree, newVTree) => {
  // let's assume oldVTree is not undefined!
  if (newVTree === undefined) {
    return $node => {
      $node.remove();
      // the patch should return the new root node.
      // since there is none in this case,
      // we will just return undefined.
      return undefined;
    }
  }

  if (typeof oldVTree === 'string' ||
    typeof newVTree === 'string') {
    if (oldVTree !== newVTree) {
      // could be 2 cases:
      // 1\. both trees are string and they have different values
      // 2\. one of the trees is text node and
      //    the other one is elem node
      // Either case, we will just render(newVTree)!
      return $node => {
         const $newNode = render(newVTree);
         $node.replaceWith($newNode);
         return $newNode;
       };
    } else {
      // this means that both trees are string
      // and they have the same values
      return $node => $node;
    }
  }

  if (oldVTree.tagName !== newVTree.tagName) {
    // we assume that they are totally different and 
    // will not attempt to find the differences.
    // simply render the newVTree and mount it.
    return $node => {
      const $newNode = render(newVTree);
      $node.replaceWith($newNode);
      return $newNode;
    };
  }

  const patchAttrs = diffAttrs(oldVTree.attrs, newVTree.attrs);
  const patchChildren = diffChildren(oldVTree.children, newVTree.children);

  return $node => {
    patchAttrs($node);
    patchChildren($node);
    return $node;
  };
};

export default diff; 
```

### diffAttrs (oldAttrs, newAttrs)

我们先来关注一下`diffAttrs`。这其实很简单。我们知道我们将在`newAttrs`中设置所有内容。设置好之后，我们只需要检查一下`oldAttrs`中的所有键，确保它们也存在于`newAttrs`中。如果没有，请删除它们。

```
const diffAttrs = (oldAttrs, newAttrs) => {
  const patches = [];

  // setting newAttrs
  for (const [k, v] of Object.entries(newAttrs)) {
    patches.push($node => {
      $node.setAttribute(k, v);
      return $node;
    });
  }

  // removing attrs
  for (const k in oldAttrs) {
    if (!(k in newAttrs)) {
      patches.push($node => {
        $node.removeAttribute(k);
        return $node;
      });
    }
  }

  return $node => {
    for (const patch of patches) {
      patch($node);
    }
    return $node;
  };
}; 
```

注意我们是如何创建一个包装补丁并通过`patches`循环来应用它们的。

### diffChildren(旧儿童，新儿童)

孩子会稍微复杂一点。我们可以考虑三种情况:

1.  `oldVChildren.length === newVChildren.length`
    *   我们可以做`diff(oldVChildren[i], newVChildren[i])`，其中`i`从`0`到`oldVChildren.length`。
2.  `oldVChildren.length > newVChildren.length`
    *   我们也可以做`diff(oldVChildren[i], newVChildren[i])`，其中`i`从`0`到`oldVChildren.length`。
    *   `newVChildren[j]`将为`undefined`为`j >= newVChildren.length`
    *   不过这也无妨，因为我们的`diff`可以搞定`diff(vNode, undefined)`！
3.  `oldVChildren.length < newVChildren.length`
    *   我们也可以做`diff(oldVChildren[i], newVChildren[i])`，其中`i`从`0`到`oldVChildren.length`。
    *   这个循环将为每个已经存在的子对象创建补丁
    *   我们只需要创建剩余的额外子节点，即`newVChildren.slice(oldVChildren.length)`。

总之，不管怎样，我们循环通过`oldVChildren`，我们将调用`diff(oldVChildren[i], newVChildren[i])`。

然后我们将渲染额外的孩子(如果有的话)，并将他们添加到`$node`中。

```
const diffChildren = (oldVChildren, newVChildren) => {
  const childPatches = [];
  oldVChildren.forEach((oldVChild, i) => {
    childPatches.push(diff(oldVChild, newVChildren[i]));
  });

  const additionalPatches = [];
  for (const additionalVChild of newVChildren.slice(oldVChildren.length)) {
    additionalPatches.push($node => {
      $node.appendChild(render(newVChildren));
      return $node;
    });
  }

  return $parent => {
    // since childPatches are expecting the $child, not $parent,
    // we cannot just loop through them and call patch($parent)
    $parent.childNodes.forEach(($child, i) => {
      childPatches[i]($child);
    });

    for (const patch of additionalPatches) {
      patch($parent);
    }
    return $parent;
  };
}; 
```

我认为如果我们使用`zip`函数会更优雅一点。

```
import render from './render';

const zip = (xs, ys) => {
  const zipped = [];
  for (let i = 0; i < Math.min(xs.length, ys.length); i++) {
    zipped.push([xs[i], ys[i]]);
  }
  return zipped;
};

const diffChildren = (oldVChildren, newVChildren) => {
  const childPatches = [];
  oldVChildren.forEach((oldVChild, i) => {
    childPatches.push(diff(oldVChild, newVChildren[i]));
  });

  const additionalPatches = [];
  for (const additionalVChild of newVChildren.slice(oldVChildren.length)) {
    additionalPatches.push($node => {
      $node.appendChild(render(additionalVChild));
      return $node;
    });
  }

  return $parent => {
    // since childPatches are expecting the $child, not $parent,
    // we cannot just loop through them and call patch($parent)
    for (const [patch, $child] of zip(childPatches, $parent.childNodes)) {
      patch($child);
    }

    for (const patch of additionalPatches) {
      patch($parent);
    }
    return $parent;
  };
}; 
```

### 最终确定的差异

**src/vdom/diff.js**

```
import render from './render';

const zip = (xs, ys) => {
  const zipped = [];
  for (let i = 0; i < Math.min(xs.length, ys.length); i++) {
    zipped.push([xs[i], ys[i]]);
  }
  return zipped;
};

const diffAttrs = (oldAttrs, newAttrs) => {
  const patches = [];

  // setting newAttrs
  for (const [k, v] of Object.entries(newAttrs)) {
    patches.push($node => {
      $node.setAttribute(k, v);
      return $node;
    });
  }

  // removing attrs
  for (const k in oldAttrs) {
    if (!(k in newAttrs)) {
      patches.push($node => {
        $node.removeAttribute(k);
        return $node;
      });
    }
  }

  return $node => {
    for (const patch of patches) {
      patch($node);
    }
    return $node;
  };
};

const diffChildren = (oldVChildren, newVChildren) => {
  const childPatches = [];
  oldVChildren.forEach((oldVChild, i) => {
    childPatches.push(diff(oldVChild, newVChildren[i]));
  });

  const additionalPatches = [];
  for (const additionalVChild of newVChildren.slice(oldVChildren.length)) {
    additionalPatches.push($node => {
      $node.appendChild(render(additionalVChild));
      return $node;
    });
  }

  return $parent => {
    // since childPatches are expecting the $child, not $parent,
    // we cannot just loop through them and call patch($parent)
    for (const [patch, $child] of zip(childPatches, $parent.childNodes)) {
      patch($child);
    }

    for (const patch of additionalPatches) {
      patch($parent);
    }
    return $parent;
  };
};

const diff = (oldVTree, newVTree) => {
  // let's assume oldVTree is not undefined!
  if (newVTree === undefined) {
    return $node => {
      $node.remove();
      // the patch should return the new root node.
      // since there is none in this case,
      // we will just return undefined.
      return undefined;
    }
  }

  if (typeof oldVTree === 'string' ||
    typeof newVTree === 'string') {
    if (oldVTree !== newVTree) {
      // could be 2 cases:
      // 1\. both trees are string and they have different values
      // 2\. one of the trees is text node and
      //    the other one is elem node
      // Either case, we will just render(newVTree)!
      return $node => {
         const $newNode = render(newVTree);
         $node.replaceWith($newNode);
         return $newNode;
       };
    } else {
      // this means that both trees are string
      // and they have the same values
      return $node => $node;
    }
  }

  if (oldVTree.tagName !== newVTree.tagName) {
    // we assume that they are totally different and 
    // will not attempt to find the differences.
    // simply render the newVTree and mount it.
    return $node => {
      const $newNode = render(newVTree);
      $node.replaceWith($newNode);
      return $newNode;
    };
  }

  const patchAttrs = diffAttrs(oldVTree.attrs, newVTree.attrs);
  const patchChildren = diffChildren(oldVTree.children, newVTree.children);

  return $node => {
    patchAttrs($node);
    patchChildren($node);
    return $node;
  };
};

export default diff; 
```

## 让我们的 app 更复杂

> [https://codes andox . io/s/mpmo 2 YY 69](https://codesandbox.io/s/mpmo2yy69)

我们目前的应用程序并没有真正充分利用我们的虚拟 DOM 的力量。为了展示我们的虚拟 DOM 有多强大，让我们把我们的应用程序变得更复杂:

**src/main . js**T2】

```
import createElement from './vdom/createElement';
import render from './vdom/render';
import mount from './vdom/mount';
import diff from './vdom/diff';

const createVApp = count => createElement('div', {
  attrs: {
    id: 'app',
    dataCount: count, // we use the count here
  },
  children: [
    'The current count is: ',
    String(count), // and here
    ...Array.from({ length: count }, () => createElement('img', {
      attrs: {
        src: 'https://media.giphy.com/media/cuPm4p4pClZVC/giphy.gif',
      },
    })),
  ],
});

let vApp = createVApp(0);
const $app = render(vApp);
let $rootEl = mount($app, document.getElementById('app'));

setInterval(() => {
  const n = Math.floor(Math.random() * 10);
  const vNewApp = createVApp(n);
  const patch = diff(vApp, vNewApp);

  // we might replace the whole $rootEl,
  // so we want the patch will return the new $rootEl
  $rootEl = patch($rootEl);

  vApp = vNewApp;
}, 1000); 
```

我们的应用程序现在将生成一个 0 到 9 之间的随机数`n`，并在页面上显示`n`张猫的照片。如果你进入开发工具，你会看到我们如何“智能地”根据`n`插入和移除`<img>`。

## 谢谢

如果你一直读到这里，我想感谢你花时间阅读全文。这是一个非常非常长的阅读！如果你真的看完了整本书，请留下你的评论。爱你！
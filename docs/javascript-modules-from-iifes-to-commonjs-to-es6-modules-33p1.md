# JavaScript 模块:从 IIFEs 到 CommonJS 再到 ES6 模块

> 原文：<https://dev.to/tylermcginnis/javascript-modules-from-iifes-to-commonjs-to-es6-modules-33p1>

我已经教很多人 JavaScript 很长时间了。该语言最常被忽视的方面是模块系统。这是有充分理由的。JavaScript 中的模块有着奇怪而不稳定的历史。在这篇文章中，我们将回顾这段历史，你将学习过去的模块，以便更好地理解 JavaScript 模块今天是如何工作的。

* * *

如果你更喜欢看而不是读，这里有一个视频涵盖了相同的内容。如果你喜欢，请随意订阅。

[https://www.youtube.com/embed/qJWALEoGge4](https://www.youtube.com/embed/qJWALEoGge4)

* * *

在我们学习如何用 JavaScript 创建模块之前，我们首先需要了解它们是什么以及它们为什么存在。现在看看你的周围。你能看到的任何边缘复杂的物品都可能是由单个的碎片组装而成的。

让我们以手表为例。

[![Internals of a watch](img/ce1938519a690415214ab1d361923628.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NnF0RdvG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ui.dev/post-images/watch-internals.jpeg)

一块简单的手表由数百个内部零件组成。每一部分都有特定的目的，并且对于如何与其他部分交互有明确的界限。所有这些零件放在一起，就构成了整个腕表。我不是手表工程师，但我认为这种方法的好处是显而易见的。

#### 复用性

再看一下上面的图表。请注意整块腕表中使用了多少相同的部件。通过以模块化为核心的高度智能设计决策，他们能够在腕表设计的不同方面重复使用相同的组件。这种重复使用零件的能力简化了制造过程，而且，我认为，增加了利润。

#### 可组合性

这个图很好地说明了可组合性。通过为每个单独的组件建立清晰的边界，他们能够将每个部件组合在一起，用微小、集中的部件创建一个功能齐全的手表。

#### 杠杆

想想制造过程。这家公司不是制造手表，而是制造组装成手表的单个部件。他们可以在内部制造这些零件，他们可以将它们外包出去，并利用其他制造工厂，这都没关系。最重要的是，每一件作品最终都会组合在一起，形成一款腕表——这些作品是在哪里制作的并不重要。

#### 隔离

理解整个系统是困难的。因为手表是由小而集中的零件组成的，所以每一个零件都可以单独思考、制造和/或修理。这种隔离允许多人在手表上单独工作，而不会互相瓶颈。此外，如果其中一个零件坏了，不需要更换整个手表，你只需要更换坏了的单个零件。

#### 组织

组织是每个独立部分的副产品，它与其他部分的交互有一个清晰的边界。有了这种模块化，组织自然会自然而然地产生，无需太多思考。

* * *

当涉及到像手表这样的日常用品时，我们已经看到了模块化的明显好处，但是软件呢？事实证明，这是同样的想法，同样的好处。至于手表是如何设计的，我们*应该*将我们的软件设计成不同的部分，每个部分都有特定的用途，并为它与其他部分的交互方式划定清晰的界限。在软件中，这些部分被称为**模块**。在这一点上，模块听起来可能与函数或 React 组件没有太大区别。那么一个模块到底包含什么呢？

每个模块都有三个部分——依赖项(也称为导入)、代码和导出。

```
imports

code

exports 
```

Enter fullscreen mode Exit fullscreen mode

##### (进口依赖)

当一个模块需要另一个模块时，它可以将那个模块作为依赖关系。例如，每当你想创建一个 React 组件时，你需要`import`这个`react`模块。如果你想使用像`lodash`这样的库，你需要`lodash`模块`import`。

##### 代码

在您建立了模块需要的依赖项之后，下一部分就是模块的实际代码。

##### 出口

导出是模块的“接口”。无论您从模块中导出什么，导入该模块的任何人都可以使用。

* * *

高层次的东西说够了，让我们深入一些真实的例子。

首先，我们来看看 React 路由器。方便的是，他们有一个[模块](https://github.com/ReactTraining/react-router/tree/master/packages/react-router/modules)文件夹。这个文件夹里装满了...当然是模块。那么在 React 路由器中，什么构成了“模块”。事实证明，在大多数情况下，它们将自己的 React 组件直接映射到模块。这是有意义的，一般来说，这就是在 React 项目中分离组件的方式。这很有效，因为如果你重新阅读上面的表，但是把“模块”换成“组件”，这些隐喻仍然有意义。

让我们看看来自`MemoryRouter`模块的代码。现在不要担心实际的代码，而是关注更多的模块结构。

```
// imports
import React from "react";
import { createMemoryHistory } from "history";
import Router from "./Router";

// code
class MemoryRouter extends React.Component {
  history = createMemoryHistory(this.props);
  render() {
    return (
      <Router
        history={this.history}
        children={this.props.children}
      />;
    )
  }
}

// exports
export default MemoryRouter; 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到在模块的顶部，他们定义了他们的导入，或者他们需要什么其他模块来使`MemoryRouter`模块正常工作。接下来，他们有自己的代码。在这种情况下，他们创建了一个名为`MemoryRouter`的新 React 组件。然后在最底层他们定义他们的出口，`MemoryRouter`。这意味着无论何时有人导入`MemoryRouter`模块，他们都会得到`MemoryRouter`组件。

* * *

现在，我们已经了解了模块是什么，让我们回顾一下手表设计的优势，看看通过遵循类似的模块化架构，这些优势如何应用于软件设计。

#### 复用性

模块最大化了可重用性，因为一个模块可以被导入并在任何其他需要它的模块中使用。除此之外，如果一个模块对另一个程序有益，你可以用它创建一个包。一个包可以包含一个或多个模块，可以上传到 [NPM](http://npmjs.com/) 供任何人下载。`react`、`lodash`和`jquery`都是 NPM 软件包的例子，因为它们可以从 NPM 目录安装。

#### 可组合性

因为模块显式地定义了它们的导入和导出，所以它们可以很容易地被组合。更重要的是，好软件的一个标志是它可以很容易地被删除。模块增加了代码的“删除能力”。

#### 杠杆

NPM 注册中心拥有世界上最大的免费、可重复使用的模块集合(准确地说超过 70 万)。如果你需要一个特定的包裹，NPM 有。

#### 隔离

我们用来描述手表隔离的文字在这里也非常合适。“理解整个系统很困难。因为(你的软件)是由小的、集中的(模块)组成的，每一个(模块)都可以被孤立地思考、构建和/或修复。这种隔离允许多人单独在(应用程序)上工作，而不会互相瓶颈。此外，如果其中一个(模块)损坏，你不必更换整个(应用程序)，只需更换损坏的单个(模块)。”

#### 组织

也许模块化软件最大的好处是组织。模块提供了一个自然的分离点。除此之外，我们很快就会看到，模块可以防止您污染全局名称空间，并允许您避免命名冲突。

* * *

至此，您已经了解了模块的好处和结构。现在是时候真正开始建造它们了。我们的方法会很有条理。原因是，如前所述，JavaScript 中的模块有一段奇怪的历史。尽管在 JavaScript 中有“更新”的方法来创建模块，但是一些旧的风格仍然存在，你会不时地看到它们。如果我们在 2018 年直接跳到模块，我会对你不利。也就是说，我们要把它带回到 2010 年末。AngularJS 刚刚发布，jQuery 风靡一时。公司最终使用 JavaScript 来构建复杂的 web 应用程序，伴随着这种复杂性而来的是通过模块来管理它的需求。

创建模块的第一直觉可能是将代码按文件分开。

```
// users.js
var users = ["Tyler", "Sarah", "Dan"]

function getUsers() {
  return users
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// dom.js

function addUserToDOM(name) {
  const node = document.createElement("li")
  const text = document.createTextNode(name)
  node.appendChild(text)

  document.getElementById("users")
    .appendChild(node)
}

document.getElementById("submit")
  .addEventListener("click", function() {
    var input = document.getElementById("input")
    addUserToDOM(input.value)

    input.value = ""
})

var users = window.getUsers()
for (var i = 0; i < users.length; i++) {
  addUserToDOM(users[i])
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<!-- index.html -->
<!DOCTYPE html>
<html>
  <head>
    Users
  </head>

  <body>
    <h1>Users</h1>
    <ul id="users"></ul>
    <input
      id="input"
      type="text"
      placeholder="New User">
    </input>
    <button id="submit">Submit</button>

    <script src="users.js"></script>
    <script src="dom.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

> **完整代码可以在[这里](https://github.com/tylermcginnis/modules/tree/separate-files)找到**。

好的。我们已经成功地将我们的应用程序分成了自己的文件。这是否意味着我们已经成功地实现了模块？不，绝对不行。实际上，我们所做的只是分离代码所在的位置。在 JavaScript 中创建新作用域的唯一方法是使用函数。我们声明的所有不在函数中的变量都存在于全局对象中。您可以通过在控制台中记录`window`对象来看到这一点。你会注意到我们可以访问，更糟的是，改变`addUsers`、`users`、`getUsers`、`addUserToDOM`。这基本上是我们的整个应用程序。我们没有做任何事情来把我们的代码分成模块，我们所做的只是通过物理位置把它分开。如果您是 JavaScript 新手，这可能会让您感到惊讶，但这可能是您对如何用 JavaScript 实现模块的第一直觉。

那么，如果文件分离不能给我们模块，那么什么能给我们模块呢？记住模块的优点——可重用性、可组合性、杠杆作用、隔离、组织。有没有一个 JavaScript 的固有特性可以用来创建我们自己的“模块”，给我们带来同样的好处？一个常规的旧函数呢？当您想到函数的好处时，它们与模块的好处非常吻合。那么这将如何工作呢？如果我们不把整个应用程序放在全局名称空间中，而是公开一个对象，我们称之为`APP`，会怎么样？然后，我们可以将我们的应用程序需要运行的所有方法放在`APP`下，这将防止我们污染全局名称空间。然后，我们可以将所有其他内容封装在一个函数中，使其与应用程序的其他部分隔离开来。

```
// App.js
var APP = {} 
```

Enter fullscreen mode Exit fullscreen mode

```
// users.js
function usersWrapper () {
  var users = ["Tyler", "Sarah", "Dan"]

  function getUsers() {
    return users
  }

  APP.getUsers = getUsers
}

usersWrapper() 
```

Enter fullscreen mode Exit fullscreen mode

```
// dom.js

function domWrapper() {
  function addUserToDOM(name) {
    const node = document.createElement("li")
    const text = document.createTextNode(name)
    node.appendChild(text)

    document.getElementById("users")
      .appendChild(node)
  }

  document.getElementById("submit")
    .addEventListener("click", function() {
      var input = document.getElementById("input")
      addUserToDOM(input.value)

      input.value = ""
  })

  var users = APP.getUsers()
  for (var i = 0; i < users.length; i++) {
    addUserToDOM(users[i])
  }
}

domWrapper() 
```

Enter fullscreen mode Exit fullscreen mode

```
<!-- index.html -->
<!DOCTYPE html>
<html>
  <head>
    Users
  </head>

  <body>
    <h1>Users</h1>
    <ul id="users"></ul>
    <input
      id="input"
      type="text"
      placeholder="New User">
    </input>
    <button id="submit">Submit</button>

    <script src="app.js"></script>
    <script src="users.js"></script>
    <script src="dom.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

> **完整代码可以在[这里](https://github.com/tylermcginnis/modules/tree/wrappers)找到**。

现在如果你看一下`window`对象，它没有我们应用程序的所有重要部分，它只有`APP`和我们的包装函数、`usersWrapper`和`domWrapper`。更重要的是，我们的重要代码(比如`users`)都不能被修改，因为它们已经不在全局名称空间中了。

让我们看看我们是否能更进一步。有没有办法去掉我们的包装函数？请注意，我们正在定义，然后立即调用它们。我们给它们命名的唯一原因是我们可以立即调用它们。有没有一种方法可以立即调用一个匿名函数，这样我们就不必给它们一个名字了？事实证明确实有，而且它甚至有一个奇特的名字——简称为`Immediately Invoked Function Expression`或`IIFE`。

### 人生

这是它的样子。

```
(function () {
  console.log('Pronounced IF-EE')
})() 
```

Enter fullscreen mode Exit fullscreen mode

请注意，这只是一个匿名函数表达式，我们将它包装在 parens()中。

```
(function () {
  console.log('Pronounced IF-EE')
}) 
```

Enter fullscreen mode Exit fullscreen mode

然后，就像任何其他函数一样，为了调用它，我们在它的末尾添加另一对参数。

```
(function () {
  console.log('Pronounced IF-EE')
})() 
```

Enter fullscreen mode Exit fullscreen mode

现在，让我们利用 IIFEs 的知识，去掉难看的包装函数，进一步清理全局名称空间。

```
// users.js

(function () {
  var users = ["Tyler", "Sarah", "Dan"]

  function getUsers() {
    return users
  }

  APP.getUsers = getUsers
})() 
```

Enter fullscreen mode Exit fullscreen mode

```
// dom.js

(function () {
  function addUserToDOM(name) {
    const node = document.createElement("li")
    const text = document.createTextNode(name)
    node.appendChild(text)

    document.getElementById("users")
      .appendChild(node)
  }

  document.getElementById("submit")
    .addEventListener("click", function() {
      var input = document.getElementById("input")
      addUserToDOM(input.value)

      input.value = ""
  })

  var users = APP.getUsers()
  for (var i = 0; i < users.length; i++) {
    addUserToDOM(users[i])
  }
})() 
```

Enter fullscreen mode Exit fullscreen mode

> **完整代码可以在[这里](https://github.com/tylermcginnis/modules/tree/IIFEs)找到**。

***厨师之吻*** 。现在，如果你看一下`window`对象，你会注意到我们添加到它的唯一的东西是`APP`，我们使用它作为我们的应用程序正确运行所需的所有方法的名称空间。

让我们称这种模式为**生命模块模式**。

生命模块模式有什么好处？首先，我们避免将所有内容都转储到全局名称空间中。这将有助于变量冲突，并使我们的代码更加隐私。它有什么缺点吗？确实如此。我们在全局名称空间上还有 1 个项目，`APP`。如果碰巧另一个库使用了相同的名称空间，我们就有麻烦了。其次，你会注意到在我们的`index.html`文件中`<script>`标签的顺序。如果你没有按照现在的顺序排列脚本，应用程序就会崩溃。

尽管我们的解决方案并不完美，但我们正在取得进展。既然我们已经了解了生命模块模式的优缺点，如果我们要为创建和管理模块制定自己的标准，它会有什么特性呢？

早先我们分离模块的第一直觉是每个文件有一个新的模块。尽管这在 JavaScript 中不能开箱即用，但我认为这是我们模块的一个明显的分离点。每个文件都是它自己的模块。然后，我们需要的另一个特性是让每个文件定义**显式导入**(或依赖项)和**显式导出**，它们将可用于导入该模块的任何其他文件。

```
Our Module Standard

1) File based
2) Explicit imports
3) Explicit exports 
```

Enter fullscreen mode Exit fullscreen mode

既然我们已经知道了我们的模块标准将需要的特性，让我们深入研究 API。我们需要定义的唯一真正的 API 是导入和导出的样子。先说出口。为了简单起见，关于模块的任何信息都可以放在`module`对象上。然后，我们想从模块中导出的任何东西都可以放在`module.exports`上。类似这样的

```
var users = ["Tyler", "Sarah", "Dan"]

function getUsers() {
  return users
}

module.exports.getUsers = getUsers 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们可以这样写它

```
var users = ["Tyler", "Sarah", "Dan"]

function getUsers() {
  return users
}

module.exports = {
  getUsers: getUsers
} 
```

Enter fullscreen mode Exit fullscreen mode

不管我们有多少方法，我们都可以将它们添加到`exports`对象中。

```
// users.js

var users = ["Tyler", "Sarah", "Dan"]

module.exports = {
  getUsers: function () {
    return users
  },
  sortUsers: function () {
    return users.sort()
  },
  firstUser: function () {
    return users[0]
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

既然我们已经弄清楚了从模块导出是什么样子，我们需要弄清楚用于导入模块的 API 是什么样子。为了简单起见，让我们假设我们有一个名为`require`的函数。它将一个字符串路径作为它的第一个参数，并将返回从该路径导出的任何内容。按照我们上面的`users.js`文件，导入该模块将看起来像这样

```
var users = require('./users')

users.getUsers() // ["Tyler", "Sarah", "Dan"]
users.sortUsers() // ["Dan", "Sarah", "Tyler"]
users.firstUser() // ["Tyler"] 
```

Enter fullscreen mode Exit fullscreen mode

相当狡猾。通过我们假设的`module.exports`和`require`语法，我们保留了模块的所有优点，同时消除了生命模块模式的两个缺点。

您可能已经猜到了，这不是一个虚构的标准。它是真实的，叫做 CommonJS。

> CommonJS 组定义了一种模块格式，通过确保每个模块都在自己的名称空间中执行来解决 JavaScript 范围问题。这是通过强制模块显式地导出那些它想要暴露给“宇宙”的变量来实现的，也是通过定义那些正常工作所需的其他模块来实现的。
> 
> -网络包文档

如果你以前用过 Node，CommonJS 应该看起来很熟悉。原因是 Node 使用(大部分)CommonJS 规范来实现模块。因此，通过 Node，您可以使用之前看到的 CommonJS `require`和`module.exports`语法获得现成的模块。然而，与 Node 不同，浏览器不支持 CommonJS。事实上，浏览器不仅不支持 CommonJS，而且开箱即用，CommonJS 并不是浏览器的好解决方案，因为它同步加载模块。在浏览器领域，异步加载器是王者。

所以综上所述，CommonJS 有两个问题。第一，浏览器不懂。第二，它同步加载模块，这在浏览器中是一个糟糕的用户体验。如果我们能解决这两个问题，我们的情况就很好。那么，如果连浏览器都不好，那么花这么多时间谈论 CommonJS 又有什么意义呢？有一个解决方案，叫做模块捆绑器。

### 模块捆扎机

JavaScript 模块捆绑器所做的是检查你的代码库，查看所有的导入和导出，然后智能地将你所有的模块捆绑到一个浏览器可以理解的文件中。然后，不用在你的 index.html 文件中包含所有的脚本，也不用担心它们的顺序，你可以包含捆绑器为你创建的单个`bundle.js`文件。

```
app.js ---> |         |
users.js -> | Bundler | -> bundle.js
dom.js ---> |         | 
```

Enter fullscreen mode Exit fullscreen mode

那么捆绑器实际上是如何工作的呢？这是一个非常大的问题，我自己也不完全理解，但是下面是通过一个流行的模块捆绑器 [Webpack](https://webpack.js.org/) 运行我们的简单代码后的输出。

> **完整的代码可以用 CommonJS 和 Webpack 在[这里找到](https://github.com/tylermcginnis/modules/tree/commonjs)** 。你需要下载代码，运行“npm 安装”，然后运行“webpack”。

```
(function(modules) { // webpackBootstrap
  // The module cache
  var installedModules = {};
  // The require function
  function __webpack_require__(moduleId) {
    // Check if module is in cache
    if(installedModules[moduleId]) {
      return installedModules[moduleId].exports;
    }
    // Create a new module (and put it into the cache)
    var module = installedModules[moduleId] = {
      i: moduleId,
      l: false,
      exports: {}
    };
    // Execute the module function
    modules[moduleId].call(
      module.exports,
      module,
      module.exports,
      __webpack_require__
    );
    // Flag the module as loaded
    module.l = true;
    // Return the exports of the module
    return module.exports;
  }
  // expose the modules object (__webpack_modules__)
  __webpack_require__.m = modules;
  // expose the module cache
  __webpack_require__.c = installedModules;
  // define getter function for harmony exports
  __webpack_require__.d = function(exports, name, getter) {
    if(!__webpack_require__.o(exports, name)) {
      Object.defineProperty(
        exports,
        name,
        { enumerable: true, get: getter }
      );
    }
  };
  // define __esModule on exports
  __webpack_require__.r = function(exports) {
    if(typeof Symbol !== 'undefined' && Symbol.toStringTag) {
      Object.defineProperty(exports, Symbol.toStringTag, { value: 'Module' });
    }
    Object.defineProperty(exports, '__esModule', { value: true });
  };
  // create a fake namespace object
  // mode & 1: value is a module id, require it
  // mode & 2: merge all properties of value into the ns
  // mode & 4: return value when already ns object
  // mode & 8|1: behave like require
  __webpack_require__.t = function(value, mode) {
    if(mode & 1) value = __webpack_require__(value);
    if(mode & 8) return value;
    if((mode & 4) && typeof value === 'object' && value && value.__esModule) return value;
    var ns = Object.create(null);
    __webpack_require__.r(ns);
    Object.defineProperty(ns, 'default', { enumerable: true, value: value });
    if(mode & 2 && typeof value != 'string')
      for(var key in value)
        __webpack_require__.d(ns, key, function(key) {
          return value[key];
        }.bind(null, key));
    return ns;
  };
  // getDefaultExport function for compatibility with non-harmony modules
  __webpack_require__.n = function(module) {
    var getter = module && module.__esModule ?
      function getDefault() { return module['default']; } :
      function getModuleExports() { return module; };
    __webpack_require__.d(getter, 'a', getter);
    return getter;
  };
  // Object.prototype.hasOwnProperty.call
  __webpack_require__.o = function(object, property) {
      return Object.prototype.hasOwnProperty.call(object, property);
  };
  // __webpack_public_path__
  __webpack_require__.p = "";
  // Load entry module and return exports
  return __webpack_require__(__webpack_require__.s = "./dom.js");
})
/************************************************************************/
({

/***/ "./dom.js":
/*!****************!*\
  !*** ./dom.js ***!
  \****************/
/*! no static exports found */
/***/ (function(module, exports, __webpack_require__) {

eval(`
  var getUsers = __webpack_require__(/*! ./users */ \"./users.js\").getUsers\n\n
  function addUserToDOM(name) {\n
    const node = document.createElement(\"li\")\n
    const text = document.createTextNode(name)\n
    node.appendChild(text)\n\n
    document.getElementById(\"users\")\n
      .appendChild(node)\n}\n\n
    document.getElementById(\"submit\")\n
      .addEventListener(\"click\", function() {\n
        var input = document.getElementById(\"input\")\n
        addUserToDOM(input.value)\n\n
        input.value = \"\"\n})\n\n
        var users = getUsers()\n
        for (var i = 0; i < users.length; i++) {\n
          addUserToDOM(users[i])\n
        }\n\n\n//# sourceURL=webpack:///./dom.js?`
);}),

/***/ "./users.js":
/*!******************!*\
  !*** ./users.js ***!
  \******************/
/*! no static exports found */
/***/ (function(module, exports) {

eval(`
  var users = [\"Tyler\", \"Sarah\", \"Dan\"]\n\n
  function getUsers() {\n
    return users\n}\n\nmodule.exports = {\n
      getUsers: getUsers\n
    }\n\n//# sourceURL=webpack:///./users.js?`);})
}); 
```

Enter fullscreen mode Exit fullscreen mode

你会注意到那里有很多神奇之处(如果你想知道到底发生了什么，你可以看看评论)，但有一点很有趣，那就是它们把所有的代码都包装在一个大生命中。因此，他们想出了一种方法，简单地利用我们旧的生命模块模式，就可以获得一个好的模块系统的所有好处，而没有缺点。

* * *

JavaScript 真正的未来证明是它是一种活的语言。围绕 JavaScript 的标准委员会 TC-39 每年召开几次会议，讨论对该语言的潜在改进。至此，应该很清楚模块是编写可伸缩、可维护的 JavaScript 的关键特性。在 2013 年左右(可能更早)，JavaScript 需要一个标准化的内置解决方案来处理模块是显而易见的。这开始了将模块原生实现到 JavaScript 的过程。

知道你现在所知道的，如果你的任务是为 JavaScript 创建一个模块系统，它会是什么样子？普通人基本上说对了。像 CommonJS 一样，每个文件都可以是一个新的模块，用一种清晰的方式定义导入和导出——显然，这就是关键所在。我们在 CommonJS 中遇到的一个问题是它同步加载模块。这对服务器来说很好，但对浏览器来说却不是。我们可以做的一个改变是支持异步加载。我们可以做的另一个改变不是`require`函数调用，因为我们在谈论增加语言本身，我们可以定义新的关键字。让我们用`import`和`export`吧。

TC-39 委员会没有在“假设的、虚构的标准”道路上走得太远，当他们创建“ES 模块”时，提出了这些完全相同的设计决策，现在是用 JavaScript 创建模块的标准化方法。让我们来看看语法。

### ES 模块

如上所述，为了指定应该从一个模块中导出什么，您使用了`export`关键字。

```
// utils.js

// Not exported
function once(fn, context) {
  var result
  return function() {
    if(fn) {
      result = fn.apply(context || this, arguments)
      fn = null
    }
    return result
  }
}

// Exported
export function first (arr) {
  return arr[0]
}

// Exported
export function last (arr) {
  return arr[arr.length - 1]
} 
```

Enter fullscreen mode Exit fullscreen mode

现在为了导入`first`和`last`，您有几个不同的选项。一是进口所有从`utils.js`出口的东西。

```
import * as utils from './utils'

utils.first([1,2,3]) // 1
utils.last([1,2,3]) // 3 
```

Enter fullscreen mode Exit fullscreen mode

但是如果我们不想导入模块正在导出的所有内容呢？在这个例子中，如果我们想要导入`first`而不是`last`会怎么样？这里你可以使用所谓的`named imports`(看起来像[析构](https://tylermcginnis.com/object-array-destructuring/)，但不是)。

```
import { first } from './utils'

first([1,2,3]) // 1 
```

Enter fullscreen mode Exit fullscreen mode

ES 模块的酷之处在于，不仅可以指定多个导出，还可以指定一个`default`导出。

```
// leftpad.js

export default function leftpad (str, len, ch) {
  var pad = '';
  while (true) {
    if (len & 1) pad += ch;
    len >>= 1;
    else break;
  }
  return pad + str;
} 
```

Enter fullscreen mode Exit fullscreen mode

当您使用一个`default`导出时，会改变您导入该模块的方式。您不用使用`*`语法或命名导入，而是使用`import name from './path'`。

```
import leftpad from './leftpad' 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您有一个导出一个`default`导出并且还导出其他常规导出的模块会怎么样？嗯，你会按照你期望的方式去做。

```
// utils.js

function once(fn, context) {
  var result
  return function() {
    if(fn) {
      result = fn.apply(context || this, arguments)
      fn = null
    }
    return result
  }
}

// regular export
export function first (arr) {
  return arr[0]
}

// regular export
export function last (arr) {
  return arr[arr.length - 1]
}

// default export
export default function leftpad (str, len, ch) {
  var pad = '';
  while (true) {
    if (len & 1) pad += ch;
    len >>= 1;
    else break;
  }
  return pad + str;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，导入语法会是什么样子呢？在这种情况下，同样，它应该是你所期望的。

```
import leftpad, { first, last } from './utils' 
```

Enter fullscreen mode Exit fullscreen mode

很漂亮，是吧？`leftpad`是`default`出口，`first`和`last`只是常规出口。

关于 ES 模块有趣的是，因为它们现在是 JavaScript 的原生，所以现代浏览器不使用捆绑器就支持它们。让我们回顾一下本教程开始时的简单用户示例，看看使用 ES 模块会是什么样子。

> **完整代码可以在[这里](https://github.com/tylermcginnis/modules/tree/esModules)找到**。

```
// users.js

var users = ["Tyler", "Sarah", "Dan"]

export default function getUsers() {
  return users
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// dom.js

import getUsers from './users.js'

function addUserToDOM(name) {
  const node = document.createElement("li")
  const text = document.createTextNode(name)
  node.appendChild(text)

  document.getElementById("users")
    .appendChild(node)
}

document.getElementById("submit")
  .addEventListener("click", function() {
    var input = document.getElementById("input")
    addUserToDOM(input.value)

    input.value = ""
})

var users = getUsers()
for (var i = 0; i < users.length; i++) {
  addUserToDOM(users[i])
} 
```

Enter fullscreen mode Exit fullscreen mode

现在最酷的部分来了。对于我们的生活模式，我们仍然需要在每个 JS 文件中包含一个脚本(并且按顺序，不变)。对于 CommonJS，我们需要使用像 Webpack 这样的捆绑器，然后在`bundle.js`文件中包含一个脚本。有了 ES 模块，在[现代浏览器](https://caniuse.com/#feat=es6-module)中，我们需要做的就是包含我们的主文件(在这里是`dom.js`)并在脚本标签中添加一个`type='module'`属性。

```
<!DOCTYPE html>
<html>
  <head>
    Users
  </head>

  <body>
    <h1>Users</h1>
    <ul id="users">
    </ul>
    <input id="input" type="text" placeholder="New User"></input>
    <button id="submit">Submit</button>

    <script type=module src='dom.js'></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 树摇晃

CommonJS 模块和 es 模块之间还有一个我们上面没有提到的区别。

有了 CommonJS，你可以在任何地方`require`一个模块，甚至有条件地。

```
if (pastTheFold === true) {
  require('./parallax')
} 
```

Enter fullscreen mode Exit fullscreen mode

因为 ES 模块是静态的，所以 import 语句必须总是在模块的顶层。你不能有条件地进口它们。

```
if (pastTheFold === true) {
  import './parallax' // "import' and 'export' may only appear at the top level"
} 
```

Enter fullscreen mode Exit fullscreen mode

做出这一设计决策的原因是，通过强制模块成为静态的，加载程序可以静态地分析模块树，找出实际正在使用的代码，并从您的包中删除未使用的代码。那是许多大词。换句话说，因为 ES 模块强迫你在模块的顶部声明你的 import 语句，所以 bundler 可以很快理解你的依赖树。当它理解了你的依赖树，它就能发现哪些代码没有被使用，并把它从包中删除。这叫做[树抖动或者死码消除](https://webpack.js.org/guides/tree-shaking/)。

> 有一个关于动态导入的第三阶段提案，它允许你通过 import()有条件地加载模块。

* * *

我希望深入了解 JavaScript 模块的历史不仅能帮助您更好地理解 es 模块，还能帮助您更好地理解它们的设计决策。

* * *

这最初发表在 [ui.dev](https://ui.dev/javascript-modules-iifes-commonjs-esmodules/) 上，是他们[高级 JavaScript](https://ui.dev/advanced-javascript/) 课程的一部分。
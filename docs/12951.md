# 带着理性、反应和包裹行动起来

> 原文：<https://dev.to/hagnerd/getting-up-and-running-with-reason-react-and-parcel-567j>

#### 更新时间:2019 年 4 月 23 日

最近 React 项目发布了新的绑定。它使用“react-jsx”的新版本，并利用 React Hooks API 代替旧的基于记录的 API。

这意味着如果你有使用钩子的经验，你会感觉非常熟悉 React，除了一些小的不同。

* * *

随着 TypeScript 在前端的兴起，我认为学习和利用类型化的、编译成 javascript 的语言的意愿将会增加。对我来说，一个强有力的竞争者是理性。

你可能听说过 Reason，也想过学习它，但是很难放弃你已经了解并热爱的 JavaScript 工具。好消息是 Bucklescript 和像 Parcel 这样的工具使得使用 Reason 变得轻而易举。

下面将向您展示如何用 Parcel 建立一个 React 项目，但是教您 React，或者 React 与 JavaScript 中的 React 相比如何工作超出了本文的范围。如果你想了解更多这些东西，请在评论中告诉我，我会很乐意写更多。

* * *

## 快速背景

### 原因是什么？

Reason 是 JavaScript 程序员非常熟悉的一种特殊语法，它在幕后使用 OCaml。

### 为什么理智在引擎盖下使用 OCaml？

脸书长期以来一直使用 OCaml 来编写他们的一些内部工具(Flow 是用 OCaml 编写的)。OCaml 是一种快速、成熟的语言，具有强大的类型推断能力、强大的类型安全性，并且可以编译成许多不同的目标，包括 JavaScript 和 Native。

### 什么是 Bucklescript？

Bucklescript 是一个采用 OCaml(或 Reason)的编译器，它将 OCaml(或 Reason)转换成高度优化的、可读性惊人的 JavaScript。

### 今天我们将使用什么？

我们使用的设置将使用 reason 作为我们代码的语言，reason-react 作为前端框架，Bucklescript 用于将我们的 Reason 代码编译成 JavaScript，而 package 用于将所有东西打包。

### 期望

为了最大限度地利用这一点，您将希望对 React 感到满意。

* * *

## 初始化

我们要做的第一件事是为我们的项目创建一个新的文件夹，并进入目录。

```
mkdir reason-parcel
cd reason-parcel 
```

然后，我们将使用 npm 或 yarn 进行初始化，并安装我们所有的依赖项。

要使用新的 React 绑定，您需要确保您使用的是 bs 平台^5.0.0 和 react ^7.0.0.

```
yarn init -y 
yarn add -D parcel-bundler bs-platform bsb-js
yarn add react react-dom reason-react 
```

* * *

## 脚本

现在打开 package.json 文件，我们将添加一个启动脚本。将以下内容添加到您的包的底部

```
{  "scripts":  {  "start":  "parcel src/index.html"  }  } 
```

* * *

## 成立 index.html

我们做一个目录叫`src`。我们需要在`src`中创建一个`index.html`文件来匹配我们添加到 package.json 中的启动脚本。

```
mkdir src
touch src/index.html
touch src/Main.re 
```

你可以使用任何你喜欢的 HTML 样板文件。我强烈推荐 emmet，如果您使用 VSCode，它已经可用。或者可以复制以下:

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  
</head>
<body>

</body>
</html> 
```

在 index.html 的`<body>`标签内添加:

```
 <div id="app"></div>
  <script src="./Main.re"></script> 
```

有两件重要的事情需要注意:

1.  div 的 id 是“app”。稍后将在我们的 Main.re 文件中使用它来呈现我们的应用程序。
2.  我们的 script 元素上的 src 属性与我们刚刚创建的文件的名称一致。Parcel 很聪明，看到了这个 src，知道需要处理原因文件，用输出的 JavaScript 文件路径替换 src 属性。

* * *

## 设置 bsconfig.json

在项目的根目录下创建一个名为`bsconfig.json`的文件。

```
touch bsconfig.json 
```

Bucklescript 将从该文件中读取配置。如果你想阅读更多关于配置文件所有可用选项的信息，Bucklescript 有[很棒的文档](https://bucklescript.github.io/docs/en/build-configuration.html)。

将以下配置复制到 bsconfig.json

```
{  "name":  "reason-parcel",  "reason":  {  "react-jsx":  3  },  "version":  "0.1.0",  "sources":  [{  "dir":  "src",  "subdirs":  true  }],  "package-specs":  {  "module":  "commonjs",  "in-source":  true  },  "suffix":  ".bs.js",  "bs-dependencies":  ["reason-react"],  "refmt":  3  } 
```

让我们来看看一些有趣的属性:

*   理由
    *   reason-react:设置为 3 以允许新的 JSX 功能。
*   来源
    *   dir:被设置为我们之前创建的`src`文件夹。
    *   subdirs:为真，因为这告诉 Bucklescript 在我们的`src`文件夹中递归地检查原因文件。
*   `package-specs`
    *   module: commonjs 是默认的👍这对我们来说已经足够了。
    *   in-source:将使我们输出的 JavaScript 文件出现在它们对应的 Reason 文件旁边。
*   bs-dependencies:这是你所依赖的特定原因库的数组。现在我们只使用`reason-react`，但是你可以使用组件库、样式库等等。
*   对于 ReasonV3 语法，refmt:应设置为 3。

* * *

## 让我们最后写点理由

现在，所有这些都解决了，让我们编写一些原因代码，并在浏览器中查看它！

打开 src/Main.re，完全复制以下内容。分号、双引号等等。我们将很快浏览一下到底发生了什么。

```
 module HelloWorld = {
   [@react.component]
    let make = () => <h1>{React.string("Hello, World!")}</h1>
};

ReactDOMRe.renderToElementWithId(<HelloWorld />, "app"); 
```

现在保存文件，运行`yarn start`或`npm run start`，打开 [localhost:1234/](https://dev.tolocalhost:1234/)

好了，有一些事情可能是熟悉的，也有一些事情可能是奇怪的。

### 熟悉的

#### 渲染到具有特定 id 的元素:

```
ReactDOMRe.renderToElementWithId(<HelloWorld />, "app"); 
```

如果你以前写过 React，你应该对它很熟悉。它本质上做的和:
一样

```
ReactDOM.render(<HelloWorld/>, document.getElementById("app")); 
```

#### JSX

在很大程度上，JSX 在理智上是完全一样的。HTML 属性有一些小的注意事项，它们的名称与原因保留字冲突，比如 input 元素上的`type`属性变成了`type_`。

### 怪异

#### 与`module`宣言有什么关系？

```
 module HelloWorld = {
}; 
```

完整的解释比本文的范围更深入一点，但是从道理上讲，文件自动就是一个模块。

为了在 React 中创建组件，你需要在一个新的模块或者一个新的文件中声明每个新的组件。通过在`module HelloWorld ={};`中包装我们的组件，它允许我们使用 JSX 元素`<HelloWorld />`。

#### 这是什么[@react.component]？

这是创建新 React 组件的推荐方法。它在幕后为我们做了一些事情，使我们的生活更容易，但如果你想学习如何在没有它的情况下手动创建自己的组件，请查看【React 文档中关于组件的新页面。

#### 什么是`make`功能？

```
let make = () => <h1>{React.string("Hello from Reason")}</h1> 
```

类似于 React 类中组件 React 如何寻找呈现方法，在 React 模块中，React 需要一个 make 函数。这个 make 函数将所有的`props`作为参数，并简单地返回一些 JSX。

#### 做琴弦真的要这么做吗？

```
React.string("Hello, from Reason") 
```

是的。是的，它是。至少这是你如何让字符串成为有效的 React 元素的方法。请继续关注下一节，了解一些让理性中的字符串反应不那么痛苦的技巧。

* * *

## 我们稍微重构一下

首先让我们取出组件，并把它放在自己的文件中。

```
touch src/HelloWorld.re 
```

然后我们可以复制并粘贴 HelloWorld 模块，但这次是因为它在自己的文件中，所以我们不需要将它包装在`module`中。

HelloWorld.re 内部

```
let s = React.string;

[@react.component]
let make = () => <h1>{s("Hello, from Reason")}</h1>; 
```

我还通过将`React.string`赋给一个短变量`s`，使得调用`React.string`变得更加容易。这种技术在 React 代码中很常见。

现在让我们回到我们的`Main.re`文件，删除除最后一行之外的所有内容。

Main.re

```
ReactDOMRe.renderToElementWithId(<HelloWorld />, "app"); 
```

注意，我们不需要显式地将`HelloWorld`导入到`Main.re`中。这乍一看似乎非常奇怪，但是由于 OCaml 的模块系统的工作方式，所有文件级模块在文件名下都是全局可用的。

所以在`Main.re`中，我们可以访问 HelloWorld 模块。通过在我们的 React 中使用它，React 知道调用模块的`make`函数。

* * *

现在，您已经拥有了使用 Parcel 启动 React 项目所需的所有要素。随意摆弄东西，让我知道你对理性、理性反应或包裹有什么问题。

附注:如果不迅速提及`bsb`作为自举 React 项目的替代，我会觉得不太对:

```
bsb -theme react -init <PROJECT_NAME> 
```

要使用`bsb`命令，您必须全局安装

```
yarn global add bsb 
```

或者使用 npx(如果您使用的是 npm 5.2 或更新版本，您已经安装了它)来运行命令

```
npx bsb -theme react -init <PROJECT_NAME> 
```
# 让我们从头开始创建 ReactJS 第 2 部分

> 原文：<https://dev.to/anamritraj/lets-create-reactjs-from-scratch--part-2-1pf0>

## 重述

在这一系列的文章中，我们从头开始创建主要的 react 功能。到目前为止，我们已经介绍了`render`函数。你可以在这里了解更多:[让我们从头开始创建 ReactJS 第 1 部分](https://dev.to/anamritraj/lets-create-reactjs-from-scratch--part-1-35do)。

## 介绍 JSX

好了，现在我们已经创建了自己的`render`函数，该函数将 *MyReact* 元素作为参数，并将其呈现给 DOM。虽然在一个 div 中呈现一个链接很酷，但是如果必须呈现更多的东西，比如导航，那么将 HTML 标记写成 javascript 对象就变得很烦人。

让我们看一个例子，

```
const myElement = {
  type: 'div',
  props: {
    children: [
      {
        type: 'ul',
        props: {
          children: [
            {
              type: 'li',
              props: {
                children: [
                  {
                    type: 'TEXT_ELEMENT',
                    props: {
                      nodeValue: 'I want to use JSX',
                    },
                  },
                ],
              },
            },
            {
              type: 'li',
              props: {
                children: [
                  {
                    type: 'TEXT_ELEMENT',
                    props: {
                      nodeValue: "Its only second li and I'm annoyed!",
                    },
                  },
                ],
              },
            },
          ],
        },
      },
    ],
  },
} 
```

上面的几行将呈现这一点

```
<div>
  <ul>
    <li>I want to use JSX</li>
    <li>Its only second li and I'm annoyed!</li>
  </ul>
</div> 
```

**介绍 JSX** ！JSX 只是句法糖。使用 JSX transpiler，你可以将 HTML 格式的语法转换成 javascript 可以理解的元素，并由浏览器正确呈现。

作为一名 JSX transpiler，你可以使用[巴别塔](https://github.com/babel/babel/)。它有一个很棒的开源社区和易于使用的 CLI 工具。

如果你正在使用 React，你很可能正在使用它。但是 JSX 实际上并不依赖于 react，它可以在没有 react 的情况下使用，并且理解和使用起来并不困难。

> 如果你想了解更多关于 JSX 的信息，请查阅这篇文章、T2 草案、JSX 规范和 T4 JSX 深度反应文档。

所以在上面的例子中，你可以写类似下面的东西，

```
/** @jsx createElement */
const myElement = (
  <div>
    <ul>
      <li>I want to use JSX</li>
      <li>Its only second li and I'm annoyed!</li>
    </ul>
  </div>
) 
```

如果你熟悉 javascript，你会猜测上面的代码是否是有效的 javascript: *它不是*。

第一行是 babel 理解的注释，名称`createElement`是 babel 将要为它遇到的每个标签调用的函数的名称。它在[巴别塔构型](https://babeljs.io/docs/en/babel-plugin-transform-react-jsx#pragma)中被称为*杂语*。

Babel 会把 JSX 代码转换成一堆函数调用，就像这样

```
const someJsx = (
  createElement(
  "div",
  { "class": "nav" },
  createElement(
    "ul",
    { "class": "nav-links" },
    createElement(
      "li",
      null,
      "Home"
    ),
    createElement(
      "li",
      null,
      "Profile"
    ),
    createElement(
      "li",
      null,
      "Logout"
    )
  )
);
) 
```

所以我们现在要做的就是实现`createElement`函数。函数的第一个参数是元素的`type`。第二个元素是`props`，后面的元素是子元素。如果您还记得我们从[这里](https://dev.to/anamritraj/lets-create-reactjs-from-scratch--part-1-35do)实现的`render`函数，您会记得 render 函数接受表单中的元素，

```
const myElement = {
  type: 'div',
  props: {
    ..
    children: []
  }
} 
```

所以我们需要在`createElement`函数中做的是将包含`children`的第三个参数移到`props`下，并返回可以被我们的`MyReact.render`函数使用的结果对象。

如果我在这里没有意义，让我们创建`createElement`函数，让代码来说话。它类似于`React.createElement`功能。查看[本页](https://reactjs.org/docs/jsx-in-depth.html)了解更多信息。

## createElement 函数

我会把它放在那里解释代码。

```
function createElement(type, config, ...args) {
  const props = Object.assign({}, config)
  const hasChildren = args.length > 0

  props.children = hasChildren ? [].concat(...args) : []

  return { type, props }
} 
```

transpiler(在我们的例子中是 babel)向函数提供了三个参数。第三个参数将包含当前节点的所有子节点。我们只是将道具分配给`props`对象，将子对象分配给`props.children`对象。然后返回对象，这是我们的`MyReact.render`函数可以处理的事情！

嗯，这很简单。但是我们的渲染函数`MyReact.render`期望文本元素有一个类型`TEXT_ELEMENT`和一个`nodeValue`属性。让我们开始吧！

```
function createElement(type, config, ...args) {
  const props = Object.assign({}, config)
  const hasChildren = args.length > 0

  const allChildren = hasChildren ? [].concat(...args) : []
  // Filter all the children which are null,
  // we don't need to render them
  props.children = allChildren
    .filter(child => child !== null && child !== false)
    .map(child => (child instanceof Object ? child : createTextElement(child)))
  // For text nodes, child above is a string
  return { type, props }
}

// This function handles the creation of text nodes
function createTextElement(value) {
  return {
    type: 'TEXT_ELEMENT',
    props: {
      nodeValue: value,
    },
  }
} 
```

现在我们的`createElement`函数已经完成，可以使用了。在你尝试使用它来创建一个 web 应用程序之前，有几件事情需要注意。因为我们使用的 JSX 不是标准 javascript 的一部分，所以我们需要在使用它之前转换这个代码。我将使用 Babel 把代码转换成浏览器可以理解的东西。

你可以看看这个 [REPL](https://babeljs.io/repl#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=PQKhAIAECsGcA9wFkCeAlApgQwMYBcA6HAJ2zwwFEAbDAWwwDs9wRgAoHAewdmdU1zMAvOADmGPP2z4AFAEoA3B269wAFQoANNQH0KAGQpIKAOTXgRAIg3a9h42ctLlPZrRTU6jYeBltw4AA8ACYAlgBuAHz-AUEArlTRsbGBVKGRAJK0AA409EzgAvjgpAzBGMSBwGlJyUE1WbleBUXMXDnc3lU1Mcmp6Y153oXSzABmoQBGFd3pvSk1mLm4GOCcxKGioQxYVCOCs7V9NQDC3ACOcRXgeAAWqwDu61TBAISH81UJtVVhUWyKNhsKSCAilcrEGTuTz5PAAGnAwU4ODisII4jwMO8ACEUBlgjJLO4ALSkQTErDZbKWOSAthjOIMfChbhiCQg2RycAAbxiDKZeBZDBKjAhMgwQyYCOyWFKeC5vOSwGA4AA4hIbvdwNliJxsrBwFgyjcUNlVmNdbRwBLmsxOJNoBh8PMuK4eSazdLdfrwABfCzWyV4ZxKlUne44ADW4FCYxjzFCBqwNww8GYNthLpUCdgalTmKDAbwptWQjL4GsWl0BiMpjUTiBofAJzJ5EN4AYGAeiM4Voz3izbqRVpEr0TebTWKY8wCAH4eyi0SQyJQgzJi2a5DPwAAuBeo7xEVsYCd4EyccoyHV62AEBgXjAANV2VzpTYAYqEqORiOAxutrXCbx9ETchO2IA1tk1VZr31QdVETEDeEYa4RB2egLEiDssHoAheFlPBYAAdVCO5CW4GkQ1iZVwAAQWCYJAOA0CUIgm5OGgns-yDeYAHkHSdQhIwwFBYCvb1YC3OpwAICZvwqGREJY8CpLqWT1goXBbhkdDSywxVpPAV1VAwICmDUEsA10gg8E4fROAeCoTiwWAMHkPC4kmXgNgYUQZAAJkBQyuIILAGIoMy8CQsCFNM7wLM9bUJIAbV0gBdIK6l9N9YmMnNaLwPANkmOI2zQnC9PAMdYAKoqplK1YADJGuwjDXnLSwcFuL9glKBs-IE_ACGE0TxJvVTklkr8f0UmrCuKhqJtidTiE0rqdIqzCeW3AJh1Siq0oDWDYH2-g0qUaTsqogIaPDJ1o1jaDSENZ6jRQIzupeUoDU4OM7lWLYzMDW14LaT7ginQijokohwdKcAAB8EfAZLzsbaiVXBa47k4VyPp676RRRCCIgld6JggvAXXByHb3_VatPFPYhCwrHIQlBFh1pa7wBoiYdioKh3spM1jX-4HYXYziZTlCWB2SGXDxF0Vwx6mQuYuv10f5ZlWWXLByEh9cSwRV0JlEBECCt2VREk7bkjypKbwDfjHSGlzYE2BgZG5X1Te4c3MoCR3bhc1WvsYAMbdvGhfLucAsIABh5x3diocPesjkRQ9gDP4fnVGiG4HADZkK3QuIW2uT3VGeeO2GCazw1Bbzxhtym-TIS6nqtu7l4qvLBgEj2Zr8f79qRDGXZXKWgICFoSkZD7xiWZ2sfGO2fCmQwX7wFdwTwHnZfdyM49TyN5fZ_AHKAlIPA4mIYVuQ9DAvWd31Nd9bXGV14V9fIc-a5wgvgwAqeYd8H5P23BuDAe4bDVnsHWOE25jp7gMtJe85RnxUCuHuYBOCMDbi_skD-MRiEinvo_e2sQ2bIIdseSGZClBfyAA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=react&prettier=false&targets=&version=6.26.0&envVersion=1.6.2) ，看看巴贝尔是如何将代码转换成一串`createElement`呼叫的。

> 如果你只是想看看这在现实世界的应用中是如何使用的，我做了一个 [**codepen 演示**](https://codepen.io/anamritraj/pen/gZVKBy) 来展示用法。

## 使其在浏览器中工作

上面的 codepen 演示是可行的，因为我们使用 babel 作为预处理器，它包含了一堆插件。如果你想让它在你的本地机器上工作，你必须包含几个包来把你的 JSX 转换成`createElement`函数调用。

我们将从添加一堆 npm 包开始，

```
npm i --save-dev @babel/cli @babel/core @babel/plugin-syntax-jsx @babel/plugin-transform-react-jsx @babel/preset-env 
```

*   `@babel/core`核心是巴别塔包

*   允许我们使用 babel 作为 CLI 工具

*   帮助 babel 理解 JSX 语法，因为 babel 不支持开箱即用

*   这是将 JSX 转换成函数调用的主程序包。默认情况下，它使用 React.createElement 作为 pragma 函数。你可以在这里了解更多信息。

*   `@babel/preset-env`负责允许我们使用最新的 JavaScript，而无需对浏览器所需的语法转换(以及可选的浏览器填充)进行微观管理。这只是一个插件的集合。

现在我们需要做的最后一件事是添加一个`.babelrc`文件，它只是告诉 babel 使用哪些预置和插件(我们在上面安装的)。我们来定义一个。

```
{
  "presets": ["@babel/preset-env"],
  "plugins": ["@babel/plugin-syntax-jsx", "@babel/plugin-transform-react-jsx"]
} 
```

我们差不多完成了，现在我们需要做的就是运行 babel-cli，将包含 JSX 代码的文件编译成浏览器可以理解的。

```
babel ./src/main.js -o ./dist/bundle.js 
```

`-o` flag 告诉 babel 将文件输出为文件夹中指定的名称。

## 代号&演示

关于这一点的完整代码可以在 [Github](https://github.com/anamritraj/my-react) 获得。

你可以看看这个 [REPL](https://babeljs.io/repl#?babili=false&browsers=&build=&builtIns=false&spec=false&loose=false&code_lz=PQKhAIAECsGcA9wFkCeAlApgQwMYBcA6HAJ2zwwFEAbDAWwwDs9wRgAoHAewdmdU1zMAvOADmGPP2z4AFAEoA3B269wAFQoANNQH0KAGQpIKAOTXgRAIg3a9h42ctLlPZrRTU6jYeBltw4AA8ACYAlgBuAHz-AUEArlTRsbGBVKGRAJK0AA409EzgAvjgpAzBGMSBwGlJyUE1WbleBUXMXDnc3lU1Mcmp6Y153oXSzABmoQBGFd3pvSk1mLm4GOCcxKGioQxYVCOCs7V9NQDC3ACOcRXgeAAWqwDu61TBAISH81UJtVVhUWyKNhsKSCAilcrEGTuTz5PAAGnAwU4ODisII4jwMO8ACEUBlgjJLO4ALSkQTErDZbKWOSAthjOIMfChbhiCQg2RycAAbxiDKZeBZDBKjAhMgwQyYCOyWFKeC5vOSwGA4AA4hIbvdwNliJxsrBwFgyjcUNlVmNdbRwBLmsxOJNoBh8PMuK4eSazdLdfrwABfCzWyV4ZxKlUne44ADW4FCYxjzFCBqwNww8GYNthLpUCdgalTmKDAbwptWQjL4GsWl0BiMpjUTiBofAJzJ5EN4AYGAeiM4Voz3izbqRVpEr0TebTWKY8wCAH4eyi0SQyJQgzJi2a5DPwAAuBeo7xEVsYCd4EyccoyHV62AEBgXjAANV2VzpTYAYqEqORiOAxutrXCbx9ETchO2IA1tk1VZr31QdVETEDeEYa4RB2egLEiDssHoAheFlPBYAAdVCO5CW4GkQ1iZVwAAQWCYJAOA0CUIgm5OGgns-yDeYAHkHSdQhIwwFBYCvb1YC3OpwAICZvwqGREJY8CpLqWT1goXBbhkdDSywxVpPAV1VAwICmDUEsA10gg8E4fROAeCoTiwWAMHkPC4kmXgNgYUQZAAJkBQyuIILAGIoMy8CQsCFNM7wLM9bUJIAbV0gBdIK6l9N9YmMnNaLwPANkmOI2zQnC9PAMdYAKoqplK1YADJGuwjDXnLSwcFuL9glKBs-IE_ACGE0TxJvVTklkr8f0UmrCuKhqJtidTiE0rqdIqzCeW3AJh1Siq0oDWDYH2-g0qUaTsqogIaPDJ1o1jaDSENZ6jRQIzupeUoDU4OM7lWLYzMDW14LaT7ginQijokohwdKcAAB8EfAZLzsbaiVXBa47k4VyPp676RRRCCIgld6JggvAXXByHb3_VatPFPYhCwrHIQlBFh1pa7wBoiYdioKh3spM1jX-4HYXYziZTlCWB2SGXDxF0Vwx6mQuYuv10f5ZlWWXLByEh9cSwRV0JlEBECCt2VREk7bkjypKbwDfjHSGlzYE2BgZG5X1Te4c3MoCR3bhc1WvsYAMbdvGhfLucAsIABh5x3diocPesjkRQ9gDP4fnVGiG4HADZkK3QuIW2uT3VGeeO2GCazw1Bbzxhtym-TIS6nqtu7l4qvLBgEj2Zr8f79qRDGXZXKWgICFoSkZD7xiWZ2sfGO2fCmQwX7wFdwTwHnZfdyM49TyN5fZ_AHKAlIPA4mIYVuQ9DAvWd31Nd9bXGV14V9fIc-a5wgvgwAqeYd8H5P23BuDAe4bDVnsHWOE25jp7gMtJe85RnxUCuHuYBOCMDbi_skD-MRiEinvo_e2sQ2bIIdseSGZClBfyAA&debug=false&forceAllTransforms=false&shippedProposals=false&circleciRepo=&evaluate=false&fileSize=false&timeTravel=false&sourceType=module&lineWrap=true&presets=react&prettier=false&targets=&version=6.26.0&envVersion=1.6.2) ，看看巴贝尔是如何将代码转换成一串`MyReact.createElement`呼叫的。

我还做了一个 [**codepen 演示**](https://codepen.io/anamritraj/pen/gZVKBy) 来演示如何在现实应用中使用它。

## 尾注

这是我们创建 ReactJs 克隆系列的第二篇文章！与往常一样，这方面的完整代码可以在 Github 上获得。

如果你看到任何错误，请让我知道！我也是人，不是什么都懂。感谢阅读！:)

我很想知道你希望我接下来实现什么功能。虽然我只是刚刚开始，但我计划让它尽可能接近最初的反应。它可能永远不会生产就绪，但我会学到很多东西。

下次见！
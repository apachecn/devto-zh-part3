# React:使用 DOM API 和钩子来检测外部点击

> 原文：<https://dev.to/margaretkrutikova/reasonreact-use-dom-api-and-hooks-to-detect-click-outside-4f74>

开发一个`reason-react`应用程序绝对是一种享受。这种语言非常适合用 reducers 编写应用程序的逻辑，特别是用最新版本的`ReasonReact`编写带有钩子的组件，它的语法更简单、更简洁。

但是当你需要进行 DOM 操作，在 DOM 元素上使用`refs`,附加一些事件处理程序或者处理事件对象时，就不那么令人愉快了。可用的资源不多，很难得到正确的类型，编译器错误有时也没有多大帮助。

在本文中，我想展示如何毫不费力地完成上述所有工作，同时解决一个非常常见的问题:检测 DOM 元素外部的点击。

最终结果将是一个`useClickOutside`钩子，它接收一个在元素外部检测到点击时运行的函数，并返回一个需要附加到该元素的`ref`。源代码在[我的 github repo](https://github.com/MargaretKrutikova/use-click-outside-re) 中，带有钩子的用法示例，所以如果你只是需要一个可行的解决方案，可以直接查看。

# 用例

有很多原因可以解释为什么您想要检测元素之外的点击。最常见的是当用户点击其区域之外时隐藏一个元素，比如关闭一个模态、一个下拉列表、一个通知等等。这里有一个简单明了的解决方案:

1.  监听文档上的`onmousedown`事件，
2.  在事件处理程序中获取调度事件的元素(事件目标)，
3.  使用 [`Node.contains`](https://developer.mozilla.org/en-US/docs/Web/API/Node/contains) 检查目标元素是否是主元素的后代，
4.  如果函数不在主元素中，就调用它。

# 实现

我使用的是最新的`ReasonReact`版本(> = 0.7.0)，允许使用钩子，如果你还没有在`ReasonReact`中使用过它们，我强烈推荐看看这篇[文章](https://dev.to/iwilsonq/reasonml-with-react-hooks-tutorial-building-a-pomodoro-timer-57h0)。

为了实现，我们将使用 [`bs-webapi`](https://github.com/reasonml-community/bs-webapi-incubator) 和绑定到 DOM API 的`reason`以及几个 react 钩子(`useRef`和`useEffect`)。

因此，让我们拥抱`OCaml`类型的系统，并直接进入实现。

## 添加依赖关系

安装`bs-webapi` :

```
npm install bs-webapi --save 
```

并将其添加到`bsconfig.json` :
中的依赖项

```
"bs-dependencies":  ["reason-react",  "bs-webapi"] 
```

## 在 useEffect 中添加事件监听器

让我们通过在`useEffect` :
中添加一个鼠标按下事件监听器来开始实现`useClickOutside`钩子

```
open Webapi.Dom;

let useClickOutside = (onClickOutside: Dom.mouseEvent => unit) => {
  let handleMouseDown = (_) => ();

  React.useEffect0(() => {
    Document.addMouseDownEventListener(handleMouseDown, document);
    // cleanup - unsubscribe on unmount.
    Some(
      () => Document.removeMouseDownEventListener(handleMouseDown, document),
    );
  });
} 
```

这里的`Document.addMouseDownEventListener`和`document`来自`Webapi.Dom`。

我们开始监听`useEffect`钩子内`document`上的`mousedown`事件。`useEffect0`意味着它没有依赖关系，因此只在组件第一次渲染后运行一次。

为了取消订阅事件，我们可以从效果中返回一个“cleanup”函数。在`ReasonReact`中，`useEffect`中函数的类型签名是`(unit => option(unit => unit))`，所以我们需要将我们的清理函数包装在`Some`中。

## 使用参照

现在我们定义`handleMouseDown`函数，它也需要访问主元素的一个`ref`,让我们确定`outside`区域:

```
let elementRef = React.useRef(Js.Nullable.null);

let handleClickOutside = (elRef, e, fn) => ();

let handleMouseDown = (e: Dom.mouseEvent) => {
  elementRef
  ->React.Ref.current
  ->Js.Nullable.toOption
  ->Belt.Option.map(refValue =>
      handleClickOutside(refValue, e, onClickOutside)
    )
  ->ignore;
}; 
```

这看起来很神秘...我们正在做的是:

*   用`useRef`定义一个`ref`，用`null`初始化它，
*   用`React.Ref.current`访问引用的下划线值，并将其转换为选项。
*   仅当 ref 值为`Some`时使用`Belt.Option.map`运行`handleClickOutside`并返回用`Some`包装的结果，否则为`None`，
*   `ignore`忽略从`Belt.Option.map`返回的结果。

我在这里使用快速管道`->`将一个表达式作为函数的第一个参数。如果你好奇的话，这里的是一篇解释快速管道如何工作的好文章。

在[原因-反应文档](https://reasonml.github.io/reason-react/docs/en/refs)中有更多关于使用参考的信息。

## 检查元素是否在外部

太好了，快好了！现在我们需要实现`handleClickOutside`来决定是否调用我们的自定义函数:

```
let handleClickOutside = (domElement: Dom.element, e: Dom.mouseEvent, fn) => {
  let targetElement = MouseEvent.target(e) |> EventTarget.unsafeAsElement;

  !(domElement |> Element.contains(targetElement)) ? fn(e) : ();
}; 
```

这里`domElement`将确定内部/外部边界。值得一提的是，本例中的鼠标事件不是 react 事件(也称为`Synthetic`事件)，因为我们手动将回调附加到文档。在反应鼠标事件的情况下，你可以使用`ReactEvent.Mouse.t`，但是在我们的例子中，我们使用`Dom.mouseEvent`。

我们将使用`Element.contains`来检查目标元素是否是`domElement`的后代。但是这里有一个问题。这个函数接受两个类型为`Element`的参数，但是目标元素的类型是`EventTarget`，严格来说，它并不总是一个元素，例如可以是`XMLHttpRequest` ( [mdn docs](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget) )类型。

然而，由于我们将事件处理程序附加到了一个 DOM 元素上，我们可以确定它是一个元素，并且可以使用`EventTarget.unsafeAsElement`将其转换成一个元素。

这里是[链接](https://github.com/MargaretKrutikova/use-click-outside-re/blob/master/src/ClickOutside.re)和`useClickOutside`钩子的完整代码。

## 示例用法

下面是钩子在野外的使用方法:

```
open ClickOutside;

[@react.component]
let make = () => {
  let handleClickOutside = _ => {
    Js.log("Click outside detected");
  };
  let divRef = useClickOutside(handleClickOutside);

  <div ref={ReactDOMRe.Ref.domRef(divRef)} />;
}; 
```

我创建了一个简单的下拉组件来展示一个真实的用例场景，即 [github](https://github.com/MargaretKrutikova/use-click-outside-re/blob/master/src/Dropdown.re) 上的源代码。

* * *

我希望这篇文章能够通过提供一些有用的提示和解释，在使用 DOM API 时，能够有助于超出检测外部点击这一特定情况。

你找到对你有帮助的东西了吗？或者在解决您的特殊情况时，您在 DOM 操作和 refs 方面有困难吗？请留言告诉我，我们会解决的:)
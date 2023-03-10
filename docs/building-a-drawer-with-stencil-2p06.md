# 用模板制作抽屉

> 原文：<https://dev.to/jefiozie/building-a-drawer-with-stencil-2p06>

在过去的几周里，我一直在开发一个使用 Stencil 的“设计系统”。今天我想和大家分享一些关于我如何用模板制作抽屉的基本知识🙏🏻。

## 抽屉是什么

抽屉是一个组件，用于在一些内容旁边添加可折叠的侧边内容(通常是导航)。此外，在本文中，您将看到我们的抽屉的最终结果(*这不会是一个完美风格的例子*😆).

## 抽屉的架构

我们的抽屉有三个主要部分:

[![Preview of drawer architecture](img/5b6d4e1f0259efd10395331d3e406981.png)](///static/9737f229489869d7ed52de2683ff9096/93d99/drawer_architecture.png)

*   一个容器
*   抽屉内容的位置
*   内容的位置

该容器将用作包装材料。这将有助于我们内部零件的造型。内容槽是我们将在槽中提供的内容的占位符。通常这将是导航。最后，我们有一个应用程序内容的位置。在这里，您将提供申请的主要内容。

## 项目设置

因为本文的重点是创建一个抽屉容器，所以我将简要介绍如何建立一个新项目。

首先，我们将创建一个新的模板项目，由模板的[提供一些简单的步骤](https://stenciljs.com/docs/getting-started)

创建新项目:

```
npm init stencil 
```

Enter fullscreen mode Exit fullscreen mode

你会提示选择一个项目类型，选择`component`

```
? Pick a starter › - Use arrow-keys. Return to submit.

❯ ionic-pwa Everything you need to build fast, production-ready PWAs
   app Minimal starter for building a Stencil app or website
   component Collection of web components that can be used anywhere 
```

Enter fullscreen mode Exit fullscreen mode

现在您需要提供一个项目名称，我们来填写`example-drawer`。这就是现在我们准备开始工作的组成部分。

## 代码

在您最喜欢的编辑器中打开项目后，打开`my-component.tsx`文件，删除所有默认代码，并替换为以下代码:

```
import { Component, Host, h, State, Method } from '@stencil/core'

@Component({
  tag: 'my-component',
  styleUrl: 'my-component.css',
  shadow: true,
})
export class MyComponent {
  /**
   * Show or hide the drawer
   */
  @State() open = true
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们使用`@State`设置一些内部数据。当改变`State`属性时，会导致组件再次渲染。这样，我们将切换一个 CSS 类。

```
import { Component, Host, h, State, Method } from '@stencil/core'

@Component({
  tag: 'my-component',
  styleUrl: 'my-component.css',
  shadow: true,
})
export class MyComponent {
  @State() open = true

  @Method()
  async toggleDrawer() {
    this.open = !this.open
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们添加了一个`@Method`装饰器和一个切换属性`open`的方法。这样做允许我们从组件外部调用`toggleDrawer`方法。

```
import { Component, Host, h, State, Method } from '@stencil/core'

@Component({
  tag: 'my-component',
  styleUrl: 'my-component.css',
  shadow: true,
})
export class MyComponent {
  @State() open = true

  @Method()
  async toggleDrawer() {
    this.open = !this.open
  }
  render() {
    return (
      <Host>
        <div class="drawer-container">
          <div class={{ drawer: true, open: this.open }}>
            <div class="drawer-content">
              <slot name="drawer-content" />
            </div>
          </div>
          <div class={{ content: true, left: !this.open }}>
            <slot name="content" />
          </div>
        </div>
      </Host>
    )
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在最后一部分，我们定义了 HTML 模板。在本文的前面，我说过我们将基于`State`属性`open`切换一些 CSS 类。现在你可以明白我的意思了。当属性`open`等于`true`时，`open`类将被添加到 div 中，类似的行为将出现在`content div`上，如代码所示。

[![Drawer outside of the view](img/24619ee49cc7fc1072e8f0be0daf3932.png)](///static/30e34bd5d2ae39fd76a9ebd3a1a7fdc5/43efa/outside_of_view.png)

现在打开`my-component.css`文件，将代码替换为:

```
:host {
  display: block;
  width: 100%;
}

.drawer-container {
  position: relative;
  display: flex;
  box-sizing: border-box;
  overflow: hidden;
  border: 1px solid #ccc;
}

.drawer {
  padding-right: 20px;
  background: gray;
  border-right: 1px solid #ccc;
  position: relative;
  width: 280px;
  transform: translate3d(-100%, 0, 0);
  z-index: 2;
  box-sizing: border-box;
  will-change: transform;
}
.drawer-content {
  width: 100%;
  height: 100%;
}
.drawer.open {
  transform: none;
}

.content {
  margin-left: 0px;
  margin-right: 0px;
  flex-grow: 1;
  flex-shrink: 1;
  display: block;
  height: 100%;
  overflow: auto;
  will-change: contents;
}
.content.left {
  margin-left: -280px;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将关注`open`和`left`行为。在我们的`drawer`类中，我们定义了`transform: translate3d(-100%, 0, 0);`。这将把我们的 div 放到视图之外。当我们追加`open`类时，我们将把`transform`设置为`none`，现在我们将 div 返回到屏幕上。

我们的另一个焦点是`left`类。因为我们抽屉的宽度固定在`280px`处，所以当追加这个类时，我们可以使用`margin-left`向左“滑动”。

## 把所有的东西放在一起

现在我们已经把所有的零件都组装好了，我们还要做一件事。当我们创建新项目时,`Stencil`在这个文件中为我们提供了一个`index.html`,它已经为“示例”组件创建了所有的东西。

然而，因为我们做了更改，我们应该在`index.html`文件中反映这些更改。为了加快速度，复制下面的代码并将其替换到您的`index.html`文件中。

```
<!DOCTYPE html>
<html dir="ltr" lang="en">
  <head>
    <meta charset="utf-8" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=5.0"
    />
    Stencil Component Starter

    <script type="module" src="/build/example-drawer.esm.js"></script>
    <script nomodule src="/build/example-drawer.js"></script>
    <style>
      html,
      body {
        height: 100%;
        margin: 0;
      }
    </style>
  </head>
  <body>
    <my-component>
      <ul slot="drawer-content">
        <li>Menu 1</li>
        <li>Menu 2</li>
        <li>Menu 3</li>
        <li>Menu 4</li>
        <li>Menu 5</li>
      </ul>
      <div slot="content">
        Main
      </div>
    </my-component>
    <button id="toggleBtn">Toggle drawer</button>
  </body>
  <script>
    const btn = document.getElementById("toggleBtn");
    const = document.querySelector('my-component');
       btn.addEventListener("click", () => {
         drawer.toggleDrawer();
       });
  </script>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

我们改变了什么？我们已经添加了一个*未排序列表*，它将被放置在槽位:`drawer-content`中，我们还有一个`div`，它将被放置在槽位:`content`中。为了切换我们的抽屉，我们添加了一个简单的按钮，`on-click`将调用`drawer`上的`toggleDrawer`方法

## 总结

那么总结一下我们做了什么？我们已经创建了一个简单的抽屉，它可以滑出视图。我们设计了一款基本风格的抽屉，可根据您的需求进行改进。最棒的是，我们可以在任何使用普通 JS、Angular、React 或 Vue 等的前端应用程序中使用这个抽屉。

要查看完整的来源，请看一下[这个回购](https://github.com/Jefiozie/stencil-drawer)

我希望这篇文章能启发你，给你一些关于如何使用**模板**构建组件的见解，并从中获得一些乐趣！😁

## 一声谢谢

最后，感谢那些评论我的文章的人

*   [韦斯·格里姆斯(@韦斯·格里姆斯)](https://twitter.com/wesgrimes)
*   [罗慕洛·辛特拉(@罗慕洛·辛特拉)](https://twitter.com/romulocintra)
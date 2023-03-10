# 我在使用 Typescript 和 React 时的发现

> 原文：<https://dev.to/hurricaneinteractive/discoveries-i-made-while-using-typescript-and-react-16lk>

> 图片致谢 [@ellladee](https://unsplash.com/photos/RFId0_7kep4)

这个星期，我一直在使用 Typescript 开发一个 React 应用程序，我发现了一些非常有用的东西。这是我使用 Typescript 的第一个项目之一，到目前为止我还不想回去。其中一些发现可能是常识，但是对于一个 Typescript 新手来说，它们对于编写更好的代码非常有用。至少对我来说是这样。所以事不宜迟，让我们开始吧！

## 只允许一个对象上的特定关键点

当您想要限制可以添加到对象的关键点时，这非常有用。例如，允许另一个 dev 传递应该用作事件侦听器的函数。在这种情况下，您只希望开发人员传递有效的事件侦听器，以避免令人讨厌的错误。

```
type TListenerTypes = "onload" | "progress" | "error"
type TListeners = {
  [k in TListenerTypes]: Function
}

// Passes!
const listenerObj: TListeners = {
  onload: () => {}
}

// Error
const errorObj: TListeners = {
  a: "something", // wrong type
  progress: () => {},
  d: 10 // not in objectKeys type
}

// Improvement added by this comment (https://dev.to/theodesp/comment/bd1k)
type TListenerTypes = "onload" | "progress" | "error"

const x: Record<TListenerTypes, Function> = {
    a: "something", // wrong type
    progress: () => {},
    d: 10 // wrong type
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 分类故事书故事

在我正在做的项目中，我们使用[故事书](https://storybook.js.org/)来测试我们的组件。一旦你添加了几个故事，你就开始希望有一种方法将它们归类到相关的组中。幸运的是有一个解决方案！顺便说一下，我怎么推荐故事书都不为过。这是**超级**有用的视觉独立测试组件。借助*插件*的力量，你可以进行可访问性检查、明/暗模式测试等。

```
// uncategorised
storiesOf("Button", module).add(...)

// categorised under "Form"
storiesOf("Form|Selectbox", module).add(...) 
```

Enter fullscreen mode Exit fullscreen mode

## 传递一个组件作为道具

当我想在使用 [React 路由器](https://reacttraining.com/react-router/web/api/Route/render-func)时声明一个定制的`<Route>`组件时，这就成了一个问题。我需要一种方法将组件传递给自定义的`<Route>`，然后能够呈现该组件。这令人惊讶地讨厌。**提示**，如果你能够查看其他模块的类型定义，**去做吧！**我从现有的代码库中找到了相当多的解决方案，包括这个；

```
import { ComponentType } from "react"
import { RouteProps } from "react-router-dom"

interface ICustomRoute extends RouteProps {
  // Allows you to pass in components and then render them
  component: ComponentType<any>
}

const CustomRoute = ({
  component: Component,
  ...rest
}: ICustomRoute) => (
  <Route
    {...rest}
    render={props => (
      <Component {...props} />
    )}
  /> ) 
```

Enter fullscreen mode Exit fullscreen mode

## 允许原生 HTML 属性作为道具

假设您想要创建一个`<Input />`组件，它应该接受一个`<input />`元素的所有属性以及一个额外的`theme`对象。为了防止你为组件创建自定义定义，最好是扩展一个`<input />`元素的可用属性，而且，**你可以！**

```
import { HTMLAttributes } from "react"

type Theme = "light" | "dark"
interface IProps extends HTMLAttributes<HTMLInputElement> {
  // additional props if need
  theme: {
    variation: Theme
  }
}

// You might want to extract certain props and your custom props
// instead of just spreading all the props
// if you don't have additional props swap IProps for HTMLAttributes<HTMLInputElement>
const Input ({ theme, ...props }: IProps) => (
  <input
    {...props}
    className={`input input--${theme.variation}`}
  /> )

// Usage
<Input
  onChange={(e) => handle(e)}
  value={this.state.name}
  name="name"
  id="id"
  theme={{
    variation: "light"
  }}
/> 
```

Enter fullscreen mode Exit fullscreen mode

## 获取设备方位

这不是真正的 Typescript 或 React 相关，但是，它可能会导致一些有趣的事情。我完全可以想象这对一个很酷但也很没用的功能很有用。在 [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Detecting_device_orientation) 上了解更多信息。

```
// Check if it is supported
if (window.DeviceOrientationEvent) {
  window.addEventListener("deviceorientation", function(e) {
    console.log({
      x: e.alpha,
      y: e.beta,
      z: e.gamma
    })
  }, false)
} 
```

Enter fullscreen mode Exit fullscreen mode

## 包装完毕

每周我们都学习新的技术和不同的思维方式。我建议任何人记下你学到的不同技术。你不仅会创建一个小的知识库，当你看到自己取得的进步时，你也会变得更有动力。

* * *

谢谢你看我的文章，它真的意义重大！❤️，请提供任何反馈或意见，我总是期待改进和有意义的讨论。这篇文章是我的 [#myweekinjs](http://myweekinjs.com) 挑战的一部分，如果你有兴趣了解的话，我有一些有趣的文章。

### 👋下次见！
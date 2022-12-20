# 作为服务器端应用程序的模板

> 原文：<https://dev.to/paramharrison/react-as-templates-for-your-server-side-applications-1e9o>

您可能已经在 web 框架中使用了几个模板引擎。用于网站的常见模板引擎有:

*   `ejs`
*   `jade`
*   `jinja2`

在过去几年中，React 通过各种框架吸引了开发人员的大量关注。

在本文中，我们将讨论如何使用 react 作为模板引擎。

## ReactDomServer

有一些方法可以帮助将 react 组件编译成字符串或静态 HTML 标记。这些方法是，

*   renderToString
*   renderToStaticMarkup

### renderToString

*   从传递给它的组件中创建并返回 HTML 字符串。
*   从传递给它的组件中创建静态标记。

#### 有什么区别😅

是的，两者都是一样的，几乎没有什么不同。

*   使用`renderToStaticMarkup`创建没有添加任何基于 react 的标记的静态 HTML。它可以用作 HTML 转换的 react，从 React 生成静态页面等。
*   Use `renderToString`创建与 react 相关的额外 DOM 属性，并帮助客户端 react 使用它。它广泛用于通用渲染。第一次加载通过 renderToString 从服务器进行，然后客户端 react 利用它生成的属性并接管控制权。

### 用什么进行模板渲染🤔

取决于你的网站的需要，如果你不想在浏览器中使用 react，一般来说`renderToStaticMarkup`会更快并且没有任何过载。

#### 示例代码

```
/*
    - options are props from server to your react templates
    - component is your root component (eg., Layout of the page)
*/
let component = component.default || component;
let markup = ReactDOMServer.renderToStaticMarkup(
  React.createElement(component, options)
);
// whatever way you return HTML in your framework
return markup; 
```

希望这有所帮助。下一次，您可以选择 react 作为服务器端渲染项目的模板引擎。😎
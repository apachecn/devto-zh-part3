# 使用 Atomico 创建 web 组件而不使用类

> 原文：<https://dev.to/uppercod/create-web-components-without-using-a-class-with-atomico-2l97>

[![Atomico](img/2e47b56e5831b398392d6c35ebce6a53.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TEa_-_No--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/atomicojs/atomico/brand/h1.svg)

# 带 Atomico 的 WebComponents

Atomico 的诞生是为了简化 webcomponents 的开发，它应用了一种函数式方法，并完全消除了这种可见性！是的，再见，同学们。告诉我👇

## 💪非常强大的语法

```
// IMPORT
import { c, html, css } from "atomico";

// WEBCOMPONENT
function component({ message }) {
  return html`<host shadowDom>${message}</host>`;
}

// WEBCOMPONENT PROPERTIES AND ATTRIBUTES
component.props = {
  message: String,
};

// WEBCOMPONENT APPEARANCE
component.styles = css`
  :host {
    font-size: 30px;
  }
`;

// DEFINITION OF THE WEBCOMPONENT AS A TAG
customElements.define("my-component", c(component)); 
```

Enter fullscreen mode Exit fullscreen mode

让我们分部分分析代码...

### 导入

```
import { c, html, css } from "atomico"; 
```

Enter fullscreen mode Exit fullscreen mode

我们进口了什么？

1.  `c`:将功能组件转换成标准定制元素的功能。
2.  `html`:函数声明我们组件的模板，也可以使用 JSX。
3.  `css`:允许为我们的组件创建 CSS 样式表(CSS)的函数，只要它声明了 shadowDom。

### web 组件

```
function component({ message }) {
  return html`<host shadowDom>${message}</host>`;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的组件函数接收 component.props 中声明的所有 props(属性和特性)，组件函数声明 webcomponent 的所有逻辑和模板。Atomico 中的一个重要规则是**“用 Atomico 创建的每个组件必须总是返回标签“**”。

### 反应属性和 webcomponent 的属性

Atomico 检测组件的属性(Properties 和 Attributes ),这得益于 props 对象的关联，这通过使用索引和值允许您定义:

1.  **索引**:属性和属性的名称。
2.  **值**:道具的类型。

```
component.props = {
  message: String,
}; 
```

Enter fullscreen mode Exit fullscreen mode

从这个例子中，我们可以推断出 Atomico 将在我们的 webcomponent 中创建一个名为`message`的属性，它只能接收`String`类型的值。

### web 组件的外观。

Atomico 通过关联`styles`属性:
来检测组件的静态样式

```
component.styles = css`
  :host {
    font-size: 30px;
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

styles 接受单个或列表 CSSStyleSheet (CSS)值，CSS 函数返回的是标准 CSSStyleSheet，因此可以在 Atomico 之外共享。

### 定义你的 webcomponent

```
customElements.define("my-component", c(component)); 
```

Enter fullscreen mode Exit fullscreen mode

为了创建我们的标准 customElement，我们必须将我们的功能组件交付给 Atomico 模块的 c 函数，`c`函数将生成一个可以定义或扩展的 customElement 作为返回。

### 举例

[https://codepen.io/uppercod/embed/oNGdQzz?height=600&default-tab=js,result&embed-version=2](https://codepen.io/uppercod/embed/oNGdQzz?height=600&default-tab=js,result&embed-version=2)
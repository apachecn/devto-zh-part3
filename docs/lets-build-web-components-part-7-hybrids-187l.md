# 让我们来构建 Web 组件！第 7 部分:混合动力车

> 原文：<https://dev.to/bennypowers/lets-build-web-components-part-7-hybrids-187l>

基于组件的 UI 最近非常流行。您知道 web 有自己的本地组件模块，不需要使用任何库吗？真实故事！你可以编写、发布和重用单文件组件，这些组件可以在任何好的浏览器 [*](https://caniuse.com/#feat=shadowdomv1) 和任何框架[中工作(如果那是你的包的话)。](https://custom-elements-everywhere.com/)

在我们的[上一篇文章](https://dev.to/bennypowers/lets-build-web-components-part-6-gluon-27ll)中，我们看了一下[胶子](https://github.com/ruphin/gluonjs)以及它如何给你*恰到好处的*库支持来快速构建组件而不需要太多额外的东西。

[![bennypowers](img/57370d6f6dab8d6651a3f138a1aaaa40.png)](/bennypowers) [## 让我们来构建 Web 组件！第六部分:胶子

### 本尼·鲍尔斯🇮🇱🇨🇦10 月 28 日 186 分钟阅读

#webcomponents #gluon #javascript #html](/bennypowers/lets-build-web-components-part-6-gluon-27ll)

距离我们的上一部已经有一段时间了(原因我保证与《荒野之息》或《空心骑士》无关)，但是一旦你看到我们的作品，我想你会同意这是值得等待的。今天，我们正在检查我们迄今为止最不寻常和(依我拙见)有趣的 web 组件库-**。当我们从简单的对象定义和组合组件时，准备好获得功能，并且只在需要时注册它们。**

 ***   [大创意](#the-big-ideas)
*   [模板化](#templating)
    *   [混血儿更喜欢属性](#hybrids-prefers-properties-to-attributes)
    *   [绑定到`class`和`style`和](#binding-to-raw-class-endraw-and-raw-style-endraw-)
*   [属性描述符](#properties)
    *   [T2`get`](#-raw-get-endraw-)
    *   [T2`set`](#-raw-set-endraw-)
    *   [T2`connect`](#-raw-connect-endraw-)
    *   [工厂](#factories)
*   [致谢](#acknowledgements)

按照我们的习惯，我们将通过重新实现我们正在运行的例子——一个延迟加载的图像元素来感受混合。不过，在我们深入实际问题之前，让我们先简要了解一下混合动力车的一些独特特性。

## 大构想

与我们迄今为止看到的所有库不同，Hybrids 不处理典型的[定制元素](https://dev.to/bennypowers/lets-build-web-components-part-1-the-standards-3e85#custom-elements)类。不是从`HTMLElement`或其超类扩展，而是根据 <abbr title="Plain Old JavaScript Objects">POJO</abbr> s:

使用 Hybrids，你可以通过一个库函数来定义你的元素，而不是使用内置的浏览器工具:

```
import { define, html } from 'hybrids';

export const HelloWorld = {
  name: 'World',
  render: ({name}) => html`Hello, ${name}!`;
};

define('hello-world', HelloWorld); 
```

Enter fullscreen mode Exit fullscreen mode

这比普通版本简洁多了！

```
class HelloWorld extends HTMLElement {
  constructor() {
    super();
    this.__name = 'World';
    this.attachShadow({mode: 'open'});
    this.shadowRoot.appendChild(document.createTextNode('Hello, '));
    this.shadowRoot.appendChild(document.createTextNode(this.name));
  }

  get name() {
    return this.__name;
  }

  set name(v) {
    this.__name = v;
    this.render();
  }

  render() {
    this.shadowRoot.children[1].data = this.name;
  }
}

customElements.define('hello-world', HelloWorld); 
```

Enter fullscreen mode Exit fullscreen mode

此外，由于元素定义是一个简单的对象，所以通过组合而不是继承来修改元素要容易得多:

```
import { HelloWorld } from './hello-world.js';
define('hello-joe', { ...HelloWorld, name: 'Joe' }); 
```

Enter fullscreen mode Exit fullscreen mode

但是，您可能希望编写一个比“Hello World”更有意义的组件。那么，我们如何管理我们的混合组件的状态呢？让我们把正在运行的示例`<lazy-image>`元素带回来，以便更动态地使用。

由于 hybrids 对定制元素有自己非常独特的方法，我们对`<lazy-image>`的重写将不仅仅是简单地改变几个类 getters，所以让我们一点一点来，从元素的模板开始。

## 模板化

我们将在一个名为`render`的属性中定义元素的阴影子元素，这个属性是一个[一元函数](https://www.wikiwand.com/en/Unary_function)，它将宿主元素(即我们正在渲染的元素)作为其参数。

```
import { dispatch, html } from 'hybrids';

const bubbles = true;
const composed = true;
const detail = { value: true };
const onLoad = host => {
  host.loaded = true;
  // Dispatch an event that supports Polymer two-way binding.
  dispatch(host, 'loaded-changed', { bubbles, composed, detail })
};

const style = html`<style>/*...*/</style>`;
const render = ({alt, src, intersecting, loaded}) => html` ${style} <div id="placeholder"
      class="${{loaded}}"
      aria-hidden="${String(!!intersecting)}">
    <slot name="placeholder"></slot>
  </div>

  <img id="image"
      class="${{loaded}}"
      aria-hidden="${String(!intersecting)}"
      src="${intersecting ? src : undefined}"
      alt="${alt}"
      onload="${onLoad}"
    />
`;

const LazyImage = { render };

define('hybrids-lazy-image', LazyImage); 
```

Enter fullscreen mode Exit fullscreen mode

如果你加入了我们关于[发光元素](https://dev.to/bennypowers/lets-build-web-components-part-5-litelement-906)和[胶子](https://dev.to/bennypowers/lets-build-web-components-part-6-gluon-27ll)的帖子，你会注意到与我们之前的`<lazy-image>`实现有一些相似之处和一些明显的不同。

像`LitElement`和`GluonElement`一样，混合使用一个`html`模板文字标签函数来生成它们的模板对象。您可以将数据插入到模板的子模板或它们的属性中，使用模板返回函数映射数组，并将模板组合在一起，就像我们之前看到的一样。事实上，从表面上看，hybrids 和 lit-html 非常相似。但是要小心，这里有龙。虽然 hybrids 的模板系统受到了像`lit-html`和`hyper-html`这样的库的启发，但这不是一回事。你可以在 [hybrids 的模板系统文档](https://hybrids.js.org/template-engine/overview)中读到更多关于 lit-html 的具体区别。出于我们的目的，我们需要记住与`lit-html`的两大区别:

1.  绑定主要是针对属性，而不是特性。稍后会有更多的介绍。
2.  事件监听器被绑定了`on*`语法(例如`onclick`、`onloaded-changed`)，并把主机元素而不是事件作为它们的第一个参数，所以函数签名是`(host: Element, event: Event) => any`。

由于 Hybrids 强调纯函数，我们可以将`onLoad`处理程序提取到模块的根。即使它的主体引用了元素本身，也不需要担心什么`this`绑定！我们可以轻松地对这个处理程序进行单元测试，而根本不用实例化我们的元素。得分！

还要注意，我们从`hybrids`导入了一个`dispatch`助手，使触发事件不那么冗长。

在我们之前的实现中，我们在主机元素上使用了一个`loaded`属性来设计图像和占位符的样式，那么为什么我们现在要在它们上使用`class`呢？

### 混血儿更喜欢属性

Hybrids 强烈反对在元素的 API 中使用属性。因此，没有办法显式绑定到模板中元素的属性。那么我们是如何绑定到上面的`aria-hidden`属性的呢？

当您将某个值`bar`绑定到某个属性`foo`(通过在模板中设置`<some-el foo="${bar}">`)时，Hybrids 会检查元素的原型上是否存在同名的属性。如果是，hybrids 使用`=`赋值。然而，如果该属性在元素原型中不存在，Hybrids 使用`setAttribute`设置属性。保证属性绑定的唯一方法是将字符串显式绑定为属性值，即`<some-el foo="bar">`或`<some-el foo="bar ${baz}">`。

因此，在 Hybrids-land 中不将属性映射到属性也是有意义的(在关于[工厂](#factories)的章节中，我们将讨论一种允许我们这样做的替代方法)。因此，我们不需要将我们的样式从主机属性中分离出来，我们只需要传递一个类就可以了:

```
#placeholder ::slotted(*),
#image.loaded {
  opacity: 1;
}

#image,
#placeholder.loaded ::slotted(*) {
  opacity: 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 绑定到`class`和`style`

因为`class`属性映射到`classList`属性，所以 hybrids 以不同的方式处理该属性。您可以将带有布尔值的字符串、数组或对象传递给一个`class`绑定。

*   对于字符串，hybrids 将使用`setAttribute`来设置该字符串的`class`属性。
*   对于数组，hybrids 会将每个数组成员添加到`classList`
*   对于对象，hybrids 将把每个具有真值的键添加到`classList`中，类似于`classMap` lit-html 指令。

所以下面是等价的:

```
html`<some-el class="${'foo bar'}"></some-el>`;  
html`<some-el class="${['foo', 'bar']}"></some-el>`;  
html`<some-el class="${{foo: true, bar: true, baz: false}}"></some-el>`; 
```

Enter fullscreen mode Exit fullscreen mode

尽可能避免绑定到`style`的最好方法是给元素的影子根添加一个样式标签，但是如果你需要绑定到元素的`style`属性(例如，你有动态更新的样式不能被类提供)，你可以传入那种 css-in-js 对象，这已经成为许多开发人员圈子里的*的惯例*:

```
const styles = {
  textDecoration: 'none',
  'font-weight': 500,
};

html`<some-el style="${styles}"></some-el>`; 
```

Enter fullscreen mode Exit fullscreen mode

## 属性描述符

如果我们用上面的`LazyImage`对象来定义我们的元素，那就没什么用了。只有当元素的一个观察属性被设置时，Hybrids 才会调用`render`。为了定义那些观察到的属性，我们需要向我们的对象添加属性描述符，这些属性描述符只是具有除了`render`之外的任何名称的键。

```
const LazyImage = {
  alt: '',
  src: '',
  intersecting: false,
  loaded: false,
  render;
}; 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我们将每个属性描述为简单的静态标量值。在这种情况下，Hybrids 会用这些值初始化我们的元素，然后每当它们被设置为 [*](#simple-descriptors) 时就调用`render`。超级有效，但是有点无聊，对吧？为了添加我们的延迟加载秘方，让我们为`intersecting`属性定义一个更复杂的描述符。

具有真正自信的描述符是在三个键中的一个或多个键上具有功能的对象:`get`、`set`和`connect`。这些函数中的每一个都将`host`作为它们的第一个参数，很像我们在上面的模板中定义的`onLoad`事件监听器。

### `get`

不出所料，每当属性被读取时,`get`函数就会运行。如果您愿意，可以在这里设置一些逻辑来计算属性。尽可能避免副作用，但是如果需要读取前一个值来计算下一个值，可以将它作为第二个参数传递给函数。

这个简单的例子展示了从元素的`day`、`month`和`year`属性:
计算出的 ISO 日期字符串

```
const getDateISO = ({day, month, year}) =>
  (new Date(`${year}-${month}-${day}`))
    .toISOString();

const DateElementDescriptors = {
  day: 1,
  month: 1,
  year: 2019,
  date: { get: getDateISO }
} 
```

Enter fullscreen mode Exit fullscreen mode

Hybrids 将检查属性的当前值是否不同于从`get`返回的值，如果不是，它将不会运行效果(例如调用`render`)。像对象和数组这样的引用类型通过简单的等价性来检查，所以[你应该使用不可变的数据技术来确保你的元素重新呈现](https://open-wc.org/help/js.html#modifying-an-array-or-object-s-members-does-not-trigger-rerender)。

### `set`

如果你需要在赋值的时候操作一个值，甚至(喘息！)执行副作用，您可以用`set`来完成，它接受`host`、新值和最后一个值。

```
import { targetDate } from './config.js';

const setDateFromString =  (host, value, previous) => {
  const next = new Date(value);
  // reject sets after some target date
  if (next.valueOf() < targetDate) return previous;
  host.day = next.getDate();
  host.month = next.getMonth();
  host.year = next.getYear();
  return (new Date(value)).toISOString();
}

const DateElementDescriptors = {
  day: 1,
  month: 1,
  year: 2019,
  date: {
    get: getDateISO,
    set: setDateFromString,
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果省略`set`功能，hybrids 会自动添加一个直通设置器(即`(_, v) => v` ) [**](#pass-through-setter) 。

### `connect`

到目前为止，hybrids 已经废除了类和绑定，但是我们还没有完成。混合动力车砧板上的下一个受害者是生命周期回调。如果您想在创建或销毁元素时做任何工作，您可以在`connect`函数中基于每个属性来做。

您的`connect`函数接受`host`、属性名和一个函数，该函数在被调用时将使该属性的缓存条目无效。您可以在 redux 动作、事件监听器、承诺流等中使用`invalidate`。`connect`在`connectedCallback`中被调用，应该返回一个将在`disconnectedCallback`中运行的函数。

```
import { targetDate } from './config.js';

/** connectDate :: (HTMLElement, String, Function) -> Function */
const connectDate = (host, propName, invalidate) => {
  const timestamp = new Date(host[propName]).valueOf();
  const updateTargetDate = event => {
    targetDate = event.target.date;
    invalidate();
  }

  if (timestamp < targetDate)
    targetDateForm.addEventListener('submit', updateTargetDate)

  return function disconnect() {
    targetDateForm.removeEventListener('submit', updateTargetDate);
  };
}

const DateElementDescriptors = {
  day: 1,
  month: 1,
  year: 2019,
  date: {
    get: getDateISO,
    set: setDateFromString,
    connect: connectDate
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在`<hybrids-lazy-image>`中，我们将使用`connect`来设置我们的交叉点观察器。

```
const isIntersecting = ({ isIntersecting }) => isIntersecting;
const LazyImage = {
  alt: '',
  src: '',
  loaded: false,
  render,
  intersecting: {
    connect: (host, propName) => {
      const options = { rootMargin: '10px' };
      const observerCallback = entries =>
        (host[propName] = entries.some(isIntersecting));
      const observer = new IntersectionObserver(observerCallback, options);
      const disconnect = () => observer.disconnect();
      observer.observe(host);
      return disconnect;
    }
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 工厂

为每个属性编写相同风格的描述符会很乏味，所以 hybrids 推荐使用“工厂”来抽象掉这种重复。

工厂只是返回一个对象的函数。出于我们的目的，它们是返回属性描述符对象的函数。Hybrids 带有一些内置工厂，但您可以轻松定义自己的工厂。

```
const constant = x => () => x;
const intersect = (options) => {
  if (!('IntersectionObserver' in window)) return constant(true);
  return {
    connect: (host, propName) => {
      const options = { rootMargin: '10px' };
      const observerCallback = entries =>
        (host[propName] = entries.some(isIntersecting));
      const observer = new IntersectionObserver(observerCallback, options);
      const disconnect = () => observer.disconnect();
      observer.observe(host);
      return disconnect;
    }
  }
}

const LazyImage = {
  alt: '',
  src: '',
  loaded: false,
  intersecting: intersect({ rootMargin: '10px' }),
  render,
} 
```

Enter fullscreen mode Exit fullscreen mode

在这种特殊情况下，胜利是相当肤浅的，我们只是黑盒子的描述符。当您使用工厂来定义属性的可重用逻辑时，它们真的会大放异彩。

例如，尽管 hybrids 强烈建议不要使用属性，但我们可能确实希望我们的元素将属性值反映为属性，就像许多内置元素所做的那样，也像[标签指南推荐的](https://w3ctag.github.io/webcomponents-design-guidelines/#native-html-elements)。对于这些情况，我们可以为我们的属性编写一个`reflect`工厂:

```
import { property } from 'hybrids';

export const reflect = (defaultValue, attributeName) => {
  // destructure default property behaviours from built-in property factory.
  const {get, set, connect} = property(defaultValue);
  const set = (host, value, oldValue) => {
    host.setAttribute(attributeName, val);
    // perform hybrid's default effects.
    return set(host, value, oldValue);
  };

  return { connect, get, set };
}; 
```

Enter fullscreen mode Exit fullscreen mode

工厂是混血儿最强大的模式之一。例如，您可以使用它们来创建使用混合缓存作为状态存储的数据提供者元素装饰器。参见 [`parent`](https://hybrids.js.org/built-in-factories/parent-children) 工厂示例。

## 最终组件

[https://stackblitz.com/edit/hybrids-lazy-image?](https://stackblitz.com/edit/hybrids-lazy-image?)

```
import { html, define, dispatch } from 'hybrids';

const style = html`
  <style>
    :host {
      display: block;
      position: relative;
    }

    #image,
    #placeholder ::slotted(*) {
      position: absolute;
      top: 0;
      left: 0;
      transition:
        opacity
        var(--lazy-image-fade-duration, 0.3s)
        var(--lazy-image-fade-easing, ease);
      object-fit: var(--lazy-image-fit, contain);
      width: var(--lazy-image-width, 100%);
      height: var(--lazy-image-height, 100%);
    }

    #placeholder ::slotted(*),
    #image.loaded {
      opacity: 1;
    }

    #image,
    #placeholder.loaded ::slotted(*) {
      opacity: 0;
    }
  </style>
`;

const constant = x => () => x;
const passThroughSetter = (_, v) => v;
const isIntersecting = ({isIntersecting}) => isIntersecting;
const intersect = (options) => {
  if (!('IntersectionObserver' in window)) return constant(true);
  return {
    connect: (host, propName) => {
      const observerCallback = entries =>
        (host[propName] = entries.some(isIntersecting));
      const observer = new IntersectionObserver(observerCallback, options);
      const disconnect = () => observer.disconnect();
      observer.observe(host);
      return disconnect;
    }
  }
}

const bubbles = true;
const composed = true;
const detail = { value: true };
const onLoad = host => {
  host.loaded = true;
  // Dispatch an event that supports Polymer two-way binding.
  dispatch(host, 'loaded-changed', { bubbles, composed, detail })
};

const render = ({alt, src, intersecting, loaded}) => html` ${style} <div id="placeholder"
      class="${{loaded}}"
      aria-hidden="${String(!!intersecting)}">
    <slot name="placeholder"></slot>
  </div>

  <img id="image"
      class="${{loaded}}"
      aria-hidden="${String(!intersecting)}"
      src="${intersecting ? src : undefined}"
      alt="${alt}"
      onload="${onLoad}"
    />
`;

define('hybrids-lazy-image', {
  src: '',
  alt: '',
  loaded: false,
  intersecting: intersect({ rootMargin: '10px' }),
  render,
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

Hybrids 是一个独特的、现代的、自以为是的 web 组件创作库。它为注重功能的组件作者带来了诱人的特性，如不可变的数据模式、对纯函数的强调以及简单的可组合性。有了功能 UI 世界和老式 OOP 模式的平衡组合，并利用这些标准来提高性能和用户体验，在您的下一个项目中尝试一下是值得的。

| 赞成的意见 | 骗局 |
| --- | --- |
| 强调纯功能和组合的高功能 API | 强烈的意见可能与您的用例冲突，或者需要您从其他方法中返工模式 |
| 极其简单的组件定义让您关注更高层次的问题 | 抽象 API 使得按原样处理 DOM 变得更加麻烦 |

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[hybrids js](https://github.com/hybridsjs)/[hybrids](https://github.com/hybridsjs/hybrids)

### 非凡的 JavaScript UI 框架，具有独特的声明性和功能性架构

<article class="markdown-body entry-content container-lg" itemprop="text">

# [![hybrids](img/8d74ae79c379cf59e26eb3206bea6a0d.png)](https://raw.githubusercontent.com/hybridsjs/hybrids/main/docs/assets/hybrids-full-logo.svg?sanitize=true)

[![build status](img/15a5aa15be2ca9132802a84fa4bba0e4.png)](https://github.com/hybridsjs/hybrids/actions/workflows/test.yml?query=branch%3Amain)[![coverage status](img/bcf398742c8f6ce7ac3db3999bbc0c46.png)](https://coveralls.io/github/hybridsjs/hybrids?branch=main)[![npm version](img/e38aed3fd42d3a246ff244f63d653e08.png)](https://www.npmjs.com/package/hybrids)

> 一个非凡的 JavaScript 框架，用于创建客户端 web 应用程序、UI 组件库或具有独特的混合声明性和功能性架构的单个 web 组件

**hybrids** 为 web 平台提供了一整套工具——一切都没有外部依赖性:

*   **基于简单对象和纯功能的组件模型**
*   **全局状态管理**外部存储、离线缓存、关系等
*   **基于视图图形结构的类 App 路由**
*   **本地化**自动翻译模板内容
*   **布局引擎**使 UI 布局开发更快
*   **热模块更换**支持和其他 DX 功能

### 证明文件

项目文件可在[hybrids.js.org](https://hybrids.js.org)网站获得。

## 快速查找

### 组件模型

它基于普通对象和纯函数*，仍然使用幕后的 [Web 组件 API](https://developer.mozilla.org/en-US/docs/Web/Web_Components) :

```
import { html, define } from "hybrids";
function increaseCount(host) {
  host.count += 1;
}

export default define({
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/hybridsjs/hybrids)

您想就此处涉及的任何主题进行一对一的辅导吗？[![Contact me on Codementor](img/97f0e0737ce864c47f2412396b01e737.png)](https://www.codementor.io/bennyp?utm_source=github&utm_medium=button&utm_term=bennyp&utm_campaign=github)

## 鸣谢

特别感谢 hybrids 的作者和主要维护者张秀坤·卢巴斯基，他在我准备这篇文章时慷慨地贡献了他的时间和见解，尤其是他帮助重构了一种惯用的 Hybrids 风格。

[![smalluban image](img/6861718a2fd849252ddb6888604c411f.png)](/smalluban)

## [张秀坤·鲁班斯基](/smalluban)T2 紧随其后

[JavaScript passionate. Hybrids library author. Piano player.](/smalluban)

 实际上 hybrids 在这里做的是为你生成简单的描述符，以确保属性效果运行，等等。
 截至最初发布时，省略`set`时添加直通设置器的行为尚未发布。

<small>2020-10-31:编辑过的普通示例</small>**
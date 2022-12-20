# 从类到简单的对象和纯函数

> 原文：<https://dev.to/hybrids/from-classes-to-plain-objects-and-pure-functions-2gip>

这是关于 [hybrids](https://github.com/hybridsjs/hybrids) 核心概念系列文章的第一篇——这是一个用简单实用的 API 创建 Web 组件的库。

ES2015 引入了现在在 UI 库和框架中广泛使用的类。然而，它们是在 JavaScript 中创建基于组件的逻辑的最佳方式吗？在我的上一篇文章中，我强调了一些主要的类陷阱:

[![smalluban image](img/6861718a2fd849252ddb6888604c411f.png)](/smalluban) [## 我们真的需要 JavaScript 中的类吗？

### 张秀坤·卢巴斯基 12 月 11 日 185 分钟阅读

#discuss #javascript #webdev #webcomponents](/smalluban/do-we-really-need-classes-in-javascript-after-all-91n)

hybrids 库是关于我们如何采取不同的方法，并为构建 web 组件创建简单而实用的工具的研究成果。然而，创建定制元素的唯一方法是使用扩展了`HTMLElement`的`class`，并用定制元素 API 定义它。除此之外别无他法(你也可以使用函数构造函数来恰当地反映`super()`调用)。那么，hybrids 怎么可能使用普通对象而不是类呢？

答案是三个属性相关概念的组合:属性描述符、属性工厂和属性转换。让我们用一个简单的定制元素定义将这些概念分解成一步一步的过程。

## 第一步:使用自定义元素 API

为了更好地理解这个过程，我们将使用一个对定制元素 API 有最低要求的例子。这里的目标是展示我们如何从类定义切换到具有纯函数的普通对象。

```
class MyElement extends HTMLElement {
  constructor() {
    this.firstName = 'Dominik';
    this.lastName = 'Lubański';
  }

  get fullName() {
    return `${this.firstName}  ${this.lastName}`;
  }
}

customElements.define('my-element', MyElement); 
```

Enter fullscreen mode Exit fullscreen mode

我们的定制元素定义有两个简单的属性(`firstName`和`lastName`)和一个计算属性，它返回前两个属性的连接。该示例不包含方法，但是可以使用相同的过程轻松地转换它们(可以将方法定义为 computed property，它返回一个函数)。

## 步骤 2:使用原型的 Desugar 类语法

类语法只不过是位于函数及其原型之上的语法糖。每个类定义都有`prototype`属性，它保存类方法(除了`constructor`)。重要的是，我们可以在定义之后更改它，这样类的主体就可以是空的。可以使用`Object.defineProperty()`方法在`MyElement.prototype`上直接定义属性。原型委托可能会意外地使用正常值，所以我们应该只定义计算属性，这些属性返回与上下文相关的值。

```
class MyElement extends HTMLElement {}

// before: this.firstName in constructor()
Object.defineProperty(MyElement.prototype, 'firstName', {
  get: function get() {
    return this._firstName || 'Dominik';
  },
  set: function set(val) {
    this._firstName = val;
  },
  configurable: true,
});

// before: this.lastName in constructor()
Object.defineProperty(MyElement.prototype, 'lastName', {
  get: function get() {
    return this._lastName || 'Lubański';
  },
  set: function set(val) {
    this._lastName = val;
  },
  configurable: true,
});

// before: fullName computed property in the class body
Object.defineProperty(MyElement.prototype, 'fullName', {
  get: function fullName() {
    return `${this.firstName}  ${this.lastName}`;
  },
  configurable: true,
});

customElements.define('my-element', MyElement); 
```

Enter fullscreen mode Exit fullscreen mode

我们似乎后退了一步。代码变得更加冗长和冗余(类定义的简单结构是引入类语法的原因之一)。此外，当前的实现与最初的实现不一致。如果我们将其中一个属性设置为 falsy 值，它仍然会返回一个默认值。我们将在第五步中处理这个问题。现在，我们必须集中精力清理我们的定义。

## 第三步:将冗余代码隐藏到自定义定义中

所有属性都由`Object.defineProperty()`方法定义。我们可以将传递的参数提取到属性名和描述符的映射中，并将其余的放入自定义函数中，该函数将替换`customElements.define()`方法。

```
const MyElement = {
  firstName: {
    get: function get() {
      return 'Dominik' || this._firstName;
    },
    set: function set(val) {
      this._firstName = val;
    },
  },
  lastName: {
    get: function get() {
      return 'ConFrontJS' || this._lastName;
    },
    set: function set(val) {
      this._lastName = val;
    },
  },
  fullName: {
    get: function fullName() {
      return `${this.firstName}  ${this.lastName}`;
    },
  },
};

defineElement('my-element', MyElement); 
```

Enter fullscreen mode Exit fullscreen mode

这就是**属性描述符**概念的工作原理。`MyElement`现在是一个带有属性描述符映射的普通对象，我们在定制元素原型上定义了属性描述符。

我们的`defineElement()`函数可以这样定义:

```
function defineElement(tagName, descriptors) {
  class Wrapper extends HTMLElement {}

  Object.keys(descriptors).forEach(key => {
    Object.defineProperty(Wrapper.prototype, key, {
      ...descriptors[key],
      configurable: true,
    });
  });

  return customElements.define(tagName, Wrapper);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 第四步:摆脱“这个”

自定义函数为进一步优化开辟了道路。从现在开始，我们可以完全控制输入的结构。函数可以动态地创建属性描述符，而不是将属性描述符传递给`Object.defineProperty()`。我们终于可以干掉最后一个屹立不倒的堡垒- `this`关键词。

`get`和`set`方法的第一个参数可能成为一个`host`——一个元素实例。因此，我们不再需要通过关键字`this`访问定制元素实例。此外，方法是[纯](https://en.wikipedia.org/wiki/Pure_function)的——它们只依赖于参数，没有副作用。移除上下文还允许使用 ES2015 的一些有用功能，如箭头功能和解构函数参数。

```
const MyElement = {
  firstName: {
    get: ({ _firstName }) => _firstName || 'Dominik',
    set: (host, value) => { host._firstName = value; },
  },
  lastName: {
    get: ({ _lastName }) => _lastName || 'Lubański',
    set: (host, value) => { host._lastName = value; },
  },
  fullName: {
    get: ({ firstName, lastName }) => `${firstName}  ${lastName}`,
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们的定义已经大大缩小了。我们已经用箭头函数替换了普通函数，并且已经为`get`调用析构了`host`参数。

## 第五步:添加中间件保存属性值

设计的计算属性不保存其值。该定义是一对函数(不是值)，其中一个函数返回从外部依赖项获取的属性的当前状态，第二个函数更新那些外部依赖项。在我们当前的解决方案中，`firstName`和`lastName`属性依赖于自定义元素实例中的`_firstName`和`_lastName`属性(它们是在第一次调用`set`方法时定义的)。

使用第三步中的事实，我们可以在自定义 define 函数的属性定义过程中引入一个局部变量。该值可以作为新的最后一个参数- `lastValue`传递给`get`和`set`方法。从现在开始，`get`和`set`方法应该返回属性的当前值。

```
const MyElement = {
  firstName: {
    get: (host, lastValue = 'Dominik') => value,
    set: (host, value, lastValue) => value,
  },
  lastName: {
    get: (host, lastValue = 'Lubański') => value,
    set: (host, value, lastValue) => value,
  },
  fullName: {
    get: ({ firstName, lastName }) => `${firstName}  ${lastName}`,
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

您可以注意到默认值现在是如何处理的。我们已经开始使用 ES2015 的另一项功能——默认参数。如果没有值或`undefined`被传递，则这些参数被初始化为默认值。比用`||`算子的解决方案好多了。尽管如此，如果我们将`firstName`和`lastName`设置为`undefined`，它们将返回`Dominik`或`Lubański`(在现实场景中，这不成问题，因为我们可以使用库中的内置工厂，这涵盖了这种情况)。

## 第六步:引入属性工厂

在所有的优化之后，我们可以再次找到冗余代码- `firstName`和`lastName`属性描述符变得几乎相同。只有默认值不同。为了使它更简洁，我们可以创建一个函数属性工厂，它返回由参数参数化的属性描述符。

```
export function property(defaultValue) {
  return {
    get: (host, lastValue = defaulValue) => value,
    set: (host, value) => value,
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以用`property()`函数调用:
替换`firstName`和`lastName`描述符

```
import property from './propertyFactory';

const MyElement = {
  firstName: property('Dominik'),
  lastName: property('Lubański'),
  fullName: {
    get: ({ firstName, lastName }) => `${firstName}  ${lastName}`,
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

有了**属性工厂**的概念，我们只用一行代码就可以定义属性！工厂隐藏实现细节，尽量减少冗余代码。

## 第七步:引入属性翻译

我们还有最后一个概念要遵循。我们的自定义 define 函数只接受描述符，这些描述符是具有预定义结构的对象。如果我们允许传递原语、函数甚至对象，但没有定义方法，会发生什么？

**属性转换**概念提供了一组规则，用于转换与属性描述符结构不匹配的属性定义。它支持原语、函数，甚至对象(没有描述符键)。

例如，如果我们将`firstName`属性的值设置为一个原语，那么库使用内置的`property`工厂在定制元素的原型上定义它。在另一种情况下，如果你设置属性值为一个函数，它会被用`get`方法转换成一个描述符对象。

因此，自定义元素定义可以是默认值的简单结构和没有外部依赖性的纯函数:

```
const MyElement = {
  firstName: 'Dominik',
  lastName: 'Lubański',
  fullName: ({ firstName, lastName }) => `${firstName}  ${lastName}`,
} 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

今天的编码之旅到此结束。在最后一步中，我们创建了最简单的可能定义，没有`class`和`this`语法，但是具有真正的纯函数可组合结构。

整个过程表明，用一个简单的属性描述符概念来代替命令式和有状态类定义是可能的。另外两个，属性工厂和属性转换，允许进一步简化定义。

## 接下来是什么？

通常，定制元素比我们的简单例子做得更多。它们执行异步调用，观察内部和外部状态的变化并做出反应，等等。为了涵盖这些特性，基于组件的库引入了复杂的生命周期方法和机制来管理外部和内部状态。如果所有这些都不再需要，你会说什么？

在本系列的下一篇文章中，我们将更深入地研究属性描述符的定义，了解更多关于缓存机制、变更检测和独立的`connect`方法的知识。

[![smalluban image](img/6861718a2fd849252ddb6888604c411f.png)](/smalluban) [## 告别生命周期方法，专注于高效的代码

### 张秀坤·卢巴斯基 1919 年 1 月 29 日 17 分钟阅读

#webdev #javascript #webcomponents #functional](/hybrids/how-to-say-goodbye-to-lifecycle-methods-and-focus-on-productive-code-175)

你可以在[项目文档](https://hybrids.js.org)中阅读更多关于混合库的信息。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[hybrids js](https://github.com/hybridsjs)/[hybrids](https://github.com/hybridsjs/hybrids)

### 从普通对象和纯函数创建 web 组件的最简单方法！💯

<article class="markdown-body entry-content container-lg" itemprop="text">

# [![hybrids - the web components](img/7835334dab31a0355c19ba1881e91e6e.png)](https://raw.githubusercontent.com/hybridsjs/hybrids/master/docs/assets/hybrids-full-logo.svg?sanitize=true)T3】

[![npm version](img/e38aed3fd42d3a246ff244f63d653e08.png)](https://www.npmjs.com/package/hybrids)[![bundle size](img/e7096235243d88623d28c0853db0d596.png)](https://bundlephobia.com/result?p=hybrids)[![types](img/a28e08b57c6b70b0919ab8465c56c835.png)](https://github.com/hybridsjs/hybrids/blob/master/types/index.d.ts)[![build status](img/6acbe72ed8add64a16839f48caf1b2c4.png)](https://travis-ci.com/hybridsjs/hybrids)[![coverage status](img/efe2f85a0edbb1ade946f6fee616132a.png)](https://coveralls.io/github/hybridsjs/hybrids?branch=master)[![npm](img/9668c23a207d986b16e5fc71f7b421ae.png)](https://www.npmjs.com/package/hybrids)[![gitter](img/c4b1678de1d3017fd92d1ceb3704f144.png)](https://gitter.im/hybridsjs/hybrids)[![twitter](img/0bd5d9f1184388e52b4a074597fa5b12.png)](https://twitter.com/hybridsjs)[![Conventional Commits](img/5c9ec1154f801f65b71e877883f3927c.png)](https://conventionalcommits.org)[![code style: prettier](img/76245b01cc15290741bd336634799095.png)](https://github.com/prettier/prettier)[![GitHub](img/742d640928cb2cc526e6b43c4de7a98f.png)](https://raw.githubusercontent.com/hybridsjs/hybrids/master/LICENSE)

> <g-emoji class="g-emoji" alias="medal_sports" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f3c5.png">🏅</g-emoji>2019 年[开源奖](https://osawards.com/javascript/)的**“年度突破”**类四个提名项目之一

**hybrids** 是一个 UI 库，用于创建 [web 组件](https://www.webcomponents.org/)，它具有基于简单对象和纯函数的独特声明性和函数性方法。

*   **最简单的定义**——只是简单的对象和纯粹的函数——没有`class`和`this`语法
*   **无全球生命周期** —独立的资产，拥有自己简化的生命周期方法
*   **继承之上的组合** —易于重用、合并或拆分属性描述符
*   **超快速重新计算** —智能缓存和变更检测机制
*   **全局状态管理** -支持外部存储的模型定义
*   **没有外部工具的模板** —基于标记模板文字的模板引擎
*   **开发人员工具包括** —开箱即用的 HMR 支持，实现快速愉快的开发

## 快速查找

将 hybrids [npm 包](https://www.npmjs.com/package/hybrids)添加到您的应用程序中，导入所需的特性，并定义您的自定义元素:

```
import { html
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/hybridsjs/hybrids)

* * *

🙏你如何支持这个项目？给 GitHub 库一个⭐️，在⬇️下面评论，把混血儿的消息传播到全世界📢！

* * *

👋欢迎开发者加入社区！我叫 Dominik，这是我写的第二篇博客——欢迎任何反馈，❤️.

<small>封面照片由 [Patrick Robert Doyle](https://unsplash.com/photos/sWGDbVPcPdg) 在[Unsplash](https://unsplash.com)T5 拍摄</small>
# 人道介绍反应(和 JSX，和一些 ES 6)

> 原文：<https://dev.to/kaisershahid/humane-intro-to-react-and-jsx-and-some-es-6-3amf>

*   [什么是 React？](#what-is-react-)
*   [使用 jQuery 的复选框-文本框示例](#the-checkbox-textbox-example-using-jquery)
    *   [结论](#conclusion)
*   [React 中的复选框-文本框示例](#the-checkbox-textbox-example-in-react)
    *   [ES 6 和 JSX 快速入门](#es-6-and-jsx-quick-primer)
    *   [让我们的组件交互](#making-our-component-interactive)
    *   [状态(`this.state` )](#state---thisstate--)
    *   [解构(ES 6)](#destructuring--es-6-)
    *   [尝试一下](#try-it-out)
    *   [箭头功能(ES 6)](#arrow-functions--es-6-)
    *   [尝试一下](#try-it-out-1)
    *   [结论](#conclusion-1)
    *   [提升状态上升](#lifting-state-up)
*   [嵌套组件](#nested-components)
    *   [属性(`this.props` )](#properties---thisprops--)
    *   [使用我们的新组件](#using-our-new-component)
    *   [与父组件通信(间接)](#communicate-with-parent-component--indirectly-)
    *   [结论](#conclusion-2)
*   [包装完毕](#wrapping-it-up)
    *   React 是如何做到这一点的？
    *   [进一步阅读](#further-readings)
    *   [接下来是](#next-up)

<small>*[用 markdown-toc 生成的目录](http://ecotrust-canada.github.io/markdown-toc/)*</small>

Javascript 是我学习的第一门编程语言，大约是在 1998 年。我一直很喜欢它，但我觉得我在 2000 年代早期就开始使用它了——jQuery 和 MooTools 是流行的框架。您从头开始编写了许多代码，并且在一座小山上走了 5 英里来恢复更改。

因此，当我涉足 Node.js、React 和 npm 的新世界时，我被事情变得多么简单以及具有讽刺意味的是，生态系统和工具变得多么复杂这两个事实震惊了。

有人亲自向我解释，并解答了我的问题，我才开始理解什么是 React】。也许我在这里有点笨，但是我遇到的文档在另一个人解释之前没有意义。

也许最终没有办法解决这个问题，但是，我会尽可能人道地解释 React，这样你就可以有机地理解它。你应该已经熟悉了 [Javascript](#todo) 和 [HTML](#todo) 。与其让你设置一个本地环境，我会在 [jsfiddle](https://jsfiddle.net) 上发布实例。

我们走吧。

* * *

## 什么是反应？

[React](https://reactjs.org/) 是脸书创建的 UI 框架。它与一种叫做[通量](https://facebook.github.io/flux/docs/in-depth-overview.html)(也叫脸书)的设计模式配合得很好。

目标是有一个简单的、单向的连接 UI 到数据和用户输入的流程。不再需要用`onEvent`处理程序标记 HTML，也不再需要单独编写一堆具有复杂关系的 jQuery。

> 通过单向，取`<a><b>text</b></a>`的基本结构。我们可以把这个关系写成`a -> b`。对`a`的任何改变都会强制`a`和`b`重新渲染，而对`b`的任何改变只会重新渲染。`b`对`a`一无所知。

不过，我不会从 React 开始。

* * *

## 使用 jQuery 的 Checkbox-Textbox 示例

假设我们有一个可以输入截止日期的复选框，但是我们需要用一个复选框显式地启用它。如果未选中该复选框，则该字段应为空白并被禁用。像这样:

```
<form id='example-01'>
<input type="checkbox" name="enableExpiration" />
<input type="text" name="expirationDate" disabled />
</form> 
```

我将使用 [jQuery](https://jquery.com/) 来写这个:

```
var textbox = $("#example-01 input[name='expirateDate']");
var checkbox = $("#example-01 input[name='enableExpiration']");
checkbox.on('click', function(event) {
   if (event.currentTarget.checked) {
       textbox.prop('disabled', false);
   } else {
       textbox.val('').prop('disabled', 'disabled');
   }
}); 
```

> 参见[js dild](https://jsfiddle.net/kaisershahid/3j7qefzn/13/)

很简单。现在让我们添加一些验证——如果日期不正确(假设它只是字母数字字符),当您改变焦点时，在控件下显示一个红色的错误框。现在我们的 HTML 可能看起来像这样:

```
<form id='example-02'>
<input type="checkbox" name="enableExpiration" />
<input type="text" name="expirationDate" disabled />
<div class="errorMessage" style="display: none; background: red; color: white">
</div>
</form> 
```

我们的 Javascript 应该是这样的:

```
var textbox = $("#example-02 input[name='expirateDate']");
var checkbox = $("#example-02 input[name='enableExpiration']");
var errorMessage = $("#example-02 .errorMessage");

checkbox.on('click', function(event) {
   if (event.currentTarget.checked) {
       textbox.prop('disabled', false);
   } else {
       textbox.val('').prop('disabled', true);
   }
});

textbox.on('blur', function(event) {
    console.log('blur');
    if (!textbox.val().match(/^[\w]+$/)) {
        errorMessage.show().html('Invalid date!');
    } else {
        errorMessage.hide();
    }
}).on('focus', function(event) {
    errorMessage.hide();
}); 
```

> 参见[js dild](https://jsfiddle.net/kaisershahid/ucg8563n/11/)

### 结论

我们目前的关系是这样的:

```
Checkbox -> Textbox -> ErrorMessage 
```

到目前为止，一切看起来都很好，也易于管理。但是随着你的需求开始增长，这种
可能会很快失控。还有很多样板代码。当你想在其他地方重用这个字段时会发生什么呢？

让我们更进一步，假设我们已经有了一个系统的方法来管理这些字段集并创建不同的实例。这是我们可以轻松应用到新领域的东西吗？

* * *

## React 中的复选框-文本框示例

首先，让我们把之前的复选框-文本框-错误组合想象成一个名为**可选截止日期**的字段。我们可以像这样写一些 JS:

```
// OptionalExpirationDate.js
import React from 'react';

class OptionalExpirationDate extends React.Component {
    render() {
        return <div className="optional-expiration-date">hello</div>;
    }
}

export default OptionalExpirationDate; 
```

如果你从未见过 ES 6 或 JSX，这将看起来相当神奇。(对我来说还是有点。)

### ES 6 和 JSX 快速入门

我将快速分解一下代码:

> `import React from 'react';`

这将从库`'react'`中加载`React`对象。任何使用 JSX 语法的地方都需要它。

> `class OptionalExpirationDate extends React.Component {`

`React.Component`是大国反应的中坚力量。我们的类现在可以被渲染了。

> `render() { return <div className="optional-expiration-date">hello</div>; }`

所有的 React 组件都需要一个`render()`方法。它返回 JSX，这是一种 HTML-Javascript 混合语法。我们正在返回相当于`<div class="optional-expiration-date">hello</div>`的值

> `export default OptionalExpirationDate;`

这意味着当我们执行`import ClassName from 'OptionalExpirationDate.js';`时，返回的是`OptionalExpirationDate`类。还有其他方式[进口](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/import)[出口](https://developer.mozilla.org/en-US/docs/web/javascript/reference/statements/export)。

现在，在另一个初始化页面的文件中，我们可以有:

```
// boot.html
<div id="example-03"></div>

<script type="javascript">
import OptionalExpirationDate from 'OptionalExpirationDate.js';

let opt = <OptionalExpirationDate />;
ReactDOM.render(opt, document.getElementById('example-03'));
</script> 
```

> 参见 [jsfiddle](https://jsfiddle.net/kaisershahid/Ld4e1zuw/4/) (代码略有改动，可在 jsfiddle 中工作)

因此，当页面呈现时，您将在`#example-03`中看到`<div className="optional-expiration-date">hello</div>`。让我们扩展我们的组件:

```
// OptionalExpirationDate.js
import React from 'react';

class OptionalExpirationDate extends React.Component {
    render() {
        return <div className="optional-expiration-date">
            <input type="checkbox" name="enableExpiration" />
            <input type="text" name="expirationDate" disabled />
            <div className="errorMessage" style={{display: 'none', background: 'red', color: 'white'}}></div>
        </div>;
    }
}

export default OptionalExpirationDate; 
```

> 参见[js dild](https://jsfiddle.net/kaisershahid/Ld4e1zuw/7/)

它和我们最初的 HTML 几乎一样，除了 JSX 的一些关键东西:

1.  由于`class`是保留字，我们必须使用`className`
2.  样式应该是作为 JS 对象的键值对。没有引号，像`font-size`这样的属性就变成了`fontSize`
3.  `style`值用`{}`括起来——这意味着使用 JS 代码而不是文字值。这开启了一个充满可能性的世界

### 让我们的组件交互

到目前为止，一切都应该很简单。我们只关注输出 HTML。现在让我们做一些布线。**我们将介绍三个让我们陷入 React 世界的新东西，这部分会很长**(抱歉):

1.  [状态](https://reactjs.org/docs/state-and-lifecycle.html) (React 跟踪 UI 变化的方式)
2.  [析构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)(设置对象的局部变量)
3.  [箭头功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)(紧凑内嵌功能)

```
// OptionalExpirationDate.js
import React from 'react';

class OptionalExpirationDate extends React.Component {
    state = {inputChecked: false, errorMessage: null};

    toggleCheckbox() {
        this.setState({...this.state, inputChecked: !this.state.inputChecked});
        return this;
    }

    setErrorMessage(msg) {
        this.setState({...this.state, errorMessage: msg});
        return this;
    }

    render() {
        return <div className="optional-expiration-date">
            <input type="checkbox" name="enableExpiration" onClick={() => this.toggleCheckbox()} />
            <input type="text" name="expirationDate"
                disabled={this.state.inputChecked ? false : true} />
            <div className="errorMessage"
                style={{
                    display: this.state.errorMessage == null ? 'none' : 'block',
                    background: 'red', color: 'white'
                }}>{this.state.errorMessage}</div>
        </div>;
    }
} 
```

通读一下，应该还是有高层次意义的。看，所有的功能都与 HTML 紧密相关！你看到正在使用的选择器了吗？我们在这里直接引用了 HTML 元素吗？(没有也没有。)

这怎么可能呢？

> 参见[js dild](https://jsfiddle.net/kaisershahid/Ld4e1zuw/6/)

### 状态(`this.state`)

首先要说明的是**状态**。注意我们有`toggleCheckbox()`和`setErrorMessage()`都调用`setState({...})`。当这种情况发生时，React 知道重新处理这个组件(实际上是它的所有子组件)。**这基本上是反应/通量模式的核心**。

### 解构(ES 6)

第二个要说明的是**析构**。假设你有一个对象:

```
let map = {a: 1, b: 2}; 
```

如果我们想把它和另一个对象合并，在老的 JS 中有很多方法可以做到。我们将采用手动方法:

```
let mapNew = {a: map.a, b: map.b, c: 3}; 
```

对于析构，下面是等效的:

```
let mapNew = {...map, c: 3}; 
```

当`...map`在右侧时，它实质上就是`copy every key-value from map`。

另一种经常使用的析构方法如下:

```
let map = {a: 1, b: 2};
let mapNew = {...maps, c: 3, d: 4};
let {a, b, ...rest} = mapNew; 
```

最后一行相当于:

```
let a = mapNew.a, b = mapNew.b, rest = {c: mapNew.c, d: mapNew.d}; 
```

在左侧时，`...rest`表示`copy all the remaining key-value pairs into rest`。

使用析构的最后一种方法是将局部变量复制到键值对中:

```
let a = 1, b = 2, c = 3, d = 4;
let map = {a, b};
let mapNew = {...map, c, d};

// equivalent to
let map = {a: a, b: b};
let mapNew = {a: map.a, b: map.b, c: c, d: d}; 
```

#### 试试看

将以下内容复制并粘贴到您的浏览器开发控制台中:

```
let a = 1, b = 2;
let map = {a, b};
console.log('a, b to map', map);

let map2 = {...map, c: 3, d: 4};
console.log('map2 merged with map + {c:3,d:4}', map2);

let {c, d} = map2;
console.log('c, d extracted from map2', c, d); 
```

### 箭头功能(ES 6)

第三件要解释的事情是**箭头功能**。你在`onClick={() => this.toggleCheckbox()}`里看到的。

这个和`onClick={function() { this.toggleCheckbox(); }}`类似但不一样。使用`function() { }`语法，`this`实际上不绑定任何东西。要在这种情况下做到这一点，您实际上必须做到以下几点:

```
// in render()
const me = this;
return <div className="optional-expiration-date">
    <input type="checkbox" name="enableExpiration" onClick={function() {me.toggleCheckbox();}} />
    ...
</div>; 
```

对于箭头函数，`this`是封闭方法绑定到的对象。这是优势 1(老实说，也是最大的优势)。

如果您正在编写一行程序，`() => statement`执行语句并返回值。对于多个语句(或者如果您想更详细一些)，您应该编写`() => { return statement; }`。如果你有一个论点，你可以写`(arg) => statement`或者`arg => statement`。这是优势之二。

#### 试试看

将以下内容复制并粘贴到您的浏览器开发控制台中:

```
const f1 = () => console.log('called f1');
const f2 = (a, b) => console.log('called f2', a, b);
const f3 = (a, b) => {
    const c = a + b;
    console.log('called f3', a, '+', b, '=', c);
}

f1();
f2(1, 2);
f3(1, 2);

class Throwaway {
    constructor() {
        this.a = '#instance property a';
    }

    getFunc() {
        return () => this.a;
    }
}

let t = new Throwaway();
let callback = t.getFunc();
console.log('retrieving t.a from an arrow function callback', callback()); 
```

### 结论

我们最初的 HTML/jQuery 字段现在已经变成了一个由一个类封装的单元。代码逐行变长，但是看看发生了什么:

1.  我们不必编写/跟踪混乱的 CSS 选择器
2.  我们将 HTML 视为一个对象，它将功能与上下文直接联系起来
3.  一切都是一个逻辑单元

所以，少吃点代码汤。(至少在这个例子里。React 世界的其余部分并不完美。)

这是我们现在的关系:

```
OptionalExpirationDate
 └-> Checkbox
 └-> Textbox
 └-> ErrorMessage 
```

#### 解除状态上升

在我们的组件中发生的另一件事是，兄弟组件之间互不了解。父组件的任务是找出传递给子组件的内容。这也降低了复杂性。简单来说，这就是所谓的 [**提升状态**](https://reactjs.org/docs/lifting-state-up.html) 。理解这一点将使 React 代码更容易使用。

有了这个基本的理解，最后让我们向 React 示例添加验证。

* * *

## 嵌套组件

我们还没有使用过嵌套组件。事实上，`<lowercase ...>`形式的任何东西总是被当作最终的 HTML 输出。因此，让我们将错误消息框作为自己的组件。我们应该能够给它传递一个`message`，要么是一个字符串，要么是`null`——如果它为空，它应该被隐藏。

```
// in ErrorMessage.js
import React from 'react';

class ErrorMessage extends React.Component {
    render() {
        const {message} = this.props;
        const styles = {
            display: message == null ? 'none' : 'block',
            background:'red',
            color: 'white'
        };
        return <div className="errorMessage" style={styles}>{message}</div>
    }
}

export default ErrorMessage; 
```

什么是`this.props`？？？

### 属性(`this.props`)

我们已经看过 JSX 了。当`element`是一个组件时，它们被转换成对象`{key: value, key2: 'value', ...}`，该对象被设置在`React.Component`的`props`属性上。事实上，默认的构造函数是`construct(props)`。

在`ErrorMessage.render()`中，我们从`this.props`中提取`message`属性。

不像`this.state`，我们从来不直接改变`this.props`。可以把它看作是定义组件行为的基线规则，以及任何规则/值等。这应该传递给孩子们(后面会有更多的介绍)。

### 使用我们的新组件

我们将简单地导入`ErrorMessage`并将其替换为旧的`<div>`:

```
// OptionalExpirationDate.js
import React from 'react';
import ErrorMessage from './ErrorMessage';

class OptionalExpirationDate extends React.Component {
    state = {inputChecked: false, errorMessage: null};

    toggleCheckbox() {
        this.setState({...this.state, inputChecked: !this.state.inputChecked});
        return this;
    }

    setErrorMessage(msg) {
        this.setState({...this.state, errorMessage: msg});
        return this;
    }

    render() {
        return <div className="optional-expiration-date">
            <input type="checkbox" name="enableExpiration" onClick={() => this.toggleCheckbox()} />
            <input type="text" name="expirationDate"
                disabled={this.state.inputChecked ? false : true} />
            <ErrorMessage message={this.state.errorMessage} />
        </div>;
    }
} 
```

很漂亮，对吧？让我们做最后一点:添加验证。

### 与父组件通信(间接)

为此，我们将为 textbox 创建另一个组件，我们将期待两个属性:

1.  应该有一个回调函数，当出现错误时调用。它应该采用单个参数`message`
2.  `disabled`应为布尔型以启用或禁用该字段

```
// Textbox.js
import React from 'react';

class Textbox extends React.Component {
    validate(event) {
        const callback = this.props.validationCallback || (() => {});
        if (!event.currentTarget.value.match(/^\w+$/)) {
            callback('Invalid date: ' + event.currentTarget.value);
        }
    }

    render() {
        return <input type="text" name="expirationDate" disabled={this.props.disabled ? true : false}
            onBlur={(event) => this.validate(event)} />
    }
} 
```

现在，我们的主要组件将如下所示:

```
// OptionalExpirationDate.js
import React from 'react';
import ErrorMessage from './ErrorMessage';
import Textbox from './Textbox';

class OptionalExpirationDate extends React.Component {
    state = {inputChecked: false, errorMessage: null};

    toggleCheckbox() {
        this.setState({...this.state, inputChecked: !this.state.inputChecked});
        return this;
    }

    setErrorMessage(msg) {
        this.setState({...this.state, errorMessage: msg});
        return this;
    }

    render() {
        return <div className="optional-expiration-date">
            <input type="checkbox" name="enableExpiration" onClick={() => this.toggleCheckbox()} />
            <Textbox validationCallback={(message) => this.setErrorMessage(message)} disabled={!this.state.inputChecked} />
            <ErrorMessage message={this.state.errorMessage} />
        </div>;
    }
} 
```

> 参见[js dild](https://jsfiddle.net/kaisershahid/Ld4e1zuw/14/)

这里发生了什么？我们正在传递一个绑定到当前`OptionalExpirationDate`组件的内嵌函数`(message) => this.setErrorMessage(message)`。当`ExpirationDate`看到它的`validationCallback`属性的非空值时，它使用它来传达一个验证错误。

然而，*它不知道它在和什么交流！它所关心的只是有一次回调——接下来会发生什么与它无关。这就是我所说的与父母间接交流的意思。并且，它仍然不知道它的兄弟姐妹的状态。*

### 结论

现在我们有了一个类似于 jQuery 示例的 React 示例，但是我们仍然没有处理任何选择器，每个单独的字段仍然不需要担心它的任何兄弟。

退一步说，我们现在可以把这个字段放在任何我们想要的地方，它会自动工作！

```
const form = <form>
    <OptionalExpirationDate />
    <OptionalExpirationDate />
</form> 
```

太简单了。当然，我们现在需要能够控制字段名称，以便一个日期不会覆盖另一个日期。你认为需要做些什么？

**溶液扰流板**

1.  传入`expirationName`到`OptionalExpirationDate` : `<OptionalExpirationDate expirationName="newField" />`
2.  将名字传给`ExpirationDate` : `<ExpirationDate name={this.props.expirationName} ... />`
3.  在日期组件中，使用传入的名称或默认值:`<input name={this.props.name || 'expirationDate'} ... />`

> 参见[js 小提琴](https://jsfiddle.net/kaisershahid/Ltw5ksyb/)

## 包装完毕

希望这已经以一种易于理解的方式展示了 React 是什么/做什么的一些核心概念:

1.  创建一个数据流方向，这样每个组件只需要了解自己并设置其子组件
2.  轻松构建可重用和可定制的组件

### React 是如何做到这个神奇的？

我一直在猜测这一切是如何工作的，因为我不想让你在了解 React 是什么之前迷失在细节中。我仍然不打算公正地处理这个过程，但这里有一个简单的概述:

**首先是 JSX** 。这是 Javascript 的语言扩展。这还不是一个标准。那么它是如何工作的呢？

React 应用程序是使用 npm 或 yarn(或其他一些构建工具)编译的。编译过程的一部分包括一个名为 [babel](https://babeljs.io/) 的库，它扫描导入的文件并在需要的地方进行转换。如前所述，每当我们有 JSX，我们总是需要有`import React from 'react';`在我们的文件中。这是给巴别塔的信号，让他把这个文件当作有 JSX。所以`<SomeComponent prop1="whatever" ... />`被翻译成:

```
let someComponent = React.createElement(
    'tagName',
    {prop1: "whatever", ...},
    [...children...]
); 
```

现在，如果您开始研究其他 React 库，您会很快发现它们是用各种 JS 变体编写的。但是将这一切联系在一起的是[trans pilling](https://scotch.io/tutorials/javascript-transpilers-what-they-are-why-we-need-them)——将代码从一种语言(或变体)转换成另一种语言。这也使得浏览器上可以使用先进的 JS 语法。

**接下来是反应本身**。它如何连接到实际的 DOM？React 维护一个虚拟 DOM。您创建/注入的每个组件都成为这个虚拟 DOM 的一部分。当你调用`setState()`时，它只影响被调用的组件。这又会更新虚拟 DOM，然后在浏览器 DOM 中呈现新的 HTML。还记得上面的`ReactDOM.render()`吗？这是连接虚拟 DOM 和浏览器 DOM 的东西。

### 进一步阅读

如果你想要更深入的介绍，请查阅[一份完整的初学者指南来回应](https://dev.to/aspittel/a-complete-beginners-guide-to-react-2cl6)。

要在 npm 中设置 react，[请参见本教程](https://www.valentinog.com/blog/babel/)——注意，如果您以前没有使用过 node，这可能会令人望而生畏。所以，也许还可以看看[这篇关于 npm](https://www.sitepoint.com/beginners-guide-node-package-manager/) 的教程。

当然，如果你只是想玩玩，jsfiddle 是最快的方法。从上面的例子中选一个来快速开始跑步。

### 接下来

我们只是触及了表面。在下一期文章中，我们将更深入地讨论`props`、`state`、子组件和高阶组件(HOC)。在另一期文章中，我们将实际讨论流入和流出组件的数据。

希望你喜欢！请告诉我是否有我可以改进的地方，或者我是否完全误解了一个概念！点击“喜欢”按钮并订阅。
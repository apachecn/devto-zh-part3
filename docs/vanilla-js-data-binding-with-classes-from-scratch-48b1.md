# 从头开始用类进行普通的 JS 数据绑定

> 原文：<https://dev.to/proticm/vanilla-js-data-binding-with-classes-from-scratch-48b1>

# 概述

> 最初发布于[我的博客](https://devinduct.com/blogpost/15/vanilla-js-data-binding-with-classes-from-scratch)

字体世界，实际上是整个编程世界，充满了有用的框架和库，解决了我们每天面临的大量问题，这就是它们如此受欢迎的原因。

使用框架的主要好处是，我们不必重复我们在以前的项目中所做的工作，并且我们保持对我们正在进行的项目的业务逻辑的强烈关注。更不用说公司的成本降低了，因为开发商比建筑师便宜多了。发展的速度也是如此，这与成本直接相关...等等。许多其他的好处可以在这里说明，但是我们不会这样做，因为这篇文章有不同的观点。

不超越框架边界会产生一系列后果。最大的变化是我们不再是开发者(我们错过了很酷的东西),而变成了某个人为我们创造的工具的高级用户。这就是我们现在的情况，许多人认为如果他们知道 Angular、Vue 或 React，他们就是程序员。这是不正确的，因为在这种情况下，你所知道的只是如何设置和配置一个为你服务的工具。

这就是这篇文章的全部内容。它将涵盖一个许多人认为理所当然的话题，因为它在当今非常普遍，即数据绑定。在我所处的时代，数据绑定还没有像今天这样深度集成，有时我需要手动完成所有工作。

我们的简单数据绑定机制将包含以下类:

1.  `Binding` -代表绑定指令
2.  `Binder` -代表我们指令的解析器
3.  `TextBindingHandler` -表示文本数据绑定处理程序
4.  `ValueBindingHandler` -表示双向数据绑定处理程序

好了，让我们开始吧。

# HTML

考虑下面的 html:

```
<!DOCTYPE html>
<html>

<head>
    Vanilla JS Data Binding
</head>

<body>
    <main>
        <input placeholder="Enter name">
        <input placeholder="Enter surname">
        <br>
        <br>
        <span>Entered Person</span>
        <br>
        <span></span>
        <span></span>
    </main>
</body>
</html> 
```

现在，让我们说，每当一个`name`或`surname`输入域改变时，我们想要更新下面输入的人的信息。例如，对于 Vue，我们只需输入`v-model`和`v-text`指令，无需担心。但这不会引起你的兴趣吗？你不好奇它到底是怎么运作的吗？当我第一次看到数据绑定时，我确实被吸引住了。

让我们更新我们的 HTML 并使用我们的指令:

```
<!DOCTYPE html>
<html>

<head>
    Vanilla JS Data Binding
</head>

<body>
    <main>
        <input data-bind="value: name" placeholder="Enter name">
        <input data-bind="value: surname" placeholder="Enter surname">
        <br>
        <br>
        <span>Entered Person</span>
        <br>
        <span data-bind="text: name"></span>
        <span data-bind="text: surname"></span>
    </main>
</body>
</html> 
```

此时，它们不会做任何事情，因为我们还没有创建它们。让我们从代表`data-bind`属性的`Binding`类开始。

# JavaScript

为了使数据绑定成为可能，我们需要用我们选择的语言 JavaScript 来备份它。

> 请注意，这段代码是用下一代 JavaScript 编写的，请确保使用最新的浏览器

### 绑定类

`Binding`类是这样的:

```
class Binding {
    constructor(prop, handler, el) {
        this.prop = prop;
        this.handler = handler;
        this.el = el;
    }
    bind() {
        let bindingHandler = Binder.handlers[this.handler];
        bindingHandler.bind(this);
        Binder.subscribe(this.prop, () => {
            bindingHandler.react(this);
        });
    }
    setValue(value) {
        Binder.scope[this.prop] = value;
    }
    getValue() {
        return Binder.scope[this.prop];
    }
} 
```

我们的`Binding`类有三个属性和三个方法。`prop`属性将保存我们希望将元素绑定到的范围或视图模型的属性名。`handler`属性将保存我们在`Binder`类中定义的处理程序键(`value`或`text`)，而`el`属性将保存我们绑定的 HTML 元素。

方法`bind`具有所有的魔力。它基于提供的键获取处理程序，并触发其内部的`bind`方法。此外，它将绑定订阅到 scope 属性，并为其附加一个回调以供将来更新。

> 每个绑定处理程序必须有`bind`和`react`方法实现，我们将在本文后面看到。

方法`getValue`和`setValue`分别为我们检索和设置范围值。

### 装订器类

让我们转到下一个类实现，即`Binder`类:

```
class Binder {
    static setScope(scope) {
        this.scope = scope;
    }
    static redefine() {
        let keys = Object.keys(this.scope);
        keys.forEach((key) => {
            let value = this.scope[key];
            delete this.scope[key];
            Object.defineProperty(this.scope, key, {
                get() {
                    return value;
                },
                set(newValue) {
                    const shouldNotify = value != newValue;
                    value = newValue;
                    if (shouldNotify) {
                        Binder.notify(key);
                    };
                }
            })
        });
    }
    static subscribe(key, callback) {
        this.subscriptions.push({
            key: key,
            cb: callback
        });
    }
    static notify(key) {
        const subscriptions = this.subscriptions.filter(
            subscription => subscription.key == key
        );
        subscriptions.forEach(subscription => {
            subscription.cb();
        })
    }
}

// create some static properties
Binder.subscriptions = [];
Binder.scope = {};
Binder.handlers = {
    value: new ValueBindingHandler(),
    text: new TextBindingHandler()
} 
```

这个类将被我们所有的指令使用，因此，方法和属性被定义为静态的。

我们有`setScope`方法。此方法仅在应用程序启动时调用一次。它所做的只是设置类的 scope (viewmodel)属性。范围是我们希望将视图绑定到的对象。

另一个只调用一次的方法是`redefine`方法。这个方法在我们的程序中非常重要。它所做的是获取给定视图模型的每个属性，并将其重新定义为一个反应式属性。没有这一点，就不可能在我们的范围更新后更新 UI。UI 更新是通过`notify`方法完成的。此方法遍历特定范围属性的所有订阅，并执行附加到它的回调。

> 还记得附加回调的`Binding`类上的`bind`方法吗

最后，我们有一个为给定的`key/callback`对创建新订阅的`subscribe`方法。

> 一个稍微复杂的解决方案是将订阅存储在实际的范围属性上。这需要将它们转换成更复杂的可观测量，这意味着我们的`get`覆盖不会只返回原始值，但这是另一篇文章的主题。

### 处理程序类

在这些类中，我们指定每个指令在最初和范围更新后应该做什么。如前所述，我们必须实现`bind`和`react`方法。让我们从`ValueBindingHandler`开始，因为它是一个双向绑定，并且有额外的方法实现。类是这样的:

```
class ValueBindingHandler {
    bind(binding) {
        binding.el.addEventListener('input', () => {
            this.listener(binding);
        });
        this.react(binding);
    }
    react(binding) {
        binding.el.value = binding.getValue();
    }
    listener(binding) {
        let value = binding.el.value;
        binding.setValue(value);
    }
} 
```

双向数据绑定顾名思义。两个方向的绑定。这意味着当我们更新 scope 属性时，我们绑定的 HTML 元素也必须更新，反之亦然，当我们更新 HTML 元素时，它也必须更新 scope 属性。这种行为是通过事件侦听器实现的。在我们的特例中，使用了一个`input`处理程序。

最初，`bind`方法被调用，并且只在应用程序启动时调用一次。这是内部完成的，不必手动调用。在这个方法中，我们附加一个事件侦听器，并将 scope 属性的初始值设置为 HTML 元素(通过调用`this.react`)。

> 还记得返回范围属性值的`Binding`类上的`getValue`方法吗

每当我们更新页面上的`input`值时，就会执行`listener`方法，并将新输入的值设置为`scope`属性。

> 还记得设置范围属性值的`Binding`类上的`setValue`方法吗

另一方面，在每次范围属性改变时调用的`react`方法中，我们将新值设置回 HTML 元素。

> 例如，您可以在浏览器控制台中更新 scope 属性。打开它并执行这个:`Binder.scope.name = 'My Cool Name'`

我们示例中的最后一个类`TextBindingHandler`看起来像这样:

```
class TextBindingHandler {
    bind(binding) {
        this.react(binding);
    }
    react(binding) {
        binding.el.innerText = binding.getValue();
    }
} 
```

这门课非常简单。它有两个强制方法，`bind`和`react`，分别在应用程序初始化时和范围更新后调用。由于这是对文本属性的单向绑定，我们在这里所做的就是设置元素的`innerText`。

> 设置好页面后，继续尝试更新输入字段。这种改变应该实时更新范围文本。

### 应用程序启动

最终，我们需要一个代码将这些点连接在一起。一个应用程序初始化的例子如下:

```
Binder.setScope({
    name: 'John',
    surname: 'Doe'
});
Binder.redefine();

const els = document.querySelectorAll('[data-bind]');
els.forEach(el => {
    const expressionParts = el.getAttribute('data-bind').split(':');
    const bindingHandler = expressionParts[0].trim();
    const scopeKey = expressionParts[1].trim();
    const binding = new Binding(scopeKey, bindingHandler, el);
    binding.bind();
}); 
```

另外，不要忘记更新 HTML 元素并包含脚本:

```
<!DOCTYPE html>
<html>

<head>
    Vanilla JS Data Binding
</head>

<body>
    <main>
        <input data-bind="value: name" placeholder="Enter name">
        <input data-bind="value: surname" placeholder="Enter surname">
        <br>
        <br>
        <span>Entered Person</span>
        <br>
        <span data-bind="text: name"></span>
        <span data-bind="text: surname"></span>
    </main>
</body>

<script src="my-path/TextBindingHandler.js"></script>
<script src="my-path/ValueBindingHandler.js"></script>
<script src="my-path/Binder.js"></script>
<script src="my-path/Binding.js"></script>
<script src="my-path/App.js"></script>

</html> 
```

这个动作之后，一切都应该像魔咒一样起作用。

> 上面的例子只对给定视图模型的根属性有效。你的家庭作业将是更新代码，使之有可能与嵌套的对象和属性一起工作。另外，如果你感兴趣，可以考虑实现`unbind`机制。打一个`unbind`电话后，您的视图应该会再次空闲。

# 结论

如果您以前对此不感兴趣，希望现在您已经感兴趣了，我希望我已经设法为您介绍了绝大多数流行的数据绑定背后的机制。如果你自己想不出来，不要害羞，问问别人事情是怎么做的(但是之前一定要尝试一下)，不要忘记，没有愚蠢的问题，只有愚蠢的答案。

感谢您的阅读和快乐编码。
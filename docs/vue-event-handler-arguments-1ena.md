# Vue 事件处理程序参数

> 原文：<https://dev.to/therealdanvega/vue-event-handler-arguments-1ena>

> 本文最初发布于[https://www.danvega.dev/blog/2019/03/04/vue-event-arguments](https://www.danvega.dev/blog/2019/03/04/vue-event-arguments)

学习 JavaScript 和 Vue 的一项基本技能是监听事件并处理它们的能力。事件是对代码的通知，表明发生了有趣的事情，比如用户点击按钮或按回车键。Vue 让事件处理变得非常简单，并且在事件修改器方面给了我们一些很棒的特性。

## 事件处理程序项目

首先，您将通过运行以下命令来设置一个新的 Vue 项目:

```
vue create event-handlers 
```

创建此项目时，请随意接受默认值。你要做的第一件事是打开`App.vue`并删除`<template></template>`标签之间的默认内容。在空白页面中，您将添加两个按钮，我们将使用它们作为演示的基础。

```
<template>
  <div id="app">
    <a href="#" id="increase" class="btn">Increase</a>
    <a href="#" id="decrease" class="btn">Decrease</a>
  </div>
</template> 
```

为了给我们的按钮添加一些样式，添加下面的 css:

```
<style>
  body {
    margin: 10px;
  }
  a.btn {
    display: inline-block;
    margin: 10px;
    padding: 12px;
    font-size: 13px;
    font-weight: 700;
    background-color: rgb(63, 63, 219);
    color: white;
    text-decoration: none;
    text-transform: uppercase;
    border-radius: 4px;
  }
</style> 
```

## 倾听事件

按钮就位后，我们可以为每个按钮添加一个事件监听器。在普通的 JavaScript 中，您必须获得对元素的引用，然后添加一个事件侦听器。在 Vue 中，您可以使用`v-on`指令来监听 DOM 事件，并在它们被触发时运行一些 JavaScript。

`v-on`指令后跟一个冒号，然后是您想要监听的 DOM 事件。在本例中，您想要监听`click`事件，所以您的代码现在看起来像这样。

```
<template>
  <div id="app">
    <a href="#" id="increase" class="btn" v-on:click="">Increase</a>
    <a href="#" id="decrease" class="btn" v-on:click="">Decrease</a>
  </div>
</template> 
```

> v-on:指令有一个简写，即使用@符号，后跟事件名称。在这种情况下,`@click=""`与长格式做完全相同的事情。对于本演示的其余部分，我将使用长版本。

放在括号内的代码是当该事件被触发时将运行的代码。

## 方法事件处理程序

你需要做的第一件事是设置一些初始数据。在脚本中，block 创建一个名为 counter 的实例变量，并将其设置为零。

```
<script>
export default {
  name: "app",
  data() {
    return {
      counter: 0
    };
  }
};
</script> 
```

在模板中，您将添加一些文本，并使用数据绑定来显示计数器的值。

```
<template>
  <div id="app">
    <a href="#" id="increase" class="btn" v-on:click="">Increase</a>
    <a href="#" id="decrease" class="btn" v-on:click="">Decrease</a>
    <p>The button was clicked {{ counter }} times</p>
  </div>
</template> 
```

### 内联事件处理程序

既然您已经知道了如何声明一个事件处理程序，那么您需要编写一些在事件被触发时执行的代码。您可以将这段代码写在括号内(内联),也可以声明一个函数来处理它。对于基本操作，编写内联代码就可以了，这里你所要做的就是增加或减少变量 counter 的值。

```
<div id="app">
  <a href="#" id="increase" class="btn" v-on:click="counter += 1">
    Increase
  </a>
  <a href="#" id="decrease" class="btn" v-on:click="counter -= 1">
    Decrease
  </a>
  <p>The button was clicked {{ counter }} times</p>
</div> 
```

### 方法事件处理程序

你会很快发现大多数事件处理程序的逻辑更复杂，对于这些情况，我们可以调用一个方法。`v-on`指令将采用当事件被触发时将被调用的方法的名称。在下面的代码中，您将逻辑从内联移动到一个方法中。

```
<template>
  <div id="app">
    <a href="#" id="increase" class="btn" v-on:click="increase">Increase</a>
    <a href="#" id="decrease" class="btn" v-on:click="decrease">Decrease</a>
    <p>The button was clicked {{ counter }} times</p>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      counter: 0
    };
  },
  methods: {
    increase() {
      this.counter += 1;
    },
    decrease() {
      this.counter -= 1;
    }
  }
};
</script> 
```

程序的功能是一样的，但是现在你已经把它提取到一个程序中，如果需要的话，它可以包含更复杂的操作。

## 事件处理程序参数

虽然这个程序运行良好，但我们似乎可以简化它。所有的增加或减少方法只是改变变量计数器。您可以编写一个方法来处理这个方法的逻辑。就像 JavaScript 中的其他方法一样，你可以向它传递参数。这里您为两个按钮调用了`updateCounter()`,但是为增加传递了值 1，为减少传递了值-1。

```
<template>
  <div id="app">
    <a href="#" id="increase" class="btn" v-on:click="updateCounter(1)"
      >Increase</a
    >
    <a href="#" id="decrease" class="btn" v-on:click="updateCounter(-1)"
      >Decrease</a
    >
    <p>The button was clicked {{ counter }} times</p>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      counter: 0
    };
  },
  methods: {
    updateCounter(operand) {
      this.counter += operand;
    }
  }
};
</script> 
```

就像任何其他方法一样，您可以向该方法传递任何需要的参数。

### 隐含事件论元

看着这个方法我不禁觉得我们这里有点露骨。你知道增加按钮将使计数器增加 1，减少按钮将使计数器减少 1。如果是这样的话，为什么需要将变量传递给方法呢？

在普通 JavaScript 中，您可以访问原始的 DOM 事件。从那里，您可以确定事件的来源。在 Vue 中，如果方法没有参数，原始 DOM 事件被隐式传递。这意味着在我们的`updateCounter()`方法中，你可以声明一个名为 event 的参数(或者任何你想要的东西),事件将被传入。对于原始事件，我们可以使用`event.target.id`获得按钮的 id，并确定我们是增加还是减少计数器的值。

```
<template>
  <div id="app">
    <a href="#" id="increase" class="btn" v-on:click="updateCounter"
      >Increase</a
    >
    <a href="#" id="decrease" class="btn" v-on:click="updateCounter"
      >Decrease</a
    >
    <p>The button was clicked {{ counter }} times</p>
  </div>
</template>

<script>
export default {
  name: "app",
  data() {
    return {
      counter: 0
    };
  },
  methods: {
    updateCounter(event) {
      this.counter += event.target.id === "increase" ? 1 : -1;
    }
  }
};
</script> 
```

### 显式事件自变量

当您有需要传递给方法的参数，但还需要访问原始 DOM 事件时，会发生什么情况呢？在这种情况下，有一个特殊的变量`$event`可以传递。

```
<template>
  <div id="app">
    <a href="#" id="increase" class="btn" v-on:click="updateCounter(1,$event)"
      >Increase</a
    >
    <a href="#" id="decrease" class="btn" v-on:click="updateCounter(-1,$event)"
      >Decrease</a
    >
    <p>The button was clicked {{ counter }} times</p>
  </div>
</template> 
```

## 结论

我意识到大部分或者所有这些都是非常基础的，但是我发现很多人不知道隐式事件论点。这在很多情况下非常方便，所以知道如何访问原始 DOM 事件很有好处。如果你对这篇文章或任何与 Vue 相关的东西有任何问题，请让我知道，我会尽力回答他们，直到那时...

快乐编码

丹

*这篇文章最初是在 https://www.danvega.dev/blog 的[发表在我的博客上的。如果你觉得这篇文章有趣，请考虑](https://www.danvega.dev/blog)[订阅我的时事通讯](https://www.danvega.dev/signup/)或者在 [Twitter](http://twitter.com/therealdanvega) 上关注我。*
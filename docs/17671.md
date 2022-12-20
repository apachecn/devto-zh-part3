# Vue vs 普通 JavaScript -初学者指南

> 原文：<https://dev.to/michi/vue-vs-vanilla-javascript---beginners-guide-h35>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/vue-vs-vanilla-javascript-for-beginners)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

今天我们将编写一个非常简单的应用程序，并比较 VueJs 和普通 JavaScript 的实现。对于 Vue，我们将使用[单个文件组件](https://vuejs.org/v2/guide/single-file-components.html)，这基本上意味着每个组件都存在于自己的`.vue`文件中。

如果你更喜欢一个贯穿所有基本步骤的交互式教程，看看[通过观察](https://learning-by-vueing.netlify.app/vue/pre)来学习。

我们想要构建的应用程序有一个按钮，当你点击它时，它会自动计数。

让我们看看普通的 JavaScript 解决方案。

```
<button id="counter">0</button> 
```

Enter fullscreen mode Exit fullscreen mode

```
const counterBtn = document.getElementById('counter')

counterBtn.addEventListener('click', function incrementCounter() {
    const count = Number(counterBtn.innerText) + 1
    counterBtn.innerText = count
}) 
```

Enter fullscreen mode Exit fullscreen mode

好的，目前为止还不错。我们还可以在变量/状态中保存当前计数，增加它并更新 DOM。让我们看看如何实现这一点。

```
<button id="counter"></button> 
```

Enter fullscreen mode Exit fullscreen mode

```
const counterBtn = document.getElementById('counter')
let count = 0

function renderCount() {
    counterBtn.innerText = count
}

counterBtn.addEventListener('click', function incrementCounter() {
    count = count + 1
    renderCount()
})

// on init
renderCount() 
```

Enter fullscreen mode Exit fullscreen mode

这种方法的一个问题是，我们必须在初始化期间调用方法`renderCount`,以 100%确保计数与 DOM 保持同步。

如您所见，从一开始就有多种方法来设计您的应用程序。第一种是简单的，但是有点脏，不容易扩展。
第二种方式稍微干净一些，会带来一些开销。

重要的是要记住，DOM 不应该被用作数据存储。所以现在让我们坚持第二个版本。

让我们来看看 Vue 单个文件组件中的等效情况。由于我们使用单个文件组件，你需要使用 Vue Cli、Laravel Mix 等。将 vue 文件转换成普通的 Javascript。或者，你可以在[在线编辑器](https://codesandbox.io/s/vue)中尝试一下。

让我们假设我们有下面的包装组件`App.vue`

```
<template>
<div>
    <app-counter />
</div>
</template>

<script>
import AppCounter from './Counter'

export default {
    components: { AppCounter }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这是我们将花费大部分时间的部分。

```
<template>
<div>
    <button @click="counter++">{{ counter }} </button>
</div>
</template>

<script>
export default {
    data() {
        return {
            counter: 0,
        }
    },
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

在 Vue 里你永远找不到类似`counterBtn.innerText = count`的东西。用户界面与其状态/数据同步。让我重复一遍

> 用户界面与其状态/数据同步

对于我们简单的计数器来说，这可能没什么大不了的，但是想象一下有一个可以添加、编辑和删除记录的表。想象一下，你用 JavaScript 更新数组，然后想办法更新 HTML 表。你能重新加载整张桌子吗？在 HTML 中找到元素，然后编辑/删除它？当然，这将是混乱的。Vue 负责为我们处理整个 UI 部分。

但到目前为止，安装 Vue 只是为了这个有点大材小用。让我们看看添加更多功能后，我们的应用程序会如何发展。

我们希望我们的应用程序在计数器至少为 10 时显示文本`Good Job!`。

这将是常规的方法。

```
<button id="counter"></button>
<div id="inspirational-message" class="hidden">Good Job!</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
const counterBtn = document.getElementById('counter')
const inspirationalMessageEl = document.getElementById('inspirational-message')
let count = 0

function renderCount() {
    counterBtn.innerText = count

    if (count >= 10) {
        inspirationalMessageEl.classList.remove('hidden')
    }
}

counterBtn.addEventListener('click', function incrementCounter() {
    count = count + 1

    renderCount()
})

// on init
renderCount() 
```

Enter fullscreen mode Exit fullscreen mode

我们来添加这个 CSS 类:

```
.hidden {
    display: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

好的，所以我们必须添加一个新元素，它现在总是在 DOM 中，一个 CSS 类，和一个 if 条件。让我们看看我们的代码库是如何在 Vue 组件中成长的。

```
<template>
<div>
    <button @click="counter++">{{ counter }} </button>
    <div v-if="counter >= 10">Good Job!</div>
</div>
</template>

<script>
export default {
    data() {
        return {
            counter: 0,
        }
    },
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

哇，这太简单了！我们用一行代码做了所有的事情。如果我们检查 DOM，如果计数器小于 10，甚至没有隐藏的 div。这是因为 Vue 使用一个虚拟 DOM，因此可以只将必要的 HTML 传送到实际的 DOM。

但是现在，我们价值数百万美元的应用程序的项目经理找到我们，说他们也想要一个减量按钮。让我们看看谁会因为实现这个而遭受更大的损失？为了制作一个减量按钮，我们必须从增量按钮标签中删除当前计数，并将其添加到增量和减量按钮之间。

让我们看看 JavaScript 实现

```
<button id="increment-counter">+</button>
<span id="counter"></span>
<button id="decrement-counter">-</button>
<div id="inspirational-message" class="hidden">Good Job!</div> 
```

Enter fullscreen mode Exit fullscreen mode

```
const counterEl = document.getElementById('counter')
const incrementCounterEl = document.getElementById('increment-counter')
const decrementCounterEl = document.getElementById('decrement-counter')
const inspirationalMessageEl = document.getElementById('inspirational-message')
let count = 0

function renderCount() {
    counterEl.innerText = count

    const forceToggle = count < 10
    inspirationalMessageEl.classList.toggle('hidden', forceToggle)
}

incrementCounterEl.addEventListener('click', function incrementCounter() {
    count = count + 1
    renderCount()
})

decrementCounterEl.addEventListener('click', function decrementCounter() {
    count = count - 1
    renderCount()
})

// on init
renderCount() 
```

Enter fullscreen mode Exit fullscreen mode

```
.hidden {
    display: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

嗯，对于一个简单的减量按钮，我们做了很多改变...

这是 Vue
中的全部内容

```
<template>
<div>
    <button @click="counter--">-</button>
    {{ counter }}
    <button @click="counter++">+</button>
    <div v-if="counter >= 10">Good Job!</div>
</div>
</template>

<script>
export default {
    data() {
        return {
            counter: 0,
        }
    },
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

两行！那只有两行代码！

好吧，公平地说，普通的 JavaScript 已经失控了。所以在继续之前，让我们先重构它，毕竟我并不是想贬低它。

```
 class Counter {

    constructor() {
        this.count = 0

        this.cacheDOM()
        this.bindEvents()
        this.render()
    }

    cacheDOM() {
        this.counterEl = document.getElementById('counter')
        this.incrementCounterEl = document.getElementById('increment-counter')
        this.decrementCounterEl = document.getElementById('decrement-counter')
        this.inspirationalMessageEl = document.getElementById('inspirational-message')
    }

    bindEvents() {
        this.incrementCounterEl.addEventListener('click', () => this.countUp(1))
        this.decrementCounterEl.addEventListener('click', () => this.countUp(-1))
    }

    render() {
        this.counterEl.innerText = this.count

        const forceToggle = this.count < 10
        this.inspirationalMessageEl.classList.toggle('hidden', forceToggle)
    }

    countUp(value) {
        this.count += value
        this.render()
    }

}
new Counter() 
```

Enter fullscreen mode Exit fullscreen mode

那好多了！现在项目经理又来找我们了。这一次，他要求根据计数的值显示不同的励志信息。
规格如下:

```
< 10 -> Go on with it
10-15 -> 頑張って
16 - 25 -> Sauba!
25 - 50 -> Good Job! 
```

Enter fullscreen mode Exit fullscreen mode

你不能低于零度或高于 50 度。

在这一点上，用普通的 JavaScript 实现它有很多方法，很难从中选择一种...这个怎么样？

```
<button id="increment-counter">+</button>
<span id="counter"></span>
<button id="decrement-counter">-</button>
<div id="inspirational-message"></div> 
```

Enter fullscreen mode Exit fullscreen mode

```
 class Counter {

    constructor() {
        this.count = 0
        this.messages = [
            { start: 0, end: 9, message: 'Go on with it!' },
            { start: 10, end: 15, message: '頑張って!' },
            { start: 16, end: 25, message: 'Sauba' },
            { start: 26, end: 50, message: 'Good Job' },
        ]

        this.cacheDOM()
        this.bindEvents()
        this.render()
    }

    cacheDOM() {
        this.counterEl = document.getElementById('counter')
        this.incrementCounterEl = document.getElementById('increment-counter')
        this.decrementCounterEl = document.getElementById('decrement-counter')
        this.inspirationalMessageEl = document.getElementById('inspirational-message')
    }

    bindEvents() {
        this.incrementCounterEl.addEventListener('click', () => this.countUp(1))
        this.decrementCounterEl.addEventListener('click', () => this.countUp(-1))
    }

    render() {
        this.counterEl.innerText = this.count

        const { message } = this.messages.find(({start, end}) => this.count >= start && this.count <= end)
        this.inspirationalMessageEl.innerText = message
    }

    countUp(value) {
        const newCount = this.count + value
        if (newCount < 0 || newCount > 50) return
        this.count = newCount
        this.render()
    }

}
new Counter() 
```

Enter fullscreen mode Exit fullscreen mode

这个应该可以了。我们重构的 JavaScript 现在更容易扩展。我们不得不改变`constructor`、`render`方法和`count`方法。让我们看看 Vue 的实现。

```
<template>
<div>
    <button @click="counter > 0 && counter--">-</button>
    {{ counter }}
    <button @click="counter < 50 && counter++">+</button>
    <div>{{ message }}</div>
</div>
</template>

<script>
export default {
    data() {
        return {
            counter: 0,
            messages: [
                { start: 0, end: 9, message: 'Go on with it!' },
                { start: 10, end: 15, message: '頑張って!' },
                { start: 16, end: 25, message: 'Sauba' },
                { start: 26, end: 50, message: 'Good Job' },
            ],
        }
    },
    computed: {
        message() {
            return this.messages
                .find(({start, end}) => this.counter >= start && this.counter <= end)
                .message
        }
    }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

在普通的 JavaScript 实现中，我们必须扩展我们的 render 方法。Vue 的[计算字段](https://vuejs.org/v2/guide/computed.html)有一个更加优雅的解决方案。
一个计算字段获取现有数据，运行同步方法，在我们的例子中是`message()`，缓存它并使它可用，就像它是真实的`data`。

我们也可以将递减和递增提取到一个方法中。

```
<template>
<div>
    <button @click="decrement">-</button>
    {{ counter }}
    <button @click="increment">+</button>
    <div>{{ message }}</div>
</div>
</template>

<script>
export default {
    data() {
        return {
            counter: 0,
            messages: [
                { start: 0, end: 9, message: 'Go on with it!' },
                { start: 10, end: 15, message: '頑張って!' },
                { start: 16, end: 25, message: 'Sauba' },
                { start: 26, end: 50, message: 'Good Job' },
            ],
        }
    },
    computed: {
        message() {
            return this.messages
                .find(({start, end}) => this.counter >= start && this.counter <= end)
                .message
        }
    },
    methods: {
        decrement() {
            if (this.counter > 0) this.counter--
        },
        increment() {
            if (this.counter < 50) this.counter++
        },
    }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

从这两个实现来看，两者在这一点上都是可以理解的。那就好！不过，我们在普通的 JavaScript 实现中遇到了一些问题。从一开始，我们就必须决定实现计数器的最佳方式。在一些规范变更之后，我们很早就不得不将其重构为模块化结构，以保持代码的可读性。总的来说，做出必要的改变更加困难。Vue 的好处是每样东西都有它的位置。

现在我们正准备释放我们的计数器，突然 pm 敲我们的门，告诉我们一个页面上可以有多个计数器。相当简单的事情吧，只是复制一些 HTML。但是等等...我们一直用身份证。这意味着页面上只能有一个计数器...幸运的是，我们模块化了我们的代码，所以我们只需要对它做一些小的改动。我们来看看实现。

```
<div class="counter-wrapper" id="counter1">
    <button class="increment-counter">+</button>
    <span class="counter"></span>
    <button class="decrement-counter">-</button>
    <div class="inspirational-message"></div>
</div>
<div class="counter-wrapper" id="counter2">
    <button class="increment-counter">+</button>
    <span class="counter"></span>
    <button class="decrement-counter">-</button>
    <div class="inspirational-message"></div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

我们必须去掉所有的 id，用类来代替它们。

```
 class Counter {
    constructor(wrapperEl) {
        this.count = 0
        this.messages = [
            { start: 0, end: 9, message: 'Go on with it!' },
            { start: 10, end: 15, message: '頑張って!' },
            { start: 16, end: 25, message: 'Sauba' },
            { start: 26, end: 50, message: 'Good Job' },
        ]

        this.cacheDOM(wrapperEl)
        this.bindEvents()
        this.render()
    }

    cacheDOM(wrapperEl) {
        this.wrapperEl = wrapperEl
        this.counterEl = this.wrapperEl.querySelector('.counter')
        this.incrementCounterEl = this.wrapperEl.querySelector('.increment-counter')
        this.decrementCounterEl = this.wrapperEl.querySelector('.decrement-counter')
        this.inspirationalMessageEl = this.wrapperEl.querySelector('.inspirational-message')
    }

    bindEvents() {
        this.incrementCounterEl.addEventListener('click', () => this.countUp(1))
        this.decrementCounterEl.addEventListener('click', () => this.countUp(-1))
    }

    render() {
        this.counterEl.innerText = this.count

        const { message } = this.messages.find(({start, end}) => this.count >= start && this.count <= end)
        this.inspirationalMessageEl.innerText = message
    }

    countUp(value) {
        const newCount = this.count + value
        if (newCount < 0 || newCount > 50) return
        this.count = newCount
        this.render()
    }

}
new Counter(document.getElementById('counter1'))
new Counter(document.getElementById('counter2')) 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看 Vue 的实现。实际上，我们需要改变的只是我们的`App.vue`

```
<template>
<div>
    <app-counter />
    <app-counter />
</div>
</template>

<script>
import AppCounter from './Counter'

export default {
    components: { AppCounter }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

对，就是这样！我们只需要复制粘贴`<app-counter />`。vue 组件内的状态只能在该组件内访问。

## 结论

在这篇文章中，我想展示的是 Vue 的可读性和可扩展性。比较普通 JavaScript 和 Vue 解决方案的每个步骤。在所有情况下，Vue 解决方案需要的更改要少得多。固执己见的 Vue 迫使你进入一个清晰的结构。
也请花点时间比较一下最终结果。在你看来，哪一个可读性更强，因此更容易维护？

最后，您可以看到向我们的应用程序添加另一个计数器组件是多么容易。这正是 Vue 的亮点，它有着令人惊叹的组件设计。普通的 JavaScript 解决方案在可读性和可扩展性方面将远远落后。但那是另一集的事了；)我们只是勉强触及了 Vue 的皮毛。

* * *

如果这篇文章对你有所帮助，我有更多关于简化编写软件的技巧[在这里](https://michaelzanggl.gumroad.com/l/intent-driven-development)。
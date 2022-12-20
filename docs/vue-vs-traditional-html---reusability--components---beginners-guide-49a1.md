# Vue 与传统 HTML -可重用性和组件-初学者指南

> 原文：<https://dev.to/michi/vue-vs-traditional-html---reusability--components---beginners-guide-49a1>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/vue-vs-traditional-html)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

如果你想跟进，我推荐你使用 [codesandbox](https://codesandbox.com) 。

在 HTML 的世界里，假设我们想要创建一个由标题和文本组成的面板。你可以创建这样的东西

```
<div class="panel">
    <div class="panel__header">Title</div>
    <div class="panel__body">
        Lorem ipsum dolor sit amet consectetur adipisicing elit. Accusantium, sit!
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

从这里，您可以将 CSS 和 JavaScript 应用于这些类。然后，您可以随时重用这个 HTML。多亏了这些类，它才变得可重用。这就是像 bootstrap 这样的 CSS 框架多年来的工作方式。

让我们看看 Vue 如何处理可重用性:

第一个区别是我们必须为面板创建一个基类，而且是在一个组件中。

所以让我们创建组件`Panel.vue`

```
<template>
<div>
    <div class="header">Title</div>
    <div class="body">
        Lorem ipsum dolor sit amet consectetur adipisicing elit. Accusantium, sit!
    </div>
</div>
</template>

<script>
export default {

}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

请注意我们是如何删除一些类的，因为我们的 CSS 将作用于这个组件，很明显`header`指的是面板标题。

现在，不用一遍又一遍地重复这个 HTML 代码块，您可以直接将组件导入到您需要的任何地方。

让我们给组件`App.vue`
添加两个面板

```
<template>
<div>
    <Panel />
    <Panel />
</div>
</template>

<script>
import Panel from './Panel.vue'

export default {
    components: { Panel },
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这种**的关注点分离**非常好，因为我们不再使用各种嵌套的`div`容器，而是简单地使用`Panel`来结束，使得我们的模板非常容易理解。
但是等等！像这样，标题和正文永远不变。没错，所以我们需要的是让这些属性动态化。

为此，我们必须让父组件(App.vue)将标题和正文传递给子组件(Panel.vue)。子组件定义了它接受什么样的所谓道具。

### 道具

`Panel.vue`

```
<template>
<div>
    <div class="header">{{ title }}</div>
    <div class="body">{{ body }}</div>
</div>
</template>

<script>
export default {
    props: {
        title: {
            type: String,
            required: true,
        },
        body: String,
    }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

我们的组件接受两个道具。必须是字符串且必需的`title`，以及也是字符串但不一定必需的主体。

并且`App.vue`现在可以把道具传给面板了。

```
<template>
<div>
   <Panel title="Lorem Ipsum" body="Lorem ipsum dolor sit amet" />
   <Panel title="Something else" />
</div>
</template>

<script>
import Panel from './Panel.vue'

export default {
    components: { Panel },
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

道具非常类似于普通的 HTML 属性。
看看下面的元素

```
<input type="submit" value="Submit" /> 
```

Enter fullscreen mode Exit fullscreen mode

`input`元素接受属性`type`和`value`，以及许多其他属性。它理解并可以使用这些，因为它们是在`input`元素本身上定义的。
如果你要写`<input color="primary" />`，它会简单地忽略属性`color`，因为它在`input`上不存在。

* * *

现在有些面板特别重要，需要突出它们的背景。在 HTML 中，您现在可以向面板添加一个修饰符类，并对其进行样式化。

让我们添加类`panel--primary`。

```
<div class="panel panel--primary">
    <div class="panel__header">Title</div>
    <div class="panel__body">
        Lorem ipsum dolor sit amet consectetur adipisicing elit. Accusantium, sit!
    </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

CSS

```
.panel.panel--primary .panel__header {
    background-color: #369;
    color: #fff;
} 
```

Enter fullscreen mode Exit fullscreen mode

在 Vue 中，这只会成为另一个道具。

`Panel.vue`

```
<template>
<div :class="{primary: isPrimary}">
    <div class="header">{{ title }}</div>
    <div class="body">{{ body }}</div>
</div>
</template>

<script>
export default {
    props: {
        title: "String,"
        body: String,
        isPrimary: {
            type: Boolean,
            default: false,
        },
    }
}
</script>

<style scoped>
    .primary {
        background-color: #369; /* you might as well have a global CSS rule for the background color */
    }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

我们将`isPrimary`道具添加到我们的道具列表中。请注意我们是如何将它默认为 false 的。多方便啊。现在，当我们真正想要一个主面板时，我们只需要通过`isPrimary`道具。

我们还将类`primary`添加到带有`:class="{primary: isPrimary}"`的根元素的类列表中。如果你对这种语法感到困惑，一定要看看[我以前在 Vue](https://dev.to/mzanggl/css---vue-vs-vanilla-javascript---beginners-guide-42le) 中关于 CSS 的文章。

回到`App.vue`中，我们可以简单地添加`isPrimary`属性，就像您添加像`selected`或`checked`这样的 HTML 属性一样。

```
<template>
<div>
   <Panel isPrimary title="Lorem Ipsum" body="Lorem ipsum dolor sit amet" />
   <Panel title="Something else" body="Lorem ipsum dolor sit amet" />
</div>
</template>

<script>
import Panel from './Panel.vue'

export default {
    components: { Panel },
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

### 传递数据作为道具

到目前为止，我们只把字符串传递给了这个孩子。但是当我们必须传递任何其他数据时会发生什么呢？

回到`App.vue`让我们将标题和正文定义为实际数据，并尝试将其传递给孩子。

```
<template>
<div>
   <Panel isPrimary title="title" body="body" />
</div>
</template>

<script>
import Panel from './Panel.vue'

export default {
    components: { Panel },
    data() {
        return {
            title: 'Lorem Ipsum',
            body: 'Lorem ipsum dolor sit amet',
        }
    }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

上面的**会不会**起作用。它实际上会传递字符串`title`和`body`，而不是变量的内容。为了解决这个问题，我们必须给道具加上前缀。为此，我们只需改变`App.vue`的`<template>`部分。

```
<template>
<div>
   <Panel isPrimary v-bind:title="title" v-bind:body="body" />
</div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

你可以并且我推荐你把上面的缩写成

```
<template>
<div>
   <Panel :title="title" :body="body" />
</div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

事实上`v-bind`允许任何 JavaScript 表达式。

```
<template>
<div>
   <Panel :title="title.toUpperCase() + ', ' + body.substr(0, 20)" />
</div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

此外，如果你想传递一个数字，布尔值，数组或对象，你也必须通过表达式。

```
<template>
<div>
   <Panel 
       :someNumber="1"
       :someBoolean="false"
       booleanThatEvaluatesToTrue
       :array="[1, 2, 3]"
       :object="{ key: 'value' }"
   />
</div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

请注意，您不仅仅是出于可重用性的原因而创建新组件。每当一个组件变得太复杂或者你意识到它在做不止一件事的时候，考虑把它拆分成多个组件。它有助于组织代码。

想象一下，我们的面板标题变得更加复杂，我们想把它拆分成自己的组件。

`Panel.vue`

```
<template>
<div>
    <div class="header">
        <PanelHeader :title="title" :isPrimary="isPrimary"/>
    </div>
    <div class="body">{{ body }}</div>
</div>
</template>

<script>
import PanelHeader from './PanelHeader'

export default {
    components: { PanelHeader },
    props: {
        title: String,
        body: String,
        isPrimary: {
            type: Boolean,
            default: false,
        },
    }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

`PanelHeader.vue`

```
<template>
<div :class="{ primary: isPrimary }">
    {{ title }}
</div>
</template>

<script>
export default {
    props: {
        title: String,
        isPrimary: {
            type: Boolean,
            default: false,
        },
    }
}
</script>

<style scoped>
.primary {
    background-color: #369;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

`Panel.vue`仍然从`App.vue`那里得到道具`title`和`isPrimary`。然而，它并没有真正对它们做任何事情。它只是将道具进一步向下传递给`PanelHeader.vue`。

可以把`<PanelHeader :title="title" :isPrimary="isPrimary"/>`缩短为`<PanelHeader v-bind="{ title, isPrimary }" />`。

请注意`App.vue`不知道也不在乎面板头变成了自己的组件。

### 插槽

道具很棒，但是如果我们想要的不仅仅是面板中的基本文本呢？如果我们想要一些具有特定样式和功能的 HTML 呢？在这种情况下，我们有插槽。

我们不再需要`body`道具，所以让我们移除它。对于槽点，我们再加上`<slot name="body" />`。

`Panel.vue`

```
<template>
<div>
    <div class="header">{{ title }}</div>
    <div class="body">
        <slot name="body" />
    </div>
</div>
</template>

<script>
export default {
    props: {
        title: String,
    }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

基本上，在模板中，我们所做的就是用`<slot name="body" />`替换`{{ body }}`。

在`App.vue`中，我们现在可以在属性为`slot="body"`的元素中添加按钮。

```
<template>
<div>
    <Panel title="Lorem Ipsum" body="Lorem ipsum dolor sit amet">
        <div slot="body">
            <button @click="scream">Scream</button>
        </div>
    </Panel>
    <Panel title="Something else" body="Lorem ipsum dolor sit amet" />
</div>
</template>

<script>
import Panel from './Panel.vue'

export default {
    components: { Panel },
    methods: {
        scream() {
            alert('AAAAH')
        },
    },
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

将会发生的是来自`App.vue`的`<div slot="body">`将被放置在来自`Panel.vue`的`<slot name="body" />`中。

### 事件

因为我们把`title`作为道具传递下去，所以我们不能在面板中更新它。如果我们想更新标题，我们必须从子对象到父对象触发一个事件。

> 道具下降，事件上升

为此，让我们选择一个更有意义的例子。

`AwesomeCounter.vue`

```
<template>
<div>
    <button @click="increment">{{ awesomeCount }}</button>
</div>
</template>

<script>
export default {
    props: {
        awesomeCount: Number
    },
    methods: {
        increment() {
            this.$emit('update:awesomeCount', this.awesomeCount + 1)
        },
    },
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的计数器，当我们按下按钮时，它会将事件`update:awesomeCount`发送到父节点，并传递增加的值。

在下面的代码片段中，你可以看到我们可以使用`@click`监听事件`update:awesomeCount`，就像我们监听普通事件`click`一样。在该功能中，我们可以使用`$event`访问新的计数。

`App.vue`

```
<template>
<div>
    <AwesomeCounter :awesomeCount="count" @update:awesomeCount="count = $event"/>
</div>
</template>

<script>
import AwesomeCounter from './AwesomeCounter'

export default {
    components: { AwesomeCounter },
    data() {
        return {
            count: 10,
        }
    }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

事实上，这是一个非常常见的场景，您可以将上面的模板简化为

```
<template>
<div>
    <Counter :awesomeCount.sync="count"/>
</div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

为此，事件必须有名称`update:{name of prop}`。

事件不仅用于更新来自子级的数据。你可以做任何你想做的事情，比如调用 ajax。

### 开发工具

如果你在理解这里解释的一些概念上有问题，需要帮助调试或者只是想升级你的 vue 游戏，请查看 [vue devtools](https://github.com/vuejs/vue-devtools) 。你将能够看到你的组件，以及它们是如何在一个像树视图的 DOM 中连接的，它们拥有哪些数据和道具，什么事件被触发等等。你甚至可以操纵数据！

### 备注

*   你不必在一个对象中定义道具。你也可以在数组`props: ['title', 'body']`中定义它们
*   我们看了一些定义道具的需求，比如`type`、`default`和`required`。这里有更多的。
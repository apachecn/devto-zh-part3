# 面向后端开发人员的 Vue.js

> 原文：<https://dev.to/kaperskyguru/vue-js-for-backend-developers-4ccd>

继我最近关于 Vue.js 的文章(为什么作为后端开发人员学习 Vue.js)之后，我决定为后端开发人员编写一份关于 vue . js 学习内容的完整列表和教程。作为一名后端开发人员，我一直在试图找出 Vue.js 中需要学习的部分和具体内容，而不是试图把 Vue.js 提供的所有东西都包起来，因为我不是前端专业的。

如果你和我有同样的经历，那么这篇文章是给你的，因为我仔细挑选了你作为后端开发人员需要在 Vue.js 中学习的具体内容。

### 什么是 VUE。射流研究…

让我们从定义和了解 Vue.js 的含义以及它与其他框架的不同之处开始。

#### VUE。JS:是一个渐进式的 JavaScript 框架，专注于构建用户界面。

1.  它仅适用于视图层
2.  增量适应性。
3.  易于集成到其他项目或库中。
4.  能够为高级水疗中心供电

### 列举在 VUE 要学的东西。后端开发人员的 JS

1.  反应界面
2.  声明性渲染
3.  数据绑定
4.  指令
5.  模板逻辑
6.  组件和嵌套
7.  事件处理
8.  计算属性
9.  苏维埃社会主义共和国
10.  处理形式
11.  可重用性和组合
12.  测试
13.  部署

**反应界面/系统:【VUEJS 的一个优点是反应系统，如果一个数据被改变，它触发页面的更新以反映这种变化。有了 VUEJS 的这一特性，功能齐全的后端开发人员可以轻松地创建实时和反应式的仪表板，以及其他可以用 VUEJS 完成的事情。[点击这里了解更多信息](https://vuejs.org/v2/guide/reactivity.html#ad)。**

**声明式渲染:**是编写代码的过程，意图隐藏实现细节，关注结果。在渲染下，你应该查找像[条件渲染](https://vuejs.org/v2/guide/conditional.html)和[列表渲染](https://vuejs.org/v2/guide/list.html)这样的东西，它们分别描述了条件语句和迭代语句的使用。

**数据绑定:**是在应用 UI 和业务逻辑之间建立连接的过程。Vue 强制组件之间的单向数据流。这使得在重要的应用程序中更容易推理数据流。

[在这里阅读更多信息](https://vuejs.org/v2/guide/class-and-style.html)

```
<div v-bind:class="{ active: isActive }"></div> 
```

**指令:**本质上，指令是标记中的一些特殊标记，告诉库对 DOM 元素做些什么。在 Vue.js 中，directive 的概念比 Angular 中的简单得多。Vue.js 指令只能以带前缀的 HTML 属性的形式出现。指令是带有前缀`v-`的特殊属性。

[在这里阅读更多信息](https://vuejs.org/v2/guide/syntax.html#Directives)

```
<p v-if="seen">Now you see me</p> 
```

模板逻辑: VUEJS 允许在我们的组件中包含条件语句和迭代语句。

[点击此处了解更多关于组件的信息](https://vuejs.org/v2/guide/components.html)

**组件:**是 Vue.js 最强大的特性之一，它们帮助你扩展基本的 HTML 元素来封装可重用的代码。从高层次来看，组件是自定义元素，Vue.js 编译器会将指定的行为附加到这些元素上。

[点击此处了解更多关于组件的信息](https://vuejs.org/v2/guide/components.html)

#### 组件的用途

1.  他们为大型项目提供组织和封装
2.  可重用代码
3.  可以分为。Vue 文件

```
//Define a new component called button-counter

Vue.component('button-counter', {

  data: function () {

    return {

      count: 0

    }

  },

  template: '<button
v-on:click="count++">You clicked me {{ count }}
times.</button>'

}) 
```

**事件处理:** `v-on`是添加到 DOM 元素中的属性，用来监听 Vue.js 中的事件，学习如何发出不同的事件，如何用事件处理程序操纵模板。

[在这里阅读更多信息](https://vuejs.org/v2/guide/events.html#ad)

```
<div
id="example-1">

  <button v-on:click="counter += 1">Add 1</button> 
  <p>The
button above has been clicked {{ counter }} times.</p> 
</div> 
```

**计算属性:**计算属性本质上是用 getter/setter 函数定义的属性。您可以像使用普通属性一样使用计算属性，但是当您访问它时，您将获得 getter 函数返回的值；当您更改它的值时，您会触发 setter 函数，将新值作为它的参数传入。

在 Vue 中，我们使用数据来跟踪我们想要反应的特定属性的变化。计算属性允许我们定义一个与数据使用方式相同的属性，但也可以有一些基于其依赖关系缓存的自定义逻辑。

[在这里阅读更多信息](https://vuejs.org/v2/guide/computed.html#ad)

```
 var vm = new Vue({

  el: '#example',

  data: {

    message: 'Hello'

  },

  computed: {

    // a computed getter

    reversedMessage: function () {

      // `this` points to the vm instance

      return this.message.split('').reverse().join('')

    }

  }

}) 
```

**SSR(服务器端呈现):**是一种流行的技术，用于在服务器上呈现一个普通的客户端单页面应用程序(SPA)，然后将一个完全呈现的页面发送给客户端。默认情况下，Vue 组件在浏览器中产生并操作 DOM 作为输出。但是，也可以在服务器上将相同的组件渲染成 HTML 字符串，直接发送到浏览器，最后在客户端将静态标记“水合”成完全交互的 app。

点击此处了解更多信息。

我将在本期的[上发布一个关于 SSR 的实用方法。立即关注，以便在发布时收到提醒。](https://medium.com/backenders-club)

**处理表单:**处理表单请求和响应对于整个 web 和应用程序开发非常关键，因此即使作为后端开发人员，也有必要学习如何使用 Vue.js 中的最佳实践来完成。

[在这里阅读更多信息](https://vuejs.org/v2/guide/forms.html)

```
<input v-model="message" placeholder="edit me">

<p>Message is: {{ message }}</p> 
```

**可重用性和组合:**你应该学习像 mixins 这样的东西，以便灵活地为 Vue 组件分配可重用的功能。也看看像渲染功能和 JSX，插件和过滤器。

[点击此处了解更多信息](https://vuejs.org/v2/guide/mixins.html#ad)

测试: Vue-CLI 内置了 Jest 和 Mocha 单元测试选项，开箱即用。用这些工具稍微研究一下单元测试可能是值得的。

[点击此处了解更多信息](https://vuejs.org/v2/guide/unit-testing.html#ad)

**部署:**作为后端开发人员，如果你的公司没有独立的运营团队，部署可能是你的事情之一，所以深入研究 Vue.js 部署过程将是一个很好的节省。

[点击此处了解更多信息](https://vuejs.org/v2/guide/deployment.html#ad)

Vue.js 提供了更多现成的东西，但在我看来，我觉得这些是后端开发人员应该更多关注的事情。

#### 附加工具和插件

1.  **VUE-路由器:**深度集成 VUEJS 核心的官方路由器。
2.  VUE 资源:处理网络请求。
3.  **VUE 异步数据:**异步数据加载。
4.  **VUE 验证器**:表单验证插件
5.  **vista-devtools:**chrome devtools extension

#### 安装 VUEJS

实现 VUEJS 有多种方法:

1.  在 HTML 文件中包含脚本标记
2.  使用 NPM 安装
3.  将 VUE-CLI 工具与 webpack 一起使用
4.  使用 Bower 客户端软件包管理器进行安装。

#### 使用 VUE-CLI 工具

使用 Vue 命令行工具安装 Vue.js 是最简单的，也是我最喜欢的安装 Vue.js 的方式，除非是用 Laravel 运行。[在此了解如何运行安装](https://cli.vuejs.org/guide/installation.html)。

#### VUEJS 实例

在我们的机器上安装了 Vue.js 之后，我们就可以开始了，但是在此之前，让我们先来看看 Vue.js 的例子。

将数据渲染到 DOM 并使用插值

```
<div id = “app”>
  {{msg}}
</div> 
Var app = new Vue({

el: “#app”,

data: {

     msg:“Hello world”,

 }

}); 
```

我会在上面列出的每一个主题上发表文章和视频教程，并演示如何用 VUEJS 创建一个成熟的应用程序。准备好了就关注我们的博客获取更新。

### 结论

你可以从 VUEJS 中学到很多东西，但是作为一个后端开发人员，我决定编辑一个快速的主题列表，这可能对快速学习非常有用，这是基于我个人的观点，很高兴收到你的来信。

感谢您阅读我的文章。在我的[博客](https://bit.ly/multimegablog)或[媒体](https://bit.ly/mymediumprofile)上，我定期写关于后端开发、数字营销和内容管理系统的文章。要阅读我未来的帖子，只需[加入我的出版物](https://bit.ly/mymediumpublication)或点击“关注”也可以随时通过 [Twitter](http://twitter.com/kaperskyguru) 、[脸书](https://facebook.com/multimegaitschool)、 [Instagram](https://instagram.com/kaperskyguru) 与我联系。

*如果你对后端开发感兴趣(或者你是互联网发烧友)，请订阅我的 *[_Youtube 频道*([http://bit.ly/multimegaschool](http://bit.ly/multimegaschool))*，我们将为工匠们发布一系列帮助完整的教程和指南。

如果你喜欢这篇文章，一定要让我们知道并分享给你的朋友，订阅我的成长频道。分享是关怀。

后端开发人员的帖子 [Vue.js 首先出现在](https://codelikemad.com.ng/vue-js-for-backend-developers/) [CodeLikeMad](https://codelikemad.com.ng) 上。
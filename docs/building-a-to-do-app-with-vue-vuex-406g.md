# 使用 Vue & Vuex 构建待办事项应用程序

> 原文：<https://dev.to/aromig/building-a-to-do-app-with-vue-vuex-406g>

“待办”app，在“Hello World”之后，学习一个前端框架的时候写一个几乎是成年礼。我想得到更多的 Vue 和 Vuex 的练习，想先在小范围内尝试一下。Vuex 可能太热衷于追踪待办事项的应用程序，但这只是为了练习，为什么不呢？

在本文中，我们将一起构建这个应用程序。

[![To-Do App](img/8eda84b63d2f4a39ae50534f9e9a39a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GHq8c_iM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://romig.dev/asseimg/blog/vue-todo-screen.png)

现场演示: [todo.romig.dev](https://todo.romig.dev)

源代码: [Github](https://github.com/aromig/vue-todo-vuex)

部分:

*   [先决条件](#prerequisites)
    *   [Node.js](#nodejs)
    *   vista CLI
*   [使用 Vue CLI 创建项目](#creating-the-project-with-vue-cli)
*   [快速检视概述](#quick-vuex-overview)
*   [建立 Vuex 商店](#setting-up-the-vuex-store)
    *   [状态](#state)
    *   [吸气剂](#getters)
    *   [突变](#mutations)
    *   [动作](#actions)
    *   [实现商店](#implementing-the-store)
*   [构建我们的组件](#building-our-components)
    *   全息图。视图
    *   [todonew . view](#todonewvue)
    *   [app . view](#appvue)

## 先决条件

### Node.js

首先，您应该安装了 Vue CLI。这至少需要 Node.js 版。如果您没有安装 Node.js，[下载您的操作系统的当前版本](https://nodejs.org/en/download/current/)。

如果你已经安装了 Node.js，检查它的版本:

```
$ node --version
v11.3.0 
```

Enter fullscreen mode Exit fullscreen mode

如果你的版本低于 v8.9，那么就更新到最新:

```
$ npm install -g npm@latest 
```

Enter fullscreen mode Exit fullscreen mode

### 检视 CLI

在我们开始编码之前，我还将使用 Vue CLI 3 来搭建应用程序。

如果您已经安装了 Vue CLI，请检查它的版本:

```
$ vue --version
3.5.3 
```

Enter fullscreen mode Exit fullscreen mode

如果您的版本低于 3.x，请删除 vue-cli 的旧安装，然后安装最新版本:

```
$ npm uninstall -g vue-cli
.
.
.
$ npm install -g @vue/cli 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 使用 Vue CLI 创建项目

现在我们使用了正确版本的 Node 和 Vue CLI，我们可以创建我们的项目了。

在您的终端中，切换到您希望项目文件夹所在的目录。然后运行`vue create`命令创建 app 文件夹:

```
$ cd ~/Documents/Github/
$ vue create todo-app 
```

Enter fullscreen mode Exit fullscreen mode

您将看到一个配置项目的菜单。我将选择“手动选择功能”，以便将 Vuex 作为依赖项。

[![vue create screen 1](img/b18ab2de49029ea653a06e8111a85f3c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QbH2CYwk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://romig.dev/asseimg/blog/vue-todo-create-1.png)

列出了可随项目一起安装的功能列表。我选择了 Vuex(通过按空格键)。

[![vue create screen 2](img/70836f92b7e8c91d56f9305cc25b5e15.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7VgvVubU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://romig.dev/asseimg/blog/vue-todo-create-2.png)

按回车键给我一个林挺选项列表。我将选择 ESLint +标准配置。按下 Enter/Return 键后，我选择了“Lint on save ”,并将 Babel、ESLint 等的配置放在它们自己的专用配置文件中。

[![vue create screen 3](img/d8b995414bc1e4d27f4cc1065e8a6916.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VuzjKXAF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://romig.dev/asseimg/blog/vue-todo-create-3.png)

Vue CLI 在创建项目文件夹和下载依赖项时会发生变化。

[![vue create screen 4](img/381f93c694226dd9795c84ecd7da380b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qpAJ9z3c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://romig.dev/asseimg/blog/vue-todo-create-4.png)

现在我可以进入它创建的`todo-app`文件夹并启动开发服务器。我们可以访问它显示的 URL(可能与截图不同)来查看我们的应用程序。

```
$ cd todo-app
$ npm run serve 
```

Enter fullscreen mode Exit fullscreen mode

[![npm run serve](img/1dea731a03c50c655801f83d22731b64.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--32NMnH1i--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://romig.dev/asseimg/blog/vue-todo-create-5.png)

* * *

## 快速检视概述

在我们开始编码之前，让我们快速解释一下 Vuex 背后的一般思想及其在状态管理中的作用。

在 Vuex 中，我们有一个名为 *store* 的东西，它是一个全局对象，包含管理 Vue 应用程序状态所需的四个主要内容。这些事情是:

1.  **State** :保存我们 app 数据的对象。
2.  **Getters** :返回驻留在状态中的数据的函数。
3.  **突变**:直接突变/改变状态的函数。
4.  **动作**:调用突变的函数。动作可以调用多个突变，其他动作，甚至其他异步操作。

用户将与 Vue 应用程序交互，这些交互将触发*动作*，这些动作将在*状态*上提交*突变*。状态改变后，Vue 将根据这个新状态重新渲染受影响的组件。

## 设置 Vuex 商店

使用 Vuex 的第一步是决定我们的应用程序在状态中需要什么数据。这个应用程序只会跟踪待办事项的集合，所以这是我们在我们的状态下真正需要的。

数组中的每一项都应该包含一些东西:一个区分它们的唯一 ID 号，一个任务描述，以及一个让应用程序知道任务是否已经完成的标志。

### 设置店铺

在项目的 *src* 文件夹中，创建一个名为 *store* 的新文件夹。

Vue CLI 将 store.js 文件放在 *src* 文件夹中，但出于组织目的，我更喜欢将它放在 *src/store* 文件夹中，尽管如果愿意，它也可以放在 *src* 中。

如果你和我一样，把 *store.js* 文件移到 store 文件夹。如果没有，只要记住你的在哪里。

在 store 文件夹中，创建一个名为*模块*的新文件夹。然后在 modules 文件夹中创建一个名为 *todos.js* 的新文件。

**编辑“src/store/modules . todos . js”**

#### 状态

我们将首先为待办事项设置一个空数组。

```
export const state = {
  todos: []
}; 
```

Enter fullscreen mode Exit fullscreen mode

#### 吸杂

接下来将会有一个 getter 函数返回`todos`数组。

```
export const getters = {
  getTodos: state => state.todos
}; 
```

Enter fullscreen mode Exit fullscreen mode

#### 突变

现在我们必须决定国家将要经历的突变。我们将考虑对 state 进行三项更改:

1.  添加一个新项目:在输入一个新的待办事项后，我们应该能够将它添加到数组中。
2.  切换项目的完成状态:我们应该能够在真和假之间切换*完成*状态。
3.  删除一个项目:我们应该能够从数组中删除一个不再需要的项目。

```
export const mutations = {
  ADD_TODO: (state, payload) => {
    const newTask = {
      id: payload.newId,
      task: payload.task,
      completed: false
    };
    state.todos.unshift(newTask);
  },
  TOGGLE_TODO: (state, payload) => {
    const item = state.todos.find(todo => todo.id === payload);
    item.completed = !item.completed;
  },
  DELETE_TODO: (state, payload) => {
    const index = state.todos.findIndex(todo => todo.id === payload);
    state.todos.splice(index, 1);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

突变还会收到一个*有效载荷*，这是进行改变所需的信息。对于`ADD_TODO`，有效载荷是一个包含下一个可用 ID 号和任务描述的对象。`TOGGLE_TODO`和`DELETE_TODO`只需要待办事项的 ID 号，所以这就是我们要传递给这些变体的全部内容。

下面是这些函数中发生的情况:

*   `ADD_TODO`:创建一个新的待办事项对象，然后添加到数组的开头(使用[)。unshift 方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift))所以最新的项目出现在列表的顶部。
*   `TOGGLE_TODO`:找到与有效载荷中传递的 ID 相匹配的待办事项，然后我们通过翻转它的当前布尔值来改变它的`completed`状态。
*   `DELETE_TODO`:找到与有效载荷中传递的 ID 相匹配的待办事项，然后我们删除它(使用[)。拼接方法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)。

#### 动作

最后，我们可以创建动作函数，它的存在是为了调用我们的变异函数。有时他们可以调用多个变异体或其他动作，但对于这个应用程序，他们只会调用一个特定的变异。

```
export const actions = {
  addTodo: (context, payload) => {
    context.commit("ADD_TODO", payload);
  },
  toggleTodo: (context, payload) => {
    context.commit("TOGGLE_TODO", payload);
  },
  deleteTodo: (context, payload) => {
    context.commit("DELETE_TODO", payload);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们通过使用`context.commit()`函数来调用变异函数。commit()的第一个参数是变异的名称，第二个参数是有效负载。我们的有效载荷将是给予动作函数本身的有效载荷。

### 实现商店

**编辑 src/store/store.js**

现在，在实际的 store.js 文件中，我们可以很好地利用这些。

```
import Vue from "vue";
import Vuex from "vuex";
import * as todos from "@/store/modules/todos.js";

Vue.use(Vuex);

export default new Vuex.Store({
  state: todos.state,
  getters: todos.getters,
  mutations: todos.mutations,
  actions: todos.actions
}); 
```

Enter fullscreen mode Exit fullscreen mode

todo.js 模块被导入，Vuex 存储使用其属性作为值来定义。

**编辑 src/main.js**

在应用程序的 *main.js* 文件中，我们可以确保商店是导入的，并且是 Vue 对象的一部分。如果您将 *store.js* 文件移动到了 *store* 文件夹，请务必更新导入语句中的路径。不然应该没事。

```
import Vue from "vue";
import store from "./store/store";
import App from "./App.vue";

Vue.config.productionTip = false;

new Vue({
  store,
  render: h => h(App)
}).$mount("#app"); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 建筑构件

我们快到了。商店可能是应用程序中最大的部分。现在我们将开始构建用户界面。我们将有两个组件(用于列出项目和添加新项目)和包含整个应用程序的主要组件。

首先，在*组件*文件夹中，删除 Vue CLI 默认放在那里的“HelloWorld.vue”文件。

### 全息图。视图

在那里创建一个名为 *TodoList.vue* 的新文件。

让我们首先为我们的组件构建一个基本的框架。

```
<template> </template>

<script></script>

<style></style> 
```

Enter fullscreen mode Exit fullscreen mode

在`<script>`标签之间，我们将添加代码:

```
export default {
  computed: {
    todos() {
      return this.$store.getters.getTodos;
    }
  },
  methods: {
    toggleTodo: function(id) {
      this.$store.dispatch("toggleTodo", id);
    },
    deleteTodo: function(id) {
      this.$store.dispatch("deleteTodo", id);
    }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

一个`todos()`函数被创建为一个[计算属性](https://vuejs.org/v2/guide/computed.html)，它所做的就是从商店返回待办事项列表。

我们还定义了切换和删除项目的方法。它们都调用`dispatch()`方法来让*分派一个动作*，将受影响的待办事项的`id`作为有效负载传入。

现在为它的模板。在`<template>`标签之间，我们可以写如下:

```
<ul class="tasks">
  <li
    v-for="todo in todos"
    :key="todo.id"
    :class="{ completed: todo.completed }"
    class="task"
    @click="toggleTodo(todo.id)"
  >
    {{ todo.task }}
    <span class="delete" @click="deleteTodo(todo.id)">ⓧ</span>
  </li>
</ul> 
```

Enter fullscreen mode Exit fullscreen mode

该列表由`<li>`标签中的`v-for`指令创建，通过`todos`数组循环。我们将设置切换/删除列表项&的点击事件的方法。分别删除 span。此外，如果一个“completed”类的 completed 属性为 true，它将被添加到列表项中。

对于样式，我们可以在`<style>`标签之间添加以下内容:

```
.tasks {
  padding: 0;
  list-style-type: none;
}

.task {
  padding: 10px;
  margin-bottom: 0.5rem;
  border: 0.5px solid #999;
  border-radius: 5px;
  color: #34495e;
  font-weight: bold;
}

.task:before {
  content: "\2002";
}

.task:hover {
  cursor: pointer;
}

.completed {
  text-decoration: line-through;
  color: #41b883;
}

.completed:before {
  content: "\2714";
}

.delete {
  display: block;
  float: right;
  color: #d22;
  width: 1.25rem;
  height: 1.25rem;
  text-align: center;
} 
```

Enter fullscreen mode Exit fullscreen mode

### todonew . view

在*组件*文件夹中创建一个名为 *TodoNew.vue* 的新文件。搭上和上面我们为*做的*一样的脚手架。

```
<template> </template>

<script></script>

<style></style> 
```

Enter fullscreen mode Exit fullscreen mode

在`<script>`标签之间，我们会有这个:

```
export default {
  data() {
    return {
      task: "",
      newId: 0
    };
  },
  methods: {
    addTodo: function() {
      this.$store.dispatch("addTodo", this);
      this.newId++;
      this.task = "";
    }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

组件的`data`只是返回一个带有新待办事项默认值的对象。还有一个方法`addTodo`，它分派“addTodo”动作来调用它的赋值函数，然后递增`newId`并重置任务描述，这将被动地清除文本字段。

在这个组件的`<template>`标签之间:

```
<form @submit.prevent="addTodo">
  <input
    class="todo-input"
    type="text"
    placeholder="Enter a new task"
    v-model="task"
  />
</form> 
```

Enter fullscreen mode Exit fullscreen mode

该组件只包含一个带有一个输入标签的表单。在输入中键入新的待办事项描述，当按下 Enter/Return 键时，调用`addTodo`方法，在存储中创建待办事项。它还通过`v-model`绑定到`task`属性，因此当用户或底层方法更改文本时，它的更改会在两个地方都得到反映。

我们将在`<style>`标签:
之间添加输入标签的样式

```
.todo-input {
  font-family: "Open Sans", sans-serif;
  width: 100%;
  padding: 0.5rem;
  font-size: 1rem;
  outline: none;
  border-radius: 0.25rem;
  border-style: none;
  border: solid 1px lightgray;
  box-sizing: border-box;
} 
```

Enter fullscreen mode Exit fullscreen mode

### app . view

在 *src* 文件夹中，打开 *App.vue* 文件，将其内容替换为基础搭建。

```
<template> </template>

<script></script>

<style></style> 
```

Enter fullscreen mode Exit fullscreen mode

在`<script>`标签中，我们将添加我们制作的两个组件，首先导入它们，然后注册它们。

```
import TodoNew from "@/components/TodoNew.vue";
import TodoList from "@/components/TodoList.vue";

export default {
  components: {
    TodoNew,
    TodoList
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

在`<template>`标签中，我们可以充实这个应用程序。

```
<div class="container">
  <h1>To-Do List</h1>
  <div id="app">
    <div>
      <TodoNew />
      <TodoList />
    </div>
  </div>
  <img src="~@https://romig.dev/assets/logo.png" class="vue-logo" alt="Vue.js Logo" />
</div> 
```

Enter fullscreen mode Exit fullscreen mode

主应用被注入到标有“app”id 的 div 标签中。我们可以将 TodoNew 和 TodoList 组件添加到其中。这个图像只是为了增加趣味，但是请注意 src 属性。 **@** 符号被解释为[模块请求](https://cli.vuejs.org/guide/html-and-static-assets.html#url-transform-rules)，默认情况下，Vue CLI 指向资产文件夹所在的 src 文件夹。该位置将在项目构建时更新，并指向正确的位置。

我们也可以使用 *public* 文件夹来保存这样的图像，但是默认情况下 Vue CLI 将那个文件夹放在了*srchttps://romig . dev/assets*中，所以我们将只访问那个文件夹。

我们将在`<style>`标签中添加一点 CSS，作为最后的修饰:

```
@import url("https://fonts.googleapis.com/css?family=Open+Sans");

html {
  font-family: "Open Sans", sans-serif;
  background: linear-gradient(45deg, #5189c1 25%, #41b883);
  height: 100%;
  color: #333;
}

body {
  display: flex;
  height: 100%;
  margin: 0;
}

.container {
  width: 24rem;
  margin: auto;
  background-color: white;
  border-radius: 1rem;
  padding: 1rem;
  box-shadow: 0 0 1rem rgba(0, 0, 0, 0.5);
}

h1 {
  text-align: center;
  margin-top: 0;
}

.vue-logo {
  display: block;
  width: 50px;
  margin: 0 auto;
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## App 完成

我们应该结束了！继续检查运行开发服务器的 URL。

您应该能够在文本框中键入一个新任务，当按下 Enter/Return 键时，它应该会出现在它下面的列表中。

当列表项被单击一次时，它的颜色应该变成绿色并带有删除线。单击项目上的ⓧ图标应该会将其从列表中删除。

[![Vue To-Do App Finished](img/36af152b10f95b75fb8132d13abbad45.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x6HIUSzA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://romig.dev/asseimg/blog/vue-todo-finished.png)

我希望这一切进展顺利，并且很容易理解。当然，我可以做一些可能的改进。我一直在学习，这是一个持续的过程。永不停息！

### 演示

现场演示: [todo.romig.dev](https://todo.romig.dev)

这个项目的源代码可以在 [Github](https://github.com/aromig/vue-todo-vuex) 上找到。
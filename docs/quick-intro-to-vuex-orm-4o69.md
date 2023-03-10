# 快速 intro to vuex orm

> 原文：<https://dev.to/anthonygore/quick-intro-to-vuex-orm-4o69>

如果你想开发一个可扩展的 Vue 或 Nuxt 应用，你可以考虑使用 Vuex ORM。我最近在一个项目中使用了它，在这篇文章中，我将与你分享它是如何工作的，以及为什么我认为你也会喜欢它。

> 你还在想 Vuex 吗？试试我的文章 [WTF 是 Vuex？Vue 的应用数据存储入门指南](https://vuejsdevelopers.com/2017/05/15/vue-js-what-is-vuex/)。

## vuex 形状是什么

Vuex 引入了一些强大的概念来管理应用程序状态，包括存储、突变、动作等等。

Vuex ORM 是 Vuex 的一个抽象，它允许你从模型的角度考虑你的应用状态，比如文章、用户、订单等等，以及 CRUD 操作，比如创建、更新、删除等等。

> *ORM 工具*(对象关系映射)使用对象在不兼容的系统之间转换数据。ORM 对于数据库来说非常流行。

这极大地简化了您的代码。例如，你可以使用`User.update({ ... })`而不是`this.$store.state.commit("UPDATE_USER", { ... })`，这使得你的 Vue 代码更容易推理。

Vuex ORM 的另一个优点是，它通过自动设置您需要的突变和 getter 来减少样板代码，还使得在应用程序状态中使用嵌套数据结构变得容易。

## 从 Vuex 到 Vuex 形式

作为展示优势的一种方式，让我们使用 Vuex ORM 重构一些原始的 Vuex 代码。

我们将使用一个经典的待办事项列表示例，其中我们可以将待办事项标记为“完成”。这里的商店将代表:

*store/index.js*

```
store: {
  state: { todos: [] },
  mutations: {
    MARK_DONE(state, id) {
      const todo = state.todos.find(todo => todo.id === id);
      todo.done = true;
    }
  }
} 
```

假设我们在应用程序的主页上显示我们的待办事项。我们将使用一个计算属性`todos`和一个`v-for`将待办事项链接到模板。

当一个待办事项被点击时，我们将通过提交`MARK_DONE`变异来将其标记为“完成”。

*components/Home.vue*

```
<template>
  <todo-item 
    v-for="todo in todos"
    :todo="todo"
    @click="markDone(todo.id)"
  />
</template>
<script>
  export default {
    computed: {
      todos() {
        return this.$store.state.todos;
      }
    },
    methods: {
      markDone(id) {
        this.$store.state.commit(MARK_DONE, id);
      }
    }
  }
</script> 
```

## 武夫形制方式

如我所说，Vuex ORM 将数据表示为*模型*。所以我们将首先创建一个 Todo 模型，并定义我们需要的字段，如`id`、`title`和`done`。

> 与大多数 Vue 软件不同，Vuex ORM 使用 ES6 类进行配置。

*store/models/post.js*

```
import { Model } from "@vuex-orm/core";

export default class Todo extends Model {
  static entity = "todos";

  static fields () {
    return {
      id: this.string(""),      
      title: this.string(""),
      done: this.boolean(false),
      ...
    };
  }
} 
```

现在是将模型注册到 Vuex ORM“数据库”的时候了，它允许您使用模型。

同时，我们可以向 Vuex 注册 Vuex ORM 插件。

*store/index.js*

```
import VuexORM from "@vuex-orm/core";
import Todo from "./models/Todo";

const database = new VuexORM.Database();
database.register(Todo, {});

const plugin = VuexORM.install(database);

export default {
  plugins: [plugin]
}; 
```

随着我们的 Vuex ORM 商店的建立，我们可以开始在我们的组件中使用它。首先，将模型导入到组件文件中。现在，我们可以使用标准的 CRUD 方法来查询我们的存储，而不是使用 Vuex 的“奇怪”语法:

*components/Home.vue*

```
import Todo from "../store/models/todo";
export default {
  computed: {
    // posts() {
    //   return this.$store.state.todos;
    // }
    todos: () => Todos.all();
  },
  methods: {
    markAsRead(id) {
      // this.$store.state.commit(MARK_DONE, id);
      Todo.update({
        where: id,
        data: { done: true }
      });
    }
  }
} 
```

我不知道你怎么想，但我觉得这样更有可读性！

## 存储配置

但是等等，Todo 模型的存储配置在哪里？除非你想定制，否则什么都不用！Vuex ORM 将自动创建别名为模型 API 的状态、突变和获取器，例如`read`、`update`、`find`。

## 插件

更好的是，你可以给 Vuex ORM 添加一些非常方便的插件(没错，插件对插件的插件)，包括抽象你的服务器通信的插件。

例如，有一个 Axios 插件几乎是零配置的，只要你的模型端点符合 RESTful 模式。

比方说，当我们的应用程序加载时，它从服务器检索所有的待办事项，并将它们推送到商店:

```
created() {
  try {
    let { data } = await this.$http.get("/todos");
    data.forEach(todo => this.$store.state.commit(ADD_TODO, todo));
  } catch (err) {
    // handle error
  }
} 
```

Vuex ORM Axios 插件增加了额外的模型方法，如`fetch`，允许你用
替换上面的代码

```
created() {
  Todo.$fetch();
} 
```

这有多简单？

## 资源

还有很多关于 Vuex ORM 的信息需要了解，请查阅相关文档:

*   vuex 形式
*   [Vuex 形公理插件](https://github.com/vuex-orm/plugin-axios)

* * *

喜欢这篇文章吗？

通过 *Vue.js 开发者简讯*每周在您的收件箱中获取更多类似的文章。

[点击这里加入！](https://vuejsdevelopers.com/newsletter?utm_source=devto&utm_medium=article)

* * *
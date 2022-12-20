# 使用 Vue.js 编写 Firefox Web 扩展

> 原文：<https://dev.to/itachiuchiha/writing-a-firefox-web-extension-using-vue-js-2ijp>

嗨，我已经很久没有发表我的上一篇教程文章了。

在这篇文章中，我将解释如何使用 Vue.js 为 Firefox 编写 web 扩展。

在开始之前，我为我的语法错误道歉。

在本例中，我们将通过覆盖浏览器的新选项卡来创建一个待办事项应用程序。

## 前置要求

你需要有关于 Vue 的知识才能理解这个帖子。但是你不需要 Vue 来创建这样的扩展。您可以使用 Vanilla JS 为自己创建一个。

## 创建 Vue 项目

```
vue create todo-list-extension 
```

Enter fullscreen mode Exit fullscreen mode

名字不重要。我只是喜欢有意义的名字。我们不会使用 vuex 或路由器。我们将使用 localStorage 作为数据库。

## 替换默认组件。

我将替换`/src/components/`下的默认组件作为 TodoList。您还需要在`App.vue`中更改其名称

### app . view

App.vue 应该是这样的；

```
<template>
  <div>
    <todo-list />
  </div>
</template>

<script>
import TodoList from './components/TodoList.vue'
import './components/TodoList.css'

export default {
  name: 'app',
  components: {
    TodoList
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

### [你是. css](#todolistcss)

我在 components 目录中创建了一个名为 TodoList.css 的 CSS 文件。CSS 会是这样的。如果你在谷歌上搜索“Todo MVC ”,你可以找到这个 CSS
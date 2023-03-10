# 创建 Vue.js 组件库:第三部分——整合到插件中

> 原文：<https://dev.to/siegerts/consolidating-components-into-a-vue-js-plugin-ndc>

在这一点上，我们有一个[结构化的方法](https://dev.to/siegerts/creating-a-vue-js-component-library-part-ii-structure-iph)来创建新的 Vue.js 组件并将它们合并到一个模块导出中。厉害！接下来，我们将把组件捆绑到一个插件中，在 Vue 实例上注册。

请记住，在项目初始化期间，Vue CLI 会在`/src`目录的根目录下创建一个`main.js`入口点文件。通常，这被用作新 Vue 应用程序的入口点。我们将修改它来创建插件。

如果你没有阅读[系列介绍](https://dev.to/siegerts/creating-a-vue-js-component-library-part-i-introduction-2o9f)就登陆了这篇文章，先跳到那里。

## `main.js`入口点

首先，让我们删除生成的代码。我们将替换为下面的差异。

```
// main.js

- import Vue from 'vue'
- import App from './App.vue' 
- Vue.config.productionTip = false

- new Vue({
-  render: h => h(App),
- }).$mount('#app') 
+ import * as components from './components'
+
+ const ComponentLibrary = {
+  install(Vue, options = {}) {
+    // components
+    for (const componentName in components) {
+      const component = components[componentName]
+
+      Vue.component(component.name, component)
+    }
+  }
+ }
+
+ export default ComponentLibrary
+
+ if (typeof window !== 'undefined' && window.Vue) {
+  window.Vue.use(ComponentLibrary)
+ } 
```

Enter fullscreen mode Exit fullscreen mode

该文件应该类似于:

```
// main.js

import * as components from './components'

const ComponentLibrary = {
  install(Vue, options = {}) {
    // components
    for (const componentName in components) {
      const component = components[componentName]

      Vue.component(component.name, component)
    }
  }
}

export default ComponentLibrary

if (typeof window !== 'undefined' && window.Vue) {
  window.Vue.use(ComponentLibrary)
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们一步步来👀。

*   从`src/components`导入组件。这将从`index.js`中的*出口*抓取组件。这是导入(收集)我们想要包含在库中的组件的文件。

*   现在，我们将创建 Vue 插件并公开一个`install`方法。根据 Vue [插件文档](https://vuejs.org/v2/guide/plugins.html#Writing-a-Plugin):

> Vue.js 插件应该公开一个安装方法...该方法将使用 Vue 构造函数作为第一个参数，以及可能的选项来调用

*   在`install`方法中，遍历导入的组件，并将每个组件分配给`const component`。`componentName`被用作从`components`对象中取出组件的键。

*   用`Vue.component()`注册每个组件。`component.name`是来自组件的名称*属性*和作为组件的`component`。当插件在 Vue 项目中注册时，我们的组件将[全球可用](https://vuejs.org/v2/guide/components-registration.html#Global-Registration)。

在上面，`componentName`是*而不是*与`component.name`相同。

*   将组件库插件导出为默认插件。这允许作为`import ComponentLibrary from ...`语法导入到另一个文件中:

```
import Vue from 'vue'
import ComponentLibrary from './main.js'

Vue.use(ComponentLibrary)

... 
```

Enter fullscreen mode Exit fullscreen mode

*   最后，在窗口中存在 Vue 实例而没有使用模块系统的情况下，自动注册插件。当我们将库发布到内容交付网络(CDN)并将其包含在包含的 Vue 标签之后的页面上时，我们将对此进行测试。这在 Vue [入门指南](https://vuejs.org/v2/guide/index.html#Getting-Started)中有所介绍，并且是在可能不使用构建系统的现有应用中采用 Vue 时的一个选项。

目前，上面的设置只做一件事——注册组件。这就是我们现在需要它做的，但是在这种情况下，插件创建和库入口点`main.js`有不同的模式。

一些例子包括:

*   添加指令、过滤器和混合
*   使用`Vue.prototype`添加实例属性
*   导入样式依赖项
*   用`options = {}`将用户自定义选项合并到插件注册中

Vue 文档中规定的编写插件的大纲是:

```
// 1\. add global method or property
Vue.myGlobalMethod = function () {
  // some logic ...
}

// 2\. add a global asset
Vue.directive('my-directive', {
  bind (el, binding, vnode, oldVnode) {
    // some logic ...
  }
  ...
})

// 3\. inject some component options
Vue.mixin({
  created: function () {
    // some logic ...
  }
  ...
})

// 4\. add an instance method
Vue.prototype.$myMethod = function (methodOptions) {
  // some logic ...
} 
```

Enter fullscreen mode Exit fullscreen mode

来源:https://vuejs.org/v2/guide/plugins.html#Writing-a-Plugin

## 最后一个提示

另外，如果使用上面的`Vue.component`和`component.name`方法，一定要记住在组件中填充一个`name`属性。如果`component.name`不存在，注册会抛出错误。

```
<template>
  <div>
    <slot></slot>
  </div>
</template>

<script>
  export default {
    name: 'name-of-your-component' // :point_left:
  }
</script>

<style></style> 
```

Enter fullscreen mode Exit fullscreen mode

给你的组件命名为☝️

接下来，用 VuePress 收紧反馈回路，并暗示[惊人的文档](https://dev.to/siegerts/creating-a-vue-js-component-library-part-iv-documentation-with-vuepress-56h5)🌋。
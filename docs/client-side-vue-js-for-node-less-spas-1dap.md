# 用于无节点 spa 的客户端 Vue.js

> 原文：<https://dev.to/justinwash/client-side-vue-js-for-node-less-spas-1dap>

这是我最近的项目使用的结构，[永久碎片](https://www.perpetualshred.com/)。它速度快，易于使用，并允许前端作为一个静态网站托管。

[演示](https://client-side-vue.herokuapp.com)

[GitHub](https://github.com/justinwash/client-side-vue)

## 项目的结构:

#### 客户端视图/

*   **index.html**-你的单页应用的入口。通过脚本标签(Vue、Vue-Router、Http-Vue-Loader、Axios)和一个将呈现 Vue 实例的#App 元素实现全局依赖。
*   **index . js**——Vue . js、Vue-Router 被实例化，在这里您将导入您的服务并将其分配给 Vue 实例。
*   index.css -单页应用的全局样式。
*   index.php-诱骗赫罗库托管一个静态网站。

#### 客户端-vue/依赖项/

*   **vue.js** - Vue.js 本身以及所有自带的内置特性。
*   vue-router . js-vue . js 的官方路由器，允许你浏览你的单页应用。
*   **http-vue-loader.js** -客户端。vue 组件加载器。
*   axios.js -基于 Promise 的 HTTP 客户端，用于进行 ajax/http 调用。

#### 客户端-vue/服务

*   一个(非常非常)简单的状态管理服务，只有三种方法:
    *   `get(key)`
    *   `set(key, value)`
    *   `clear(key)`

## index . js 里诡异的`Object.assignProperty`东西是怎么回事？：

见[这篇伟大的文章](https://vuejsdevelopers.com/2017/04/22/vue-js-libraries-plugins/)
# Vue 表单组件 v2.0.0 已发布

> 原文：<https://dev.to/antonreshetov/vue-form-components-v2-0-0-is-out-5f00>

在版本 2 中增加了表单构建器组件——一个基于模式的构建器，用于生成包含组件和验证的表单🎉

变更日志:
**突破**

*   丢弃异步验证

**特性**

*   用于表单验证的 VeeValidate
*   表单生成器

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [安东列舍托夫](https://github.com/antonreshetov) / [ vue-form-components](https://github.com/antonreshetov/vue-form-components)

### 简洁的 vue 表单元素和带验证的表单生成器

<article class="markdown-body entry-content container-lg" itemprop="text">

[![](img/2bc02a002ab318755fcedd20eb267c6f.png)](https://github.com/antonreshetov/vue-form-components./example/assets/logo.svg)

# Vue 表单组件

[![](img/d77c981ede5766e98cee73003c298335.png)](https://camo.githubusercontent.com/7affe61c875bfa809750d67527216c3ee55e13e6aab547cd126f7ab75c433954/68747470733a2f2f696d672e736869656c64732e696f2f7472617669732f616e746f6e7265736865746f762f7675652d666f726d2d636f6d706f6e656e74732e737667)[![](img/7e94eee2a4dcb6de1c1778a66b1abb2e.png)](https://camo.githubusercontent.com/f7d468d7042c6e5c9bc938627526be01d48c42c48d50396371b0dfe3d2f7ba29/68747470733a2f2f696d672e736869656c64732e696f2f62756e646c6570686f6269612f6d696e7a69702f7666632e737667)[![](img/794dee71fac08e8d76394f580d34edc5.png)](https://camo.githubusercontent.com/6758274916a5df8306bdf50e870499616a900420e2b15e5ba3cef9b640f43707/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6c6963656e73652f616e746f6e7265736865746f762f7675652d666f726d2d636f6d706f6e656e74732e737667)

## 证明文件

[https://antonreshetov.github.io/vue-form-components](https://antonreshetov.github.io/vue-form-components/)

## 安装

### NPM

建议使用 npm 安装，它可以与 webpack 无缝协作。

```
npm i vfc
```

Enter fullscreen mode Exit fullscreen mode

### [计] 下载

您可以从 Github: Download 下载最新版本

## 快速启动

### 全球的

要在您的项目中使用，只需导入 vfc 并安装到 Vue 中。

```
import Vue from 'vue'
import App from './App.vue'
import VFC from 'vfc'
import 'vfc/dist/vfc.css'
Vue.use(VFC)

new Vue({
  render: h => h(App)
}).$mount('#app')
```

Enter fullscreen mode Exit fullscreen mode

### 一经要求

```
<template>
  <vue-input></vue-input>
</template>

<script>
  import 'vfc/dist/vfc.css'
  import { Input } from 'vfc'

  export default {
    components: {
      [Input.name]: Input
    }
  }
</script>
```

Enter fullscreen mode Exit fullscreen mode

完整的组件列表:

```
import {
  Input,
  Button,
  Checkbox,
  CheckboxGroup,
  Radio,
  Select,
  Option,
  Form,
  FormItem
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/antonreshetov/vue-form-components)
# GitHub 上漂亮的 Vue UI 工具包。

> 原文：<https://dev.to/itachiuchiha/nice-vue-ui-toolkit-repositories-on-github-l9c>

你好。在本文中，我们将看到 Vue 的漂亮的 UI 工具包。如果你准备好了，我们开始吧。

## 1-)元素

第一个将是**元素**。我认为元素有很好的成分。我创建了一个项目作为例子。

[![](img/c75a6944e2f35511b2435773969e3b3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h72-Jgb4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l53uuymreuli6emmamzk.png)

**Github 回购:**https://github.com/ElemeFE/element T2

**浏览器支持:**现代浏览器和 Internet Explorer 10+。

**安装:** `npm install element-ui -S`

### 快速启动

```
import Vue from 'vue'
import Element from 'element-ui'

Vue.use(Element)

// or
import {
  Select,
  Button
  // ...
} from 'element-ui'

Vue.component(Select.name, Select)
Vue.component(Button.name, Button) 
```

Enter fullscreen mode Exit fullscreen mode

## 2-) iView

第二个将是 **iView** 。其实我没有用在任何项目上。因为它有点慢。只是我的看法。

[![](img/316beada02086419e4fc3930319a407a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HcEhpwzj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/coo2mtizsdjm1joljxyz.png)

**GitHub 回购:**https://github.com/iview/iview T2

**浏览器支持:**大部分组件和功能支持 IE9 及以上浏览器，部分组件和功能不支持 IE

**安装:**

使用 npm:

`npm install iview --save`

使用脚本标签进行全局使用:

```
<script type="text/javascript" src="iview.min.js"></script>
<link rel="stylesheet" href="dist/styles/iview.css"> 
```

Enter fullscreen mode Exit fullscreen mode

### 快速启动

```
<template>
    <Slider v-model="value" range />
</template>
<script>
    export default {
        data () {
            return {
                value: [20, 50]
            }
        }
    }
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## 3-) Vuetify

这一个将被**验证**。我从来没用过。但是他们的介绍是这样的；

> Vue 化是 Vue 的语义组件框架。它旨在提供干净的、语义的和可重用的组件，使构建您的应用程序变得轻而易举。
> 
> 借助 Vue 和材料设计的强大功能以及大量制作精美的组件库，构建令人惊叹的应用。Vuetify 组件是根据 Google 的 Material Design Spec 创建的，具有易于记忆的语义设计，将记忆复杂的类和标记转变为具有简单清晰名称的随说随键入属性。

[![](img/8151fdc8130af004c562f3b4f2ff7608.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BrALxt2B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fonz4rujl0czarleeuii.png)

**github repo:**[https://github . com/vuetifyjs/vuetify](https://github.com/vuetifyjs/vuetify)

**浏览器支持:**

> Vuetify 支持所有现代浏览器，包括 IE11 和 Safari 9+(使用 polyfills)。从移动设备到笔记本电脑再到台式机，您可以放心，您的应用程序将按预期运行。对前沿感兴趣吗？尝试 vue-cli Webpack SSR(服务器端渲染)模板，构建针对 SEO 优化的网站。

**安装:**

使用 Vue CLI 3

```
vue create my-app

vue add vuetify 
```

Enter fullscreen mode Exit fullscreen mode

与 NPM 或纱

```
# npm
npm install vuetify

# yarn
yarn add vuetify 
```

Enter fullscreen mode Exit fullscreen mode

### 快速启动

```
import Vue from 'vue'
import Vuetify from 'vuetify'

Vue.use(Vuetify)

<v-app>
     <v-toolbar app>
       <v-toolbar-title>My Application</v-toolbar-title>
     </v-toolbar>
     <v-navigation-drawer app></v-navigation-drawer>
     <v-content>
       <v-container fluid>
          Hello World
       </v-container>
     </v-content>
   <v-footer></v-footer> </v-app> 
```

Enter fullscreen mode Exit fullscreen mode

## 4-)布埃菲

这一个将会是 **Buefy** 。基于布尔玛的 Vue.js 轻量级 UI 组件。我们在公司项目中多次使用 Buefy。

[![](img/35125e816e26883d22efcbcaafad4799.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v1d81-bj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1a1rtewhwu2zvymsi7rd.png)

**GitHub 回购:**https://github.com/buefy/buefy T2

**浏览器支持:**

Firefox、Chrome、Edge、Opera 和 Safari 的最新版本。仅部分支持 IE10+。

**安装:**

使用 NPM

```
npm install buefy 
```

Enter fullscreen mode Exit fullscreen mode

使用 CDN

```
<!-- Include Material Design Icons -->
<link rel="stylesheet" href="//cdn.materialdesignicons.com/2.0.46/css/materialdesignicons.min.css">

<!-- Buefy CSS -->
<link rel="stylesheet" href="https://unpkg.com/buefy/dist/buefy.min.css">

<!-- Buefy JavaScript -->
<script src="https://unpkg.com/buefy/dist/buefy.min.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

### 快速启动

捆绑包

```
import Vue from 'vue';
import Buefy from 'buefy';
import 'buefy/dist/buefy.css';

Vue.use(Buefy); 
```

Enter fullscreen mode Exit fullscreen mode

或单个组件

```
import Vue from 'vue'
import { Field, Input } from 'buefy/dist/components'
import 'buefy/dist/buefy.css'

Vue.use(Field)
Vue.use(Input)

or

import Vue from 'vue'
import Field from 'buefy/dist/components/field'
import Input from 'buefy/dist/components/input'
import 'buefy/dist/buefy.css'

Vue.use(Field)
Vue.use(Input) 
```

Enter fullscreen mode Exit fullscreen mode

## 【5】ant 设计视图

我实际上没有使用这个 UI 工具包。他们这样描述:

> 遵循 Ant 设计规范，我们开发了一个 Vue UI 库 antd，它包含一组用于构建丰富的交互式用户界面的高质量组件和演示。

[![](img/a7c67b260999c90e930e0c2fb776e5c1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5rF4mUSx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ulc10vejgc6qmfji2wm0.png)

**GitHub 回购:**https://github.com/vueComponent/ant-design-vue T2

**浏览器支持:**

现代浏览器和 Internet Explorer 9+(带聚合填充)

**安装:**

使用 NPM

`npm install ant-design-vue --save`

使用纱线

`yarn add ant-design-vue`

### 快速启动

完全导入

```
import Vue from 'vue'
import Antd from 'ant-design-vue'
import App from './App'
import 'ant-design-vue/dist/antd.css'
Vue.config.productionTip = false

Vue.use(Antd)

/* eslint-disable no-new */
new Vue({
  el: '#app',
  components: { App },
  template: '<App/>'
}) 
```

Enter fullscreen mode Exit fullscreen mode

以上全部进口。请注意，CSS 文件需要单独导入。

仅导入您需要的组件

```
import Vue from 'vue'
import { Button, message } from 'ant-design-vue'
import App from './App'

Vue.config.productionTip = false

/* v1.1.2 */
Vue.component(Button.name, Button)
Vue.component(Button.Group.name, Button.Group)

/* v1.1.3+ Automatically register components under Button, such as Button.Group */
Vue.use(Button)

Vue.prototype.$message = message

/* eslint-disable no-new */
new Vue({
  el: '#app',
  components: { App },
  template: '<App/>'
}) 
```

Enter fullscreen mode Exit fullscreen mode

希望这些 ui 工具包能帮到你。

感谢阅读！
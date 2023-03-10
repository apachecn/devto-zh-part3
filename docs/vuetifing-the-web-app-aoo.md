# 现代网络应用风格:虚拟化

> 原文：<https://dev.to/sarmis/vuetifing-the-web-app-aoo>

在 [(PWA 基础设施:Vue，包裹&工具箱)](https://dev.to/sarmis/single-page-progressive-web-applications-with-vue-js-2op8)中，我们使用 [Vue.js](https://vuejs.org) 和[工具箱](https://developers.google.com/web/tools/workbox/)为单页渐进式 web 应用构建一个模板。结果是功能性的，但不好看，在这篇文章中我们将致力于这一点。

让 webapp 变得好看有很多选择，从手写 css，使用 css 框架，或者因为我们已经使用 Vue.js，使用 Vue 组件库，这有增加功能的额外好处。有各种各样的 vue 组件库，其中最有前途的是 [Vuetify.js](https://vuetifyjs.com) ，它实现了谷歌的材料设计，因此看起来几乎是 android 手机的原生组件。

## 资源

本教程的代码可以在 **vuetify 分支**的[项目的 github repo](https://github.com/sarmis/greekdev-template-vue-spa) 中找到

## 整合 vuetify

集成 vuetify 其实很简单。

1.  通过运行以下命令安装 vuetify 和材料设计图标:

```
\>npm i -s vuetify
\>npm i -s material-design-icons-iconfont 
```

1.  导入脚本，并通过在`src\web\index.js`(就在`import Vue from 'vue'`之后)中添加以下内容，将它们添加到 Vue 中

```
import Vuetify from 'vuetify';
import 'vuetify/dist/vuetify.min.css' // Ensure you are using css-loader
import 'material-design-icons-iconfont'
Vue.use(Vuetify); 
```

## 使用 vuetify

在 [Vuetify.js 站点](https://vuetifyjs.com)中有很多文档和例子，但是让我们构建一个非常简单的 web 应用程序布局。

我们将只接触`.vue`文件

在`src\web\app.vue`中，我们将在页面顶部添加一个永久工具栏，在页面左侧添加一个导航抽屉。我们将工具栏配置为始终可见，默认情况下，导航抽屉会在宽度超过 640 像素的页面中打开。

```
<template lang="pug">
    v-app
        v-toolbar(app)
            v-toolbar-side-icon(@click="drawer=!drawer")
            v-toolbar-title Home
            v-spacer
            v-toolbar-items
                v-btn(flat to="/") home
                v-btn(flat to="/profile") profile
                v-btn(flat to="/about") about    

        v-navigation-drawer(v-model="drawer" app fixed mobile-break-point=640)
            v-list()               
                v-list-tile( to="/profile")
                    v-list-tile-action
                        v-icon account_circle
                    v-list-tile-title Profile

                v-divider

                v-list-tile(to="/" exact)
                    v-list-tile-action
                        v-icon home
                    v-list-tile-title Home

                v-list-tile(to="/about" exact)
                    v-list-tile-action
                        v-icon notes
                    v-list-tile-title about 

        v-content           
            router-view

</template>

<script>
    import Vue from "vue";
    import {router} from './_router.js';
    import {store} from './_store.js'

    export default Vue.extend({ 
        data: () => ( { 
            drawer: null 
        } ),

        store: store,
        router: router    
    });    
</script> 
```

在`src\web\vues\profile.vue`中，我们使用`v-card`组件
添加了一个(虚拟)登录表单

```
<template lang="pug">
    v-container(fluid fill-height)
        v-layout(justify-center)
            v-flex(xs12 sm8 md4)

                v-card(class="elevation-12")

                    v-toolbar( dark color="primary")
                        v-toolbar-title Login

                    v-card-text
                        v-form
                            v-text-field(prepend-icon="person" name="login" label="Username" type="text")
                            v-text-field(id="password" prepend-icon="lock" name="password" label="Password" type="password")

                    v-card-actions
                        v-spacer
                        v-btn(color="primary") Login

</template>

<script>
    export default {

    }
</script> 
```

我们对…做了一些小改动

`src\web\vues\home.vue`

```
<template lang="pug">
    v-container(fluid) 
        h1 Home
        p Welcome: {{$store.state.name}}

</template>

<script>
    export default {
    }
</script> 
```

最后我们修改`src\web\vues\about.vue`

```
<template lang="pug">
    v-container(fluid fill-height)
        v-layout(justify-center)
            v-flex(xs12 sm8 md4)

                v-card(class="elevation-12")

                    v-toolbar( dark color="primary")
                        v-toolbar-title About

                    v-card-text
                        p Let's Vuetify the template!                       

                    v-card-actions
                        v-spacer                        

</template>

<script>
    export default {

    }
</script> 
```

## 哈巴狗？

我们选择使用`pug`而不是`html`作为我们的标记语言，这是个人偏好，主要是因为它更紧凑(与 html 版本相比，pug 中一个结构良好的页面应该有大约一半的行数——因为没有结束标记)。

## 结果

是时候在桌面浏览器中查看最终结果了:

[![](img/666dc373128e906885a56f484d433eff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WFQXB0r9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/./result-desktop.png)

在手机中(导航抽屉关闭，导航抽屉打开):

[![](img/ef30b79a3f65732a30b51883a86b164f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n-bsvFJV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/v1/./result-mobile.png)

## 下一步

在下一篇文章中，我们将在我们的 web 应用程序中添加一些真正的功能。

###### [希腊开发商的原始帖子](https://greekdeveloper.com/2019/vuetify-template/)
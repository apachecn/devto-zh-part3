# 现代 WebApps -基础设施:Vue、包裹和工具箱

> 原文：<https://dev.to/sarmis/single-page-progressive-web-applications-with-vue-js-2op8>

单页应用程序(SPA)是包含在单个网页中的 web 应用程序，由于不必下载和解析每个页面的 html，因此提供了无缝导航体验。渐进式 web 应用程序(PWA)是一种 Web 应用程序，它使用服务人员“代理”和清单文件，提供必要的基础结构来允许浏览器兑现应用程序，以便在恶劣或无网络条件下使用。所有现代的浏览器和操作系统都允许 pwa 在本地“安装”,因此提供了类似本机的用户体验。

PWA 通常是构建原生应用程序的可行替代方案，尤其是对于小型团队，因为大多数应用程序商店现在都接受 PWA，并且所有主流操作系统(Windows、Android、iOS)都允许安装 PWA 并将其显示在桌面上。PWAs 会立即打开，并且可以指示浏览器隐藏其控件，从而提供类似本机的外观和感觉。

现代工具可以简化开发，但设置它可能是一项耗时的任务。让我们看看如何设置 SPA & PWA 项目。本教程的范围是描述设置，而不是具体描述每个框架/工具——每个工具都有解释其工作原理的扩展文档。

* * *

## 框架&工具

#### vista . js

我们将使用 Vue 生态系统进行重物搬运:

*   **[Vue.js](https://vuejs.org/)** 将通过提供一种声明性的方法来定义我们的视图，并将代码分成单个文件组件，
*   **[VueX](https://vuex.vuejs.org)** 将用于状态管理
*   **[Vue 路由器](https://router.vuejs.org/)** 将用于处理 SPA 路由

#### Node.js

**[node.js](https://nodejs.org)** 将为捆绑实用程序和所有其他可能需要的实用程序提供支持

#### package . js

**[包](https://parceljs.org/)** bundler 将用于构建和捆绑应用程序

#### 工具箱

**[工具箱](https://developers.google.com/web/tools/workbox/)** 将处理服务人员的详细信息。

* * *

## 文件布局

*   `./src`将包含这个项目的所有源代码。
    *   `./src/web`将包含 web 应用程序(html 客户端)的源代码。
    *   `./src/db`(可选)将包含任何数据库初始化脚本
    *   `./src/server`(可选)将包含任何服务器端项目
*   `./dist`将包含所有生成的工件，在 git 中应该被忽略
    *   `./dist/web`将包含构建和捆绑的 web 应用程序。
    *   `./dist/db`(可选)将包含由数据库脚本生成的任何工件
    *   `./dist/server`(可选)将包含任何服务器端项目(已编译)
*   `./.cache`将由包裹生成，在 git 中应被忽略
*   `./node_modules`将由 npm 或包裹生成，并应记入 git

* * *

## 代码

代码可以在[项目的 github repo](https://github.com/sarmis/greekdev-template-vue-spa) 中找到

#### Javascript 依赖关系

[![](img/a00c9c45c8b28c0078315f82e923f30a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3TuVG9DD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://greekdeveloper.com/2019/vue-template/dependencygraph.svg)

#### 切入点(index.html)

是我们的切入点，只是将一切联系在一起

*   `<link rel="manifest" href="./manifest.webmanifest">`链接了*。webmanifest* 文件
*   `<div id="vueapp"></div>`定义 vue 安装点
*   `<script src="./index.js"></script>`加载包含 vue 应用程序的脚本
*   `navigator.serviceWorker.register('/service-worker.js');`注册服务工作者脚本

```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <link rel="manifest" href="./manifest.webmanifest">

        Vue.js Single Page Application Template       
    </head>

    <body>
        <div id="vueapp"></div>          

        <script src="./index.js"></script>
        <script>
            if ('serviceWorker' in navigator) {
                window.addEventListener('load', () => {
                    navigator.serviceWorker.register('/service-worker.js');
                });
            }            
        </script>
    </body>
</html> 
```

#### 清单

`./src/web/manifest.webmanifest`描述申请，并且是申请被视为 PWA 所必需的。
保持*很重要。用于包裹兼容性的 webmanifest* 扩展。

```
{  "name":  "My application name",  "short_name":  "app",  "start_url":  "/",  "background_color":  "#3367D6",  "display":  "standalone",  "scope":  "/",  "theme_color":  "#3367D6",  "icons":  [  {  "src":  "/res/app-256.png",  "type":  "image/png",  "sizes":  "256x256"  }  ]  } 
```

#### 服务人员(工作箱)

实施将申请视为 PWA 所需的服务人员。使用谷歌的工具箱。Workbox 定义了多种策略(网络优先、缓存优先和重新验证时失效)。在本例中，所有资源都使用网络优先策略提供服务，因为这是最具响应性的方法，并且保持了离线工作的能力。

```
console.log("service-worker.js")
// import service worker script
importScripts('https://storage.googleapis.com/workbox-cdn/releases/4.2.0/workbox-sw.js');

// Network First
[ 
    '/$',  // Index 
    '/*',  // Anything in the same host
    '.+/*' // Anything in any host 
]
.forEach(mask => {
    workbox.routing.registerRoute(
        new RegExp(mask),
        new workbox.strategies.NetworkFirst( { cacheName: 'dynamic' } ) 
    );
}); 
```

#### Vue 绑定

`./src/web/index.js`用于绑定 vue 应用和我们的 css(在 scss 中)。它导入了 vue 框架、我们的 Vue 应用程序代码(`app.vue`)和我们的样式(`styles.scss`)——所有这些文件都位于`./src/web/`中，但是我们在导入中使用了相对路径。最后，我们将 vue 应用程序挂载到相应的 div 元素。

```
import Vue from 'vue';

import App from './app.vue';
import './style.scss'

new Vue(App).$mount('#vueapp') 
```

#### 检视应用程式

`./src/web/app.vue`包含我们的 vue 应用程序，作为一个单独的文件组件。

在`<template>`中，我们构建了一个简单的导航菜单和*路由器视图*，它是我们单页面应用程序的宿主，所有其他页面都安装在路由器视图元素中。在这个模板中，我们使用`pug`代替 html。

在`<script>`中，我们导入了 vue 框架和两个定制模块`_router.js`和`_store.js`，并且我们通过用我们刚刚加载的*商店*和*路由器*模块扩展默认 vue 应用来创建我们的 vue 应用。

在`<style>`中，我们使用 *scss* (外部绑定器会将其转换为 css)
为菜单提供了一些局部(范围)样式

```
<template lang="pug">
    div
        nav.navbar
            router-link(to="/") home
            router-link(to="/profile") profile
            router-link(to="/about") about
        router-view
</template>

<script>
    import Vue from "vue";
    import {router} from './_router.js';
    import {store} from './_store.js'

    export default Vue.extend({ 
        store: store,
        router: router    
    });    
</script>

<style lang="scss" scoped>

    .navbar {
        text-align: center;

        * + * {
            margin-left: 8px;
        }
    }
</style> 
```

#### 路由器

`./src/web/_router.js`通过加载所有页面并声明它们的路由来配置和初始化 *vue 路由器*。

```
import Vue from "vue";

import VueRouter from 'vue-router';

Vue.use(VueRouter)

// 1\. Import Components
import home    from './vues/home.vue'
import about   from './vues/about.vue'
import profile from './vues/profile.vue'

// 2\. Define some routes
const routes = [
    { path: '/'       , component: home    },   
    { path: '/profile', component: profile },
    { path: '/about'  , component: about   }
]

// 3\. Create & Export the router 
export const router = new VueRouter({
    routes: routes
}) 
```

#### 商店

`./src/web/_store.js`配置并初始化 vuex 存储模块。它声明全局状态和可用的突变。vuex 允许所有视图组件(通过突变)修改全局状态，同时保持框架的反应性。(即提交突变将更新所有受状态变化影响的组件)。

```
import Vue from 'vue'
import Vuex from 'vuex';

Vue.use(Vuex);

export const store = new Vuex.Store({
    state: {
        name: 'Unknown'
    },

    // Usege: $store.commit('mutationan', parameter)
    mutations: {
        setName(state, name) {
            Vue.set(state, 'name', name);
        }
    }
}); 
```

#### 页面

在我们的例子中有三个页面， *home* 和 *about* 几乎相同，两者都呈现商店的 *name* 属性。

*profile* 提供了一个输入框，用户可以在其中输入自己的名字，当输入的值改变时，可以立即更新全局状态。

`./src/web/vues/about.vue`

```
<template lang="pug">
    div 
        h1 About
        p  Welcome: {{$store.state.name}}
</template>

<script>
    export default {

    }
</script> 
```

`./src/web/vues/home.vue`

```
<template>
    <div> 
        <h1>Home</h1> 
        <p> Welcome: {{$store.state.name}}</p>

    </div>   
</template>

<script>
    export default {
    }
</script> 
```

`./src/web/profile.vue`

```
<template lang="pug">
  div 
    h1 Profile
    p  Welcome: {{$store.state.name}}
    div.form 
      table
        tr
          td Name
          td
            input(:value="$store.state.name" @input="$store.commit('setName',$event.target.value)")
</template>

<script>
    export default {

    }
</script>
<style lang="scss" scoped>
    .form {
        display: flex;
        justify-content: center;
    }
</style> 
```

## 发展中

为了在此模板上进行开发，需要执行以下步骤

*   下载或克隆[代码](https://github.com/sarmis/greekdev-template-vue-spa)

*   安装包裹`npm i -g parcel-bundler`

*   安装项目依赖关系`npm install`(在项目根目录中)

*   运行开发脚本`npm run dev`

###### [希腊开发商的原始帖子](https://greekdeveloper.com/2019/vue-template/)
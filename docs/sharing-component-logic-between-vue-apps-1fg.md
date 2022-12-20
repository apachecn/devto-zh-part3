# 在 Vue 应用程序之间共享组件逻辑

> 原文：<https://dev.to/perpetualwar/sharing-component-logic-between-vue-apps-1fg>

比方说，需要有 2 个独立的 Vue 应用程序，它们需要有一些共享的逻辑。其中一种情况是移动(pwa)应用程序和 web 应用程序共享一些逻辑，如身份验证或其他基于组件的逻辑，但同一类型的模块具有不同的功能(实际上意味着拥有自己的组件)。

这可能不是你想要的工作流程，通常你只是搭建一个 vue pwa 应用程序，并使用响应式设计为移动和网络提供相同的应用程序(这更容易，维护更明智)，但如果你像我一样对你的项目有类似的要求，请继续阅读。

至少有两种方法可以实现这一点，但我想讨论一个项目的设置，其中 vue cli 被配置为为单独的项目提供单独的构建，并且仍然有许多共享的逻辑。

您的 src 文件夹树应该如下所示:

```
|--src
    |--mob
        |--components
        |--views
        |--router
        |--main.js
        |--App.vue
    |--web
        |--components
        |--views
        |--router
        |--main.js
        |--App.vue
    |--assets
    |--store
    |--shared 
```

考虑到我们想要不同的构建，这意味着我们必须提供单独的入口点(main.js 和 App.vue 文件)、组件以及单独的路由器，因为视图组件是顶级路由器组件(如果您的应用程序使用路由器的话)。

在您的`package.json`中，像这样修改您的脚本设置:

```
"scripts": {
    "serve:web": "vue-cli-service serve src/web/main.js",
    "serve:mob": "vue-cli-service serve src/mob/main.js",
    "build:web": "vue-cli-service build --dest dist_web src/web/main.js",
    "build:mob": "vue-cli-service build --dest dist_mob src/mob/main.js",
    //other scripts...
}, 
```

我们将有单独的命令来服务/构建网络和移动应用。对于 serve，我们只提供入口点(这在两种情况下都不同于默认入口点)，对于 build，我们还必须提供目标文件夹，这样它们就不会互相覆盖。

在共享文件夹中，我们所有的共享组件逻辑都将驻留在其中，因为其中大部分只是导入到要使用它们的相应组件中的功能(因此在 web 和移动组件中都是如此)。

由你来决定 store 是否应该对两个应用都是全局的，或者是否应该是应用范围的，我想这取决于用例。

现在，我们有了自己的文件夹结构和服务/构建命令，因此我们可以将它们视为独立的应用程序，它们的行为也是如此。但是共享逻辑将遵循 DRY 原则从一个地方使用，这比拥有两个完全不同的项目并跨代码库复制一定数量的潜在相同代码要容易维护得多。

如果你想深入了解，可以在 [github](https://github.com/PerpetualWar/vue-shared-app) 找到测试项目。

欢迎评论，感谢阅读。
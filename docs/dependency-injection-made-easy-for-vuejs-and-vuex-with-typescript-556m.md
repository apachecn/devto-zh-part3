# 使用 TypeScript 使 VueJS 和 Vuex 的依赖注入变得简单

> 原文：<https://dev.to/fernalvarez590/dependency-injection-made-easy-for-vuejs-and-vuex-with-typescript-556m>

[![](img/f57b8c6c03509c52a0078599d0b1b464.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6yI6XR43--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2A38yIPuEG7RKUFi-v) 

<figcaption>图片由@ mriveodorta</figcaption>

不得不承认*(而且这是我个人的观点)*。依赖注入在 VueJS 生态系统中并不流行，我敢说，在 JS 生态系统中也不流行。人们希望快速而完美地完成工作，这绝对没问题，但我觉得大多数坚实的面向对象的原则在 JS 生态系统中消失了，但让我们在另一篇文章中讨论这个问题😛

* * *

为了理解依赖注入及其好处，我**绝对**推荐你先阅读这篇文章:

[快速介绍依赖注入:什么是依赖注入，何时使用依赖注入](https://medium.freecodecamp.org/a-quick-intro-to-dependency-injection-what-it-is-and-when-to-use-it-7578c84fa88f)

* * *

依赖注入**在 VueJS 应用程序中有点难以实现，因为它需要对代码库做一些调整。JS 和 TS 的依赖注入解决方案之一是 [InversifyJS](http://inversify.io/) ，它是我最喜欢的依赖注入容器，但仍然很难与 VueJS 一起使用。幸运的是，没有更多的人给我们，对我来说，**是在我们的 VueJS 应用中实现 DI 的解决方案**。**

在本指南/练习中，我希望您了解如何使用库 [Inversify Props](https://github.com/CKGrafico/inversify-props) 使用 TypeScript 轻松地将依赖注入实现到 VueJS 项目中

[CKGrafico/inversify-props](https://github.com/CKGrafico/inversify-props)

[![](img/4e93a0859451dac5652e1304110de2e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HflZDX5S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/498/0%2A92p2JN9TLqFNWpzw) 

<figcaption>【权力的游戏(HBO)</figcaption>

### 完整的例子

如果你想看完整的例子，你可以直接点击这个链接！

[司机 590/vuejsdepencyinjection xample](https://github.com/jefer590/vuejsdepencyinjectionexample/tree/03/use-dependency-injection-inside-vuex-module)

### 我需要什么？

1.  安装最新版本的 [**纱**](https://yarnpkg.com/) 和 [**Vue CLI**](https://cli.vuejs.org/)
2.  一个与 TypeScript 兼容的编辑器(为此我喜欢 WebStorm)
3.  复制练习中的基本 repo。

#### 我们在建造什么？

一个超级简单的应用程序:

1.  首先，将使用通过组件连接到 [https://api.kanye.rest](https://api.kanye.rest/) 的服务
2.  然后，同样的行为，但通过 Vuex 模块

### 做好准备

在您的机器中克隆下一个回购:

[司机 590/vuejsdepencyinjection xample](https://github.com/jefer590/vuejsdepencyinjectionexample/tree/master)

然后使用以下命令安装依赖项

```
$ yarn install 
```

### 创建服务

在/src 文件夹中，创建一个名为 services 的新文件夹，并在新文件夹中创建一个名为 impl 的文件夹

*   /services 根将用于方便地公开服务接口
*   /services/impl 将用于添加我们的接口/服务的具体实现

在/services 文件夹中，创建 IKanyeWestService.ts 文件，形状如下:
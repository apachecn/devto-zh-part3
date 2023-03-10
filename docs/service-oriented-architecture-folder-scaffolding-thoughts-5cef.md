# 面向服务的架构文件夹脚手架，想法？

> 原文：<https://dev.to/protium/service-oriented-architecture-folder-scaffolding-thoughts-5cef>

大家好，关于服务项目的文件夹搭建，你们有什么最佳实践？

我在许多地方看到的一种常见做法是遵循 MVC 模式，这与 RoR 汽车生成的框架相同。但是我发现这有点不方便，而且我的解决方案也不能 100%地说服我。所以我用下面的脚手架做了一个演示项目。

```
+-- config -- Server config files (.yml, .json, .env)
+-- scripts -- all build & deployments scripts
+-- logs -- daily rotatory log files 
+-- src
|   +-- api -- all the service endpoints
|   |   +-- endpoint1
|   |   |   +-- controller.ts
|   |   |   +-- model.ts
|   |   |   +-- routes.ts
|   |   |   +-- specs.ts
|   +-- business -- the business layer (authentication, server audit)
|   +-- components -- (such as loggers, external services consumption)
|   +-- config -- configuration middlewares
|   +-- data -- data layer
|   |   +-- repositories // all repositories needed for api/models
|   |   |   +-- endpoint1Repository.ts
|   |   +-- providers // all data source providers the service could support
|   |   +-- adapters 
```

Enter fullscreen mode Exit fullscreen mode

使用这种方法，我将所有与端点相关的逻辑放在同一个文件夹中。但是我觉得这些存储库有点“T0”远。

你们觉得怎么样？建议？
你可以在这里看到一个演示项目[(我这样做是为了验证概念，并学习一些关于 Firebase 和 CircleCI 的知识)](https://github.com/ioprotium/sarch)
# Buckaroo - C/C++包管理器发布 2.0 版

> 原文：<https://dev.to/nikhedonia/buckaroo---cc-package-manager-hits-v20-5134>

[https://github.com/loopperfect/buckaroo](https://github.com/loopperfect/buckaroo)

# 为什么是牛仔？

像 Yarn 和 Cargo 这样的包管理器已经展示了当开发人员能够轻松地集成一个大型的项目生态系统时，他们是多么的高效。Buckaroo 为 C++填补了这一空白。

牛仔工作流程如下所示:

```
# Create your project file
$ buckaroo init

# Install dependencies

$ buckaroo add github.com/buckaroo-pm/boost-thread@branch=master
# Run your code
$ buck run :my-app 
```

# 特性

C++有独特的需求，所以 Buckaroo 是一个高度复杂的软件。

*   直接从 GitHub、BitBucket、GitLab、hosted Git 和 HTTP 中提取依赖项
*   完全可再现的构建和依赖性解决方案
*   完全去中心化——没有中央服务器或发布过程
*   允许任何构建配置(即使是在逐个包的基础上)
*   避免“依赖地狱”的私有和公共依赖
*   每个包有多个库，所以像 Lerna 这样的工具是不必要的
*   从 mono-repos 中提取单个包
*   完全支持语义版本控制(但是只有在你需要的时候！)
*   住在头上！通过直接依赖 Git 分支快速移动，但要以可控的方式
*   使用巧妙的试探法快速解决问题
*   版本等价检查以减少依赖冲突
*   TOML 配置文件，便于计算机和人工编辑
*   脱机工作(使用填充的缓存)
*   启用升级机器人，只需一次点击即可保持一切最新

# 好奇？

前往 https://github.com/loopperfect/buckaroo
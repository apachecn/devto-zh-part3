# 如何为 react 应用选择最佳结构？

> 原文：<https://dev.to/minority/how-to-choose-the-best-structure-for-react-applications-5899>

你好，

我是一个新的 JS 初学者，为了学习我做了一个项目。在多次将文件从一个目录转移到另一个目录后，我决定写这篇文章。

对于那些马上想看代码的人 [GitHub](https://github.com/minority/node-react-mongo-auth/tree/master/client)

我怎么了

`src/feature/`

```
Auth (folder module feature, exported all reducers/routes)
   Signup
   RestorePassword
   Signin
      components/SigninForm
           index.jsx
           index.module.scss
      actions.js
      reducer.js
      index.jsx (container with connect redux)
   index.js (exported all reducers/routes)
   reducers.js (include all reducers module)
   routes.js (routes for module)
Cabinet
   Home
   Users
      index.jsx
   index.js
   routes.js
Shared
   Layout
   PrivateRoute 
```

你用的是什么结构？
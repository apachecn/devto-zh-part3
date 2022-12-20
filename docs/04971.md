# 找不到相应的 JavaScript

> 原文：<https://dev.to/kentonlam/corresponding-javascript-cannot-be-found-5a51>

第一次学习 Typescript 时(正确的学习，而不是 create-react-app)，我在尝试运行时遇到了这个错误。VS 代码中的 ts 文件。

解决方案是实际构建。ts 文件转换为. js 文件。为此，运行

```
tsc --watch 
```

Enter fullscreen mode Exit fullscreen mode

在您的项目目录中。
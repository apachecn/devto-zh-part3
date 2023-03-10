# 为 Redux 进行代码拆分的简单方法

> 原文：<https://dev.to/revskill10/simple-way-to-do-code-splitting-for-redux-p7d>

大多数文档、教程、youtube 视频都教你如何创建自己的 Redux 商店。很甜蜜。但是让我们做得更好。

你想用你的 Redux 商店做代码分割吗？

通过代码分割 Redux store，我的意思是，在某些情况下，您不希望导入所有 Redux 动作以便在 React 组件中使用，例如，在`/login`页面上，您只希望导入`login`动作。

解决方案非常简单！

就用老派`window.REDUX_STORE_LOGIN`做`/login`路线。

```
window.REDUX_STORE_LOGIN = createStore(...) 
```

当用户访问`/login`路线时执行此操作。

对你所有的路线做同样的事情，把你的 Redux 商店分割到最大。

热毒星快乐！
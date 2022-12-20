# React Hook:使用权限

> 原文：<https://dev.to/sergiodxa/react-hook-usepermissions-3gkh>

*最初发表于[https://sdx.im/articles/libs/react-use-permissions](https://sdx.im/articles/libs/react-use-permissions)T3】*

针对[权限 API](https://developer.mozilla.org/en-US/docs/Web/API/Permissions) 的 React 挂钩。

> **注意**:这里使用的是新的 [React Hooks API 提议](https://reactjs.org/docs/hooks-intro.html)，在 React 最终发布之前会有变化。
> 你需要在`^16.7.0-alpha.0`安装`react`、`react-dom`等

## 安装

用 yarn 命令安装它。

```
yarn add react-use-permissions 
```

Enter fullscreen mode Exit fullscreen mode

或者在命令中使用 npm。

```
npm install react-use-permissions 
```

Enter fullscreen mode Exit fullscreen mode

## 用法

将它导入到应用程序中。

```
import usePermissions from "react-use-permissions"; 
```

Enter fullscreen mode Exit fullscreen mode

然后在传递任何有效名称的任何功能性 React 组件中使用它。

> 有效的名称有`accelerometer`、`accessibility-events`、`ambient-light-sensor`、`background-sync`、`clipboard-read`、`clipboard-write`、`geolocation`、`gyroscope`、`magnetometer`、`microphone`、`midi`、`notifications`、`payment-handler`、`persistent-storage`和`push`、

```
const format = function Component() {
  const hasPermissions = usePermissions("geolocation");
  const content = (() => {
    switch (hasPermissions) {
      // User has granted permissions
      case true: {
        return "Permissions granted";
      }
      // User has denied permissions
      case false: {
        return "Permissions denied";
      }
      // User will be prompted for permissions
      case null: {
        return "Asking for permissions";
      }
    }
  })();
  return <h1>{content}</h1>; }; 
```

Enter fullscreen mode Exit fullscreen mode

当组件被渲染时，钩子将最初返回`null`，然后检查用户是否已经授予或拒绝权限，返回一个布尔值。如果用户还没有，它将被提示，然后钩子将返回新的状态。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [ sergiodxa ](https://github.com/sergiodxa) / [反应-使用-权限](https://github.com/sergiodxa/react-use-permissions)

### 权限 API 的反应挂钩

<article class="markdown-body entry-content container-lg" itemprop="text">

# `react-use-permsissions`

> 权限 API 的 React 挂钩

[![](img/e2666ac771b380f6ad7a053ee8d99855.png)](https://www.patreon.com/sergiodxa) 

## 安装

```
yarn add react-use-permissions
```

Enter fullscreen mode Exit fullscreen mode

## 使用

```
import usePermissions from '../src';

const format = hasPermissions => {
  switch (hasPermissions) {
    // User has granted permissions
    case true: {
      return "Permissions granted";
    }
    // User has denied permissions
    case false: {
      return "Permissions denied";
    }
    // User will be prompted for permissions
    case null: {
      return "Asking for permissions";
    }
  }
}

function App() {
  const hasPermissions = usePermissions("geolocation");
  const content = format(hasPermissions);
  return <h1>{content}</h1>;
}
```

Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/sergiodxa/react-use-permissions)
# 用包裹在不到一分钟的时间内创建一个最小的 React 应用程序

> 原文：<https://dev.to/ascorbic/create-a-minimal-react-app-in-less-than-a-minute-with-parcel-l4n>

开始使用 React 最简单的方法是使用`create-react-app` (CRA)，但这通常是多余的。在这篇文章中，我将带你在不到一分钟的时间内从零开始使用 React app，没有 CRA，也没有大量的依赖。此外，我还会附上打字稿。

CRA 的主要好处是它让你不必设置 Webpack 和 Babel 工具链。[package](https://parceljs.org/)是真正零配置的 Webpack 的绝佳替代品，可以让您在几秒钟内启动并运行。

首先，创建你的目录并初始化 Yarn。如果你愿意，也可以用 NPM。

```
➜ mkdir parceldemo
➜ cd parceldemo
➜ yarn init -y 
```

Enter fullscreen mode Exit fullscreen mode

现在安装软件包:

```
➜ yarn add -D parcel-bundler 
```

Enter fullscreen mode Exit fullscreen mode

这是您安装的工具链！您也可以全局安装 package，或者使用 npx，但是我更喜欢将其安装为 devDependency。

现在创建 React 应用:

```
<html>
Hello React
<body>
<div id="root"></div>
<script src="index.tsx"></script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

当然，如果您愿意，也可以使用 JavaScript。就改叫 index.js 吧。

```
 import * as React from "react";
import { render } from "react-dom";

const App:React.SFC = () => <h1>Hello world</h1>; 
render(<App />, document.getElementById("root"));

if (module.hot) {
  module.hot.accept();
} 
```

Enter fullscreen mode Exit fullscreen mode

现在运行包裹:

```
➜ yarn parcel index.html 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！package 将自动安装 React 和 ReactDOM 作为依赖项，如果您正在使用 TypeScript，它也会自动安装。热模块重装也可以。
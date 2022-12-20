# 如何在 create-react-app 项目中垂直居中元素

> 原文：<https://dev.to/mcrowder65/how-to-center-elements-horizontally-on-a-create-react-app-project-1omn>

TLDR:只要在`public/index.html`
后面加上以下内容

```
<style>
  html, body, #root, #root>div {
    height: 100%;
    margin: 0;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

您刚刚运行了`create-react-app my-app`，现在您编辑您的`App.js`文件以包含一个居中的按钮:

```
import React, { Component } from "react";

class App extends Component {
  render() {
    return (
      <div
        style={{
          display: "flex",
          justifyContent: "center",
          alignItems: "center"
        }}
      >
        <button onClick={() => alert("hello")}>alert hello</button>
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

但是，哦不！它只是水平居中，而不是垂直居中！

为了看得更远，给你的 div 添加一个背景色，就像这样:

```
<div
  style={{
    display: "flex",
    justifyContent: "center",
    alignItems: "center",
    backgroundColor: "blue"
  }}
> 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到蓝色背景并没有占据整个屏幕，尽管这个 div 是目前应用程序中唯一的东西。

为了解决这个问题，将以下内容添加到`public/index.html` :

```
<style>
  html, body, #root, #root>div {
    height: 100%;
    margin: 0;
  }
</style> 
```

Enter fullscreen mode Exit fullscreen mode

这里有源代码:[https://github.com/mcrowder65/centered-cra-example](https://github.com/mcrowder65/centered-cra-example)
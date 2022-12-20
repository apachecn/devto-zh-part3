# 问题:救命！！！！React JS 显示空白白屏

> 原文：<https://dev.to/winniebosy/30-day-challenge-javascript-based-apps-dai>

* * *

在过去的几天里，我一直在不知疲倦地试图理解 React JS(一个 JavaScript 框架)中的一些概念。然而，框架的本地安装过程并不轻松，但是我成功地完成了安装过程。但是，我无法确定为什么我的浏览器上什么都没有显示。我已经尝试诊断错误，但没有成功。我不确定丢了什么。任何帮助都将不胜感激。谢了。

标签:- ReactJS，codenewbie，javascript，100daysofcode，womenwhocode，womenintech，肯尼亚

下面是我的 webpack.config.js 文件的代码

```
```javascript
  <pre>
<code>

 const webpack = require('webpack'); 
```

Enter fullscreen mode Exit fullscreen mode

module.exports = {
条目:'。/src/index.js '，
模块:{
规则:[
{
测试:/。(js|jsx)$/，
exclude: /node_modules/，
use:[' babel-loader ']
}
}
}，
resolve: {
扩展:['* '。js '，'。jsx']
}，
输出:{
路径:__dirname + '/dist '，
publicPath: '/'，
文件名:' bundle.js'
}，
插件:[
new webpack。HotModuleReplacementPlugin()
，
devServer:{
content base:'。/dist '，

}
}；

` `

下面是我的 package.json 文件的代码

```
 <pre>
    <code>

     {
  "name": "reactoneapp",
  "homepage": "",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "build": "webpack -p",
    "start": "webpack-dev-server --config ./webpack.config.js --mode development"
  },
  "keywords": [],
  "author": "Winnie Bosibori",
  "license": "ISC",
  "babel": {
    "presets": [
      "@babel/preset-env",
      "@babel/preset-react"
    ]
  },
  "devDependencies": {
    "@babel/core": "^7.4.5",
    "@babel/preset-env": "^7.4.5",
    "@babel/preset-react": "^7.0.0",
    "babel-loader": "^8.0.6",
    "jquery": "^3.4.1",
    "mongodb": "^3.2.7",
    "popper.js": "^1.15.0",
    "react-hot-loader": "^4.9.0",
    "webpack": "^4.33.0",
    "webpack-cli": "^3.3.3",
    "webpack-dev-server": "^3.7.1"
  },
  "dependencies": {
    "bootstrap": "^4.3.1",
    "react": "^16.8.6",
    "react-dom": "^16.8.6"
  },
  "description": ""
}

  </pre>
    </code>
     ```

**Below are the codes for index.html file**

```javascript
      <pre>
    <code> 
          <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    My First React App
</head>
<body>

    <div id ="app"></div>
    <script source = " ./bundle.js"></script>

</body>
</html>
 </code>
 </pre> 
```

Enter fullscreen mode Exit fullscreen mode

下面是 index.js 文件
的代码

```
 <pre>
    <code>
import React from 'react';
import ReactDOM from 'react-dom';

var greetings = React.createElement('h1', null, 'Hello Winnie!!')
var div1 = React.createElement('div', null, greetings);

ReactDOM.render(

    div1,
    document.getElementById('app')

); 

<code>
</pre> 
```

Enter fullscreen mode Exit fullscreen mode
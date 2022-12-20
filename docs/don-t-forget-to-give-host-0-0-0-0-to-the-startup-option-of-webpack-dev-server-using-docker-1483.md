# 不要忘记使用 docker 将“- host 0.0.0.0”赋予 webpack-dev-server 的启动选项

> 原文：<https://dev.to/hagevvashi/don-t-forget-to-give-host-0-0-0-0-to-the-startup-option-of-webpack-dev-server-using-docker-1483>

# 必须给容器中的 web pack-dev-server "-host 0 . 0 . 0 . 0 "选项

## TL；博士；医生

需要在 docker 容器中给 webpack-dev-server 的启动选项赋予“- host 0.0.0.0”，在 docker 容器中你的浏览器要通过 http 协议访问哪个服务器。

## [T1】简介](#intro)

我将使用 docker 开发 react 应用程序，因为我想保持开发环境的整洁。我的本地开发环境使用全球安装的 npm 包进行了高度定制，以增强开发体验。虽然编码很舒服，但我担心开发会受到这种包的影响，所以我觉得像 docker 容器这样的沙盒环境在真实环境中执行和测试应用程序是必要的。

## 概述我最初开发

### 目录结构

```
react-app
  -- src
    -- components
    -- index.html
    -- index.js
  -- Dockerfile
  -- docker-compose.yml
  -- package.json
  -- webpack.config.js
  -- yarn.lock 
```

Enter fullscreen mode Exit fullscreen mode

### Dockerfile

```
FROM node
WORKDIR /app
COPY package.json /app/package.json
COPY yarn.lock /app/yarn.lock
RUN yarn 
```

Enter fullscreen mode Exit fullscreen mode

### 码头工-化合物. yml

```
version: '3'
services:
  react:
    build: .
    ports:
      - 8642:8080
    command: yarn start
    volumes:
      - .:/app
    tty: true 
```

Enter fullscreen mode Exit fullscreen mode

### package.json

```
{
  ...
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack-dev-server --mode development --open --hot",
    "build": "webpack --config webpack.config.js --mode production",
    "serve": "http-server ./dist"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### webpack.config.js

```
const { resolve } = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  devtool: 'source-map',
  entry: './src/index.js',
  output: {
    path: resolve(__dirname, '/dist'),
    filename: 'index_bundle.js'
  },
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader'
        }
      }
    ]
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: `${resolve(__dirname, '/src/index.html')}`
    })
  ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 发生了问题

好了，我准备好了，所以检查服务器-输入“localhost:8642”到浏览器。我看到可怕的错误信息“无法访问此页面”。

## 看不到页面的原因

在第一种设置中，无法从外部访问服务器。

```
webpack-dev-server --mode development --open --hot 
```

Enter fullscreen mode Exit fullscreen mode

## 解

来自官方手册，`If you want your server to be accessible externally, specify it like this:`

```
webpack-dev-server --host 0.0.0.0 
```

Enter fullscreen mode Exit fullscreen mode

[https://web pack . js . org/configuration/dev-server/# devserver host](https://webpack.js.org/configuration/dev-server/#devserverhost)

## 最终设定

### package.json

```
 {
   ...
   "scripts": {
     "test": "echo \"Error: no test specified\" && exit 1",
-    "start": "webpack-dev-server --mode development --open --hot", +    "start": "webpack-dev-server --mode development --open --hot --host 0.0.0.0"
     "build": "webpack --config webpack.config.js --mode production",
     "serve": "http-server ./dist"
   }
 } 
```

Enter fullscreen mode Exit fullscreen mode

只需添加“- host 0.0.0.0”，我就可以在 docker 容器中看到 webpack-dev-server 提供的页面。

## 结论

总之，在 Docker 容器上创建服务器时，必须给 webpack-dev-server 一个“- host 0.0.0.0”选项。
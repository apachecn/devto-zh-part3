# 用一个命令运行 React-Frontend 和 Nodejs-Backend

> 原文：<https://dev.to/0xkoji/run-react-frontend-and-nodejs-backend-with-one-command-cia>

在前端框架出来之前，我们只需要运行后端，用浏览器打开`localhost`。

然而，现在我们需要运行前端和后端。我认为它们不是太多的工作，但是，我有时运行 4 个以上的程序并打开许多 iTerm 的标签。然后得到一个错误，因为我试图使用同一个端口，这完全是我的错误，但我有时想喊`WTF??????`

## 步骤

1.  并发安装
2.  向前端 package.json 添加代理
3.  修改 package.json 脚本

### step1 并发安装

我喜欢这个 npm 包，因为它很容易使用。

基本上，我用这个来一起运行几个东西。

例如，我的`npm run dev`是包含 tslint，构建并运行 webpack-server

```
$ npm install --save-dev concurrently 
```

Enter fullscreen mode Exit fullscreen mode

[https://www.npmjs.com/package/concurrently](https://www.npmjs.com/package/concurrently)

### 第二步添加代理

在这种情况下，nodejs(后端)使用端口 8080。

```
"proxy": "http://localhost:8080" 
```

Enter fullscreen mode Exit fullscreen mode

### step3 修改 package.json

这个案例使用`npm start`来启动前端和后端。

```
 "scripts": {
 "client": “cd client && npm start", "server": “cd server && npm start",
 "dev": “concurrently \"npm run server\"  \"npm run client\""
 }, 
```

Enter fullscreen mode Exit fullscreen mode

### 运行

```
$ npm run dev 
```

Enter fullscreen mode Exit fullscreen mode
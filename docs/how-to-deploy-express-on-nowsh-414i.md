# 如何在 Now.sh 上部署 Express

> 原文：<https://dev.to/warenix/how-to-deploy-express-on-nowsh-414i>

# 如何在 Now.sh 上部署快递

在这篇文章中，我将分享如何设置 express API 端点以在版本 2 的 [Now.sh](https://zeit.co/docs/v2/getting-started/introduction-to-now) 上运行。你将获得一个免费的 https 端点和运行在无服务器！是不是很酷？

你可以在 [github](https://github.com/warenix/express-now) 找到完整的源代码。

## 先决条件

*   现在的 CLI (12.1.9)
*   节点(10.10.0 版)
*   快速(4.16.4)

## 添加端点来表示

为了简单起见，我们将有两个端点来展示如何处理`GET`和`POST`请求。

## /get - GET

这会在 json 输出中返回`VERSION`。

编辑`index.js`

```
app.get("/get", (req, res, next) => {
    res.json({
        "version": process.env.VERSION
    });
}); 
```

## /post - POST

回显正在发布的 JSON 内容。

编辑`index.js`

```
app.post('/post', function(request, response) {
    response.send(request.body);
}); 
```

## 将秘密存储为环境变量

您可能已经注意到，在“/get”端点中，我们使用了`process.env.VERSION`。不在代码中硬编码秘密是一种常见的做法。

### 设置环境变量

```
export VERSION="1.0" 
```

## 部署到`now.sh`

### 为`now`设置构建

我们需要设置`build`来使用`@now/node-server`。(用`@now/node`就是不行)。修改`now.json`

```
"builds":  [{  "src":  "index.js",  "use":  "@now/node-server"  }] 
```

在[文档](https://zeit.co/docs/v2/deployments/official-builders/node-js-server-now-node-server/)阅读更多内容。

### 在`now.sh`中设置环境变量为秘密

```
now-linux secret add VERSION $VERSION 
```

在[文档](https://zeit.co/docs/v2/deployments/environment-variables-and-secrets/)阅读更多内容。

### 允许 CORS

这里我们需要添加定制的响应头。修改`now.json`

```
"routes":  [{  "headers":  {  "Access-Control-Allow-Origin":  "*",  "Access-Control-Allow-Methods":  "GET, POST, PUT, DELETE, OPTIONS",  "Access-Control-Allow-Headers":  "X-Requested-With, Content-Type, Accept"  },  "src":  "/.*",  "dest":  "/index.js"  }] 
```

在[文档](https://zeit.co/docs/v2/deployments/configuration/#routes)阅读更多内容。

### 推至`now.sh`

```
now-linux 
```

样本输出

```
❯ now-linux
> UPDATE AVAILABLE The latest version of Now CLI is 12.1.9
> Read more about how to update here: https://zeit.co/update-cli
> Changelog: https://github.com/zeit/now-cli/releases/tag/12.1.9
> Deploying ~/code/repo/github/express-now under XXXXXXX
> Synced 2 files (929B) [1s]
> https://express-now-3b57ke4d4.now.sh [v2] [in clipboard] [1s]
┌ index.js        Ready               [17s]
└── λ index.js (284.31KB) [sfo1]
> Success! Deployment ready [19s] 
```

## 测试

启动本地主机服务器。

```
npm start 
```

## 测试`/get`

在终端，

```
curl http://localhost:3000/get 
```

响应

```
{"version":"1.0"} 
```

## 测试`/post`

在终端，

```
curl -H "Content-Type: application/json" \
-d '{"message":"hello"}' \
http://localhost:3000/post 
```

响应

```
{"message":"hello"} 
```

注意:可以用 now.sh 实例 url 替换`localhost`。

## 明白了

也许由于无服务器的特性，有时端点会返回`502`错误。为了解决这个问题，请为您的服务调用方添加重试机制。
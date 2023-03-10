# 区分服务器/浏览器渲染结果的简单方法

> 原文：<https://dev.to/lucifer1004/simple-way-to-diff-serverbrowser-rendering-results-4d84>

> 我在 StackOverflow 找到了这个[答案](https://stackoverflow.com/questions/53571192/react-nextjs-how-to-debug-different-nodes-of-ssr-react-application)。我觉得有用，所以在这里分享给大家。

## 浏览器端

首先，你应该确保网站提供服务。然后，您可以:

*   在任何浏览器中打开您的站点
*   打开浏览器控制台，并键入:

```
console.log(document.documentElement.innerHTML) 
```

*   复制输出，并将其保存为`client.html`

## 服务器端

假设你的网站运行在`localhost:3000`上。

*   打开一个 shell，并键入:

```
curl localhost:3000 > server.html 
```

## 比较差异

你可以简单地运行:

```
diff client.html server.html 
```

> 请注意，您可能需要在 diff 之前格式化这两个文件。
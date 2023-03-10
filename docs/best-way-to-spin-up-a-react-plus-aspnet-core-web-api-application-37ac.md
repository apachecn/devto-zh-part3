# 启动 React plus ASP.NET 核心 Web API 应用程序的最佳方式是什么？

> 原文：<https://dev.to/jonhilt/best-way-to-spin-up-a-react-plus-aspnet-core-web-api-application-37ac>

希望创建一个具有 ASP.NET 核心后端的 React 前端吗？你有两个明确的选择。

1.  使用 dotnet React 模板
2.  为前端和后端创建独立的项目

## 选项 1 (dotnet React 模板)

该选项将创建一个包含 Web API 后端和 React 前端的项目。

它只需要一个简单的命令。

```
dotnet  new  react 
```

(或者您可以使用 Visual Studio 获得相同的结果)。

只要你使用的是最新版本的模板，目标是 ASP.NET 核心 2.1(或更高版本)，这将创建一个由两部分组成的单一项目。

1.  标准的 Create-React-App 前端(位于 ClientApp 文件夹中)
2.  一个标准的 ASP.NET 核心 Web API

[![](img/3899f0d11f20740093ba99f2de35b24e.png)](/img/2018-11-19-best-way-to-spin-up-a-react-plus-webapi-application/2018-11-19-20-04-36.png)

### 优点

*   如果你刚刚开始，这可能是最简单的选择
*   在应用程序的两个部分工作时，上下文切换更少
*   前端 React 部分是“标准的”(与使用“创建 React 应用程序”模板创建的任何其他 React 应用程序相同)
*   该模板包含一些示例代码，展示了一种从前端应用程序进行后端调用的方法

### 弊

*   所有的东西都放在一个文件夹中，这使得应用程序的两个部分分开变得更加困难。)
*   任何希望只在前端或后端工作的人仍然可以访问应用程序的其他部分

## 选项 2(单独的 React 和 Web API 项目)

这需要两步。

首先启动一个新的 Web API 项目。

```
dotnet  new  api 
```

(或者使用 Visual Studio“新建项目”向导)。

并单独创建一个 React 应用程序。

```
npx  create-react-app  <app-name> 
```

或者如果您喜欢使用 Typescript...

```
npx  create-react-app  <app-name>  --typescript 
```

这样，您将最终拥有两个独立的项目，一个 Web API...

[![](img/fae9dfdfe0eb38bd1e4ef3c08d1504bf.png)](/img/2018-11-19-best-way-to-spin-up-a-react-plus-webapi-application/2018-11-19-20-13-55.png)

和单独的 React app(本例中使用 Typescript)。

[![](img/174c10ae3218a944f75c6c0d63e3249e.png)](/img/2018-11-19-best-way-to-spin-up-a-react-plus-webapi-application/2018-11-19-20-14-24.png)

### 优点

*   关注点分离
*   不同的人/团队可以在每个部分(前端/后端)工作，而不会影响到彼此
*   您可以在启动 React 应用程序时指定 Typescript
*   应用程序的每个部分都可以单独部署，存储在不同的 Git 存储库中等。

### 弊

*   如果您正在构建“全栈”特性(在前端/后端之间跳转)，则需要更多的上下文切换
*   您可能需要启动 IDE/代码编辑器的两个实例来运行应用程序(另一个选项使用 CTRL+F5 运行)

## 真的，他们很相似

自从微软在 ASP.NET 2.1 中采用了官方的 create-react-app 模板后，这两个选项之间的差距已经缩小了不少。

真的归结为个人喜好。

两种选择都是可行的；在这两种情况下，您最终使用的 React 应用程序都是一样的。

总之，选择一个并着手开发你的应用程序可能比花太多时间在它们之间做决定更重要！

[想先拿到这些文章？点击此处，让我通过电子邮件将它们发送给您:-)](https://jonhilton.net/devto/next-react-post/)
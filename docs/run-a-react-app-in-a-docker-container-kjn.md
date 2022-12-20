# Docker React 在 Docker 容器中运行 React 应用程序

> 原文：<https://dev.to/peterj/run-a-react-app-in-a-docker-container-kjn>

以下简单的步骤向您展示了如何从一个空的 React 应用程序开始(使用 [create-react-app](https://facebook.github.io/create-react-app/) ),创建该应用程序的生产版本，然后在 Docker 容器中运行它。

让我们从创建一个新的 React 应用程序开始:

1.  安装创建-反应-应用

```
npm install create-react-app --global 
```

Enter fullscreen mode Exit fullscreen mode

1.  创建新的 React 应用程序:

```
create-react-app react-docker-app 
```

Enter fullscreen mode Exit fullscreen mode

1.  转到`react-docker-app`文件夹并运行它，以确保一切正常:

```
cd react-docker-app && yarn start 
```

Enter fullscreen mode Exit fullscreen mode

> `yarn start`命令编译 React 应用并打开浏览器。

现在我们已经运行了应用程序，让我们在项目的根文件夹中创建一个`Dockerfile`。下面是`Dockerfile`的内容:
# 管理。使用 Create React 应用程序临时构建的环境变量

> 原文：<https://dev.to/jam3/managing-env-variables-for-provisional-builds-h37>

当使用 Create React App 开发 web 应用程序时，默认情况下，开发人员在本地环境中获得`NODE_ENV=development`，在生产版本中获得`NODE_ENV=production`。并且，禁止修改`NODE_ENV`。根据 Create React 应用程序，这是一个有意的设置，以保护`production`环境免受错误/意外部署的影响。

创建您的 web 应用程序后，您将能够在`package.json`中看到如下脚本。

```
// package.json
scripts: {
  "start": "react-scripts start", // `NODE_ENV` is equal to `development`.
  "build": "react-scripts build", // `NODE_ENV` is equal to `production`.
  ...
} 
```

如果您在项目的根目录中创建或者已经有了`.env.development`和`.env.production`，这些文件将用于运行每个脚本。`npm start`会拿起`.env.development`，`npm build`会在`.env.production`使用环境变量。

-
**如果要设置`.env.staging`怎么办？**

本文将向您展示如何管理临时构建的环境变量。

让我们开始吧！哦，如果你没有任何 CRA 的经历，请[开始](https://facebook.github.io/create-react-app/docs/getting-started)

-

## **故事**

假设您的项目将有三个独立的临时环境；`development`、`staging`和`production`。每个环境都使用不同的 API 端点。除此之外，项目可能需要一个`REACT_APP_CUSTOM_NODE_ENV`。这是因为无论如何,`NODE_ENV`将永远是`production`。

-

## **目标**

*   创建`.env.development`、`.env.staging`和`.env.production`。
*   为每个构建配置环境虚拟化。
*   修改`package.json`中的脚本

-

## **入门**

### **第一步。**

谢天谢地，`dotenv`从盒子里出来了。让我们在根文件夹下创建`.env`文件来管理环境变量。这些文件是`.env`、`.env.development`、`.env.staging`和`.env.production`。

*   `.env` -保留所有公共/共享环境变量
*   `.env.development` -变量用于本地开发
*   `.env.staging` -变量用于分段构建
*   `.env.production` -变量用于生产构建

比如说；

```
#.env
REACT_APP_DOC_TITLE = "Document title" 
```

```
//.env.developement
REACT_APP_API_ENDPOINT = "https://development-api.endpoint.com/" 
```

```
#.env.staging

# NODE_ENV will always be set to "production" for a build
# more details at https://create-react-app.dev/docs/deployment/#customizing-environment-variables-for-arbitrary-build-environments
REACT_APP_CUSTOM_NODE_ENV = "staging"
REACT_APP_API_ENDPOINT = "https://staging-api.endpoint.com/" 
```

```
#.env.production
REACT_APP_API_ENDPOINT = "https://api.endpoint.com/" 
```

注意:创建自定义环境变量时，前缀`REACT_APP_`是必需的。

#### `.env.development`和`.env.production`

作为默认行为，这些文件将在没有配置的情况下提供。您甚至不必更新`package.json`中的脚本

#### `.env.staging`

这是这篇文章的重点。为了将`.env.staging`文件作为分段构建的目标，我们需要一个库来实现这个目标。

1-让我们安装`env-cmd`。这个库将帮助我们使用/执行一个选定的环境文件。[查看更多详情](https://www.npmjs.com/package/env-cmd)

```
// execute command below at the root of project
$ npm install env-cmd --save
or
$ yarn add env-cmd 
```

2-在`package.json`中添加一个脚本，如下所示。

```
// package.json
scripts: {
  "start": "react-scripts start", // `NODE_ENV` is equal to `development`.
  "build": "react-scripts build", // `NODE_ENV` is equal to `production`.
  "build:staging": "env-cmd -f .env.staging react-scripts build", // `NODE_ENV` is equal to `production`.
  ...
} 
```

3-最后，测试你的`build:staging`脚本。

-

## **结论**

这种技术的目的是为许多临时环境使用不同的自定义环境变量，而不排除默认的 CRA 配置。

-

## **资源**

*   [为任意构建环境定制环境变量](https://facebook.github.io/create-react-app/docs/deployment#customizing-environment-variables-for-arbitrary-build-environments)

*   [添加自定义环境病毒](https://facebook.github.io/create-react-app/docs/adding-custom-environment-variables)

*   [dotenv](https://www.npmjs.com/package/dotenv)

## **特别感谢**

*   [@foxbit19](https://dev.to/foxbit19) -发现`env-cmd`8 . x 版需要额外的参数来链接到自定义的 env 文件。
*   [@j6000](https://dev.to/1970smthin) -指出`NODE_ENV`无论如何都会被设置为“生产”。
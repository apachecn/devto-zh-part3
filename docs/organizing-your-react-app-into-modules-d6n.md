# 将 React 应用组织成模块

> 原文：<https://dev.to/jack/organizing-your-react-app-into-modules-d6n>

第一次张贴在这里，长期读者。觉得是时候回报了。

在我八年的软件开发生涯中，最后两年我做的是 React 开发。来自香草 Javascript 和 jQuery，让我说——我恋爱了。

这篇文章将详细介绍我们是如何组织 React 应用的。

这篇文章还假设你已经知道如何设置和使用 React(也就是说，这不是一篇入门文章)。

我们开始吧！

## [T1】简介](#intro)

我厌倦了 todos——所以我们虚构的 shell 应用程序是游戏工作室的错误跟踪器，允许 alpha 和 beta 测试人员报告错误。

它有四个模块:仪表板，游戏，用户，分析。

[![Screenshot of fictional app](img/c596171ad0c4ac2b9d7164551d839e9d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IMgOOlaU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/jackjwilliams/dev-react-modules/raw/master/dev-react-modules-screenshot.png)

这种方法的优点是父应用程序不知道子模块的具体信息。它只知道*有*模块。每个模块管理自己的位置和数据。你不必在父应用程序的任何地方添加`<Route />`或`<Link></Link>`——子应用程序定义了这一点。

这篇文章的代码在 GitHub 上。

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [杰克威廉姆斯](https://github.com/jackjwilliams) / [开发-反应-模块](https://github.com/jackjwilliams/dev-react-modules)

### 如何使用模块组织 react 应用程序的示例

<article class="markdown-body entry-content p-5" itemprop="text">

这是我的文章[将 React 应用组织成模块](https://dev.to/jack/organizing-your-react-app-into-modules)的示例库。

它展示了如何使用模块进行 React 开发。

我们正在构建的虚构 shell 应用程序是游戏工作室的一个 bug 追踪器，允许 alpha 和 beta 测试人员轻松报告 bug。

这个项目是用 [Create React App](https://github.com/facebook/create-react-app) 引导的。

## 可用脚本

在项目目录中，您可以运行:

### `npm start`

在开发模式下运行应用程序。
打开 [http://localhost:3000](http://localhost:3000) 在浏览器中查看。

如果您进行编辑，页面将重新加载。您还会在控制台中看到任何 lint 错误。

### `npm test`

在交互式观察模式下启动测试运行程序。
详见[运行测试](https://facebook.github.io/create-react-app/docs/running-tests)章节。

### `npm run build`

将生产应用程序构建到`build`文件夹中。
它正确地将 React 捆绑到生产模式中，并优化构建以获得最佳…

</article>

[View on GitHub](https://github.com/jackjwilliams/dev-react-modules)

## 入门

让我们开始一些实际的编码吧！

如果没有 create-react-app，用`npm install -g create-react-app`安装。然后...

`create-react-app dev-react-modules`
`cd dev-react-modules`
`npm install react-router-dom --save`

我不会详述应用的样式，你可以在 [GitHub repo](https://github.com/jackjwilliams/dev-react-modules) 中查看。

## 创建模块

在 src 文件夹下，我们首先创建我们的模块结构。它看起来像这样:

*   模块
    *   分析学
    *   仪表盘
    *   比赛
    *   用户

在每个模块的文件夹中，添加一个 index.js

### [T1】src \ modules \ Analytics \ index . js](#srcmodulesanalyticsindexjs)

```
import React from 'react';

const Analytics = () => (
    <div>Analytics Module</div>
);

export default {
    routeProps: {
        path: '/analytics',
        component: Analytics
    },
    name: 'Analytics',
} 
```

### [T1】src \ modules \ Dashboard \ index . js](#srcmodulesdashboardindexjs)

```
import React from 'react';

const Dashboard = () => (
    <div>Dashboard Module</div>
);

export default {
    routeProps: {
        path: '/',
        exact: true,
        component: Dashboard,
    },
    name: 'Dashboard',
}; 
```

### [T1】src \ modules \ Games \ index . js](#srcmodulesgamesindexjs)

```
import React from 'react';

const Games = () => (
    <div>Games Module</div>
);

export default {
    routeProps: {
        path: '/games',
        component: Games,
    },
    name: 'Games',
}; 
```

### src \ modules \ Users \ index . js

```
import React from 'react';

const Users = () => (
    <div>Users Module</div>
);

export default {
    routeProps: {
        path: '/users',
        component: Users,
    },
    name: 'Users',
}; 
```

这里没有什么特别的，我们已经创建了我们的模块和它们的默认导出。但是不是*只导出一个组件*——让父组件来编排事情——我们导出**模块存在所需要的一切**。这可以扩展到包括模块主题、导航图标、必需的权限等...

我喜欢这一点是因为我不必更改父模块来添加模块。我只是...添加一个模块。

让我们来分解一下导出，我在下面添加了一些注释。

```
export default {
    routeProps: { // This gets passed straight to react-router
        path: '/users', // Where the module lives in the nav hierarchy
        component: Users, // The actual component itself
    },
    name: 'Users', // The name of the module
}; 
```

您可以将导出结构想象成父模块和子模块之间的契约。家长说我不在乎我有多少模块，我只需要这些东西来渲染你。

现在我们需要导出所有这些模块。在 modules 文件夹中，创建 index.js。

### src\modules\index.js

```
import Analytics from './Analytics';
import Dashboard from './Dashboard';
import Games from './Games';
import Users from './Users';

export default [
    Dashboard,
    Analytics,
    Games,
    Users
]; 
```

在这里，我们正在导出模块的列表。这正是父母所需要的。

## 创建父应用

现在我们的子模块都完成了，让我们把它们都放在主 App.js 中。

### src\App.js

```
import React from 'react';
import { useState } from 'react';
import { BrowserRouter as Router, Route, Link } from "react-router-dom";
import logo from './logo.svg';
import './App.css';

import modules from './modules'; // All the parent knows is that it has modules ...

function App() {
  const [currentTab, setCurrentTab] = useState('dashboard');

  return (
      <Router>
        <div className="App">
          <header className="App-header">
            <img src={logo} className="App-logo" alt="logo" />
            <ul className="App-nav">
              {modules.map(module => ( // with a name, and routes
                  <li key={module.name} className={currentTab === module.name ? 'active' : ''}>
                    <Link to={module.routeProps.path} onClick={() => setCurrentTab(module.name)}>{module.name}</Link>
                  </li>
              ))}
            </ul>
          </header>
          <div className="App-content">
            {modules.map(module => (
              <Route {...module.routeProps} key={module.name} />
            ))}
          </div>
        </div>
      </Router>
  );
} 
```

我们来分解一下。

`import modules from './modules';`

就像我之前说的，所有的父类需要知道的是它有模块。在这里，我们进口它们。

```
<ul className="App-nav">
  {modules.map(module => (
      <li key={module.name} className={currentTab === module.name ? 'active' : ''}>
        <Link to={module.routeProps.path} onClick={() => setCurrentTab(module.name)}>{module.name}</Link>
      </li>
  ))}
</ul> 
```

在这里，父模块知道这个模块有一个名字和一个链接(因为它是一个契约，记得吗？)，所以它可以动态构建导航菜单。

```
<div className="App-content">
  {modules.map(module => (
    <Route {...module.routeProps} key={module.name} />
  ))}
</div> 
```

这里，父模块也知道模块有一个带组件的路由，所以它可以动态地呈现`<Route />`的。

现在您有了一个自组织、模块化的 React 应用程序。

但是等等，还有呢！

## 添加新模块

我们遗漏了我们的 bug 追踪器的一个关键模块:bug。

我们新结构的美妙之处在于，我所要做的就是在导出列表中添加一个新模块。

### src \ modules \ Bugs \ index . js

```
import React from 'react';

const Bugs = () => (
    <div>Bugs Module</div>
);

export default {
    routeProps: {
        path: '/bugs',
        component: Bugs,
    },
    name: 'Bugs',
}; 
```

### src\modules\index.js

```
import Analytics from './Analytics';
import Bugs from './Bugs'; // added
import Dashboard from './Dashboard';
import Games from './Games';
import Users from './Users';

export default [
    Dashboard,
    Games,
    Bugs, // added
    Users,
    Analytics,
]; 
```

## 结论

我使用这种结构已经有几年了，非常喜欢。它在我们的应用程序中扩展了很多，但是我想保持这篇文章的简单。

这也不是我的功劳。几年前开始使用 React 时，我有幸与一位资深 React 专业人士一起工作。他教了我这个结构(并继续教我好的反应实践)。我喜欢向其他开发者学习！

想法，问题，建议？你如何组织你的 React 项目？
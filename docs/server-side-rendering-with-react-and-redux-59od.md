# 使用 React(和 Redux)的服务器端渲染

> 原文：<https://dev.to/markusclaus/server-side-rendering-with-react-and-redux-59od>

当我第一次开始 react 时，我对这个库的强大感到惊讶。我构建了一个又一个组件，当我看到脸书的共享组件时，我注意到“哦，页面上根本没有内容！”

这可不好。是吗？那是怎么回事？

大多数自动化的网站爬虫在阅读网站之前不会执行 JavaScript。(谷歌可能会，但我无法证实)。使用 React，在 JavaScript 执行之前，页面上没有任何内容。这意味着爬虫看不到你网站上的任何东西。

此外，React 并不是让访问者满意的最快方式。如果您查看浏览器开发工具中的 network 选项卡，您会注意到它请求页面，然后执行 JavaScript 包——在大多数情况下，在此之后，React 应用程序请求真正的内容。

在访问者可以看到任何内容之前，会有大量的呼叫发生。

那么，考虑到这两个问题，我们能做些什么呢？

# 服务器端渲染概括地说

有一种超级酷的技术叫做服务器端渲染。它所做的非常简单:它在服务器上执行 React 应用程序，并将 HTML 结果发送到访问者的客户端。客户端呈现 HTML，然后请求您的 JavaScript。它不是再次渲染应用程序，而是将已经发送的 HTML 与 React 应用程序中的所有功能进行融合。

使用这种方法，内容不需要客户端执行任何 JavaScript 来显示。此外，你不需要三次请求内容才能到达。访问者应该看到的所有内容都随初始调用一起发送。

# 做好准备

上面的解释听起来很简单。不幸的是，事实并非如此。这个实现有一些我们需要理解的复杂部分，但是我尽量让它们简单明了。

在我们开始之前，你需要安装`webpack, webpack-cli, webpack-merge, webpack-node-external, express, babel, babel-preset-env, babel-preset-react, babel-loader`，当然还有`react`和`react-dom`。现在应该可以了。

现在我们需要设置 Webpack。Webpack 是一个模块捆绑器。我想，你们大多数人都在使用它，或者至少听说过它。我不想深究 webpack，它足够大，可以单独写一篇文章(...或者五个)。

出于服务器端渲染的目的，我们将为 webpack 使用更复杂的设置。我们将把 webpack 配置分成多个文件，因此我们不需要重复太多代码。

最后，我们需要两个 webpack 设置。一个用于我们的服务器端代码，一个用于客户端。这两个文件有很多共同点，所以我们创建一个名为`webpack.base.js`的文件，它包含了两个设置共享的大部分配置。

```
// webpack.base.js

module.exports = {
  stats: {
    colors: true,
    reasons: true,
    chunks: false
  },
  module: {
    rules: [
      {
        test: /\.jsx?$/,
        loader: 'babel-loader',
        exclude: /node_modules/
      },
    ]
  }
} 
```

您在这里看到的是 webpack 的标准配置。您导出一个 JavaScript 对象，其中包含 webpack 的几个配置信息。stats 部分是对 webpack 输出的一些修正，module 部分告诉 webpack 当遇到满足“测试”正则表达式的文件时应该使用什么类型的加载器。加载程序是一个以某种方式转换文件内容的函数。

好吧，我说的不止一个陷阱。让我们先做服务器端，因为我们很快就要开始在服务器上工作了。

```
// webpack.server.js

const path = require('path');
const merge = require('webpack-merge');
const baseConfig = require('./webpack.base.js');

const config = {
    target: 'node',
    entry:  ['./index.js'],
    output:  {
        filename: 'server.js',
        path: path.resolve(__dirname, 'build'),
    },
    resolve: {
        extensions: ['.js', '.json']
    },
}

module.exports = merge(baseConfig, config); 
```

这是我们的服务器端配置，就在文件的末尾，您可以看到，我们将来自我们的`webpack.base.js`文件的 baseConfig 对象与该文件中的内容合并，以获得我们最终的服务器端 webpack 配置。

entry 键告诉 webpack 应该从哪里开始寻找代码。output 键告诉它捆绑文件应该保存到哪里。

在进入我们的服务器应用程序之前，让我们快速设置一下巴别塔:

```
// .babelrc

{
    "presets": [
        "react",
    ["env", {
        "targets": {
            "browsers": "last 2 versions"
        },
            "useBuiltIns": "usage",
        "loose": true,
        "modules": false
        }]
    ],

} 
```

我不能详细说明这是干什么的。文章会变得太大。简而言之，它建立了巴别塔，以了解常见的反应材料。

让我们向 npm 介绍一下 webpack 和我们刚刚创建的设置。在你的`package.json`的脚本部分添加:

```
// package.json add to scripts

{
    scripts: {
        ...
        "server": "webpack --watch --config webpack.server.js --mode development"
    }
} 
```

# 让我们得到编码

下一步是建立某种服务器来响应我们的访问者的请求。我将在这篇文章中使用 Express，但是你可以使用任何你想要的服务器。

让我们使用`npm install express`安装 Express。

之后我们建立了一个服务器:

```
// index.js

import express from 'express';
const app = express();

app.use(express.static('public'));

app.get('*', (req, res) => {
    // Here we are going to handle that react rendering and stuff.
});

app.listen(3000, () => {
    console.log('Server is listenting to port 3000');
}); 
```

我们所做的——我们设置了一个快速服务器，它监听端口 3000。我们添加了`public`作为静态资源的文件夹。我们还创建了一个带有通配符的 GET 路由。在这种情况下，这意味着每个呼叫都与该路由匹配。

你可能已经注意到我通过`import`而不是`require`引入 Express。那是因为我们想写同构的 JavaScript。这就是无需修改即可在服务器和客户端运行的 JavaScript。

现在我们想创建一些我们可以在服务器上渲染的东西。让我们做一些主页视图组件，用户在访问我们的应用程序时应该会看到。

```
// client/Homepage.js

import React from 'react';

class Homepage extends React.Component {

    render() {
        return (
            <div class="wrapper">
                <h1>Welcome, this is the homepage</h1>
                <button onClick={() => console.log("I', clicked!"}>
                    Click me!
                </button>
            </div>
        )
    }

}

export default Homepage; 
```

很简单，不是吗？这里我唯一要注意的是，我将所有的客户端代码都放在了一个文件夹`client`中。人们，永远保持你的代码排序良好！

我们有我们的服务器，我们有我们的 React 组件，让我们把它们放在一起。

```
// renderer.js

import { renderToString } from 'react-dom/server';
import Homepage from './client/Homepage';

function renderer() {

    const content = renderToString(
        <Homepage />
      );

    return content;
}

export default renderer; 
```

为了保持一切整洁，我们将渲染代码放入一个名为 renderer 的函数中。可以看到，它从 react-dom/server 导入了一个名为`renderToString()`的特殊函数。渲染到字符串类似于你可能知道的`render()`。它一次性呈现组件，并将其转换为 HTML 字符串。

之后，我们调用 Express 服务器中的`renderer()`,并将结果发送回访问者。

```
// index.js

import express from 'express';
import renderer from './renderer';

const app = express();

app.get('*', (req, res) => {
    const result = renderer();
    res.send(result);
});

app.listen(3000, () => {
    console.log('Server is listenting to port 3000');
}); 
```

瞧，服务器上第一个呈现的 React 组件。你会注意到，无论你调用哪条路由，每次都会渲染主页组件。现在没问题，我们一会儿就解决这个问题。现在高兴起来吧，我们已经有了服务器端渲染并开始运行。

# 我的 JavaScript 去哪了？！？

你可能尝试过使用主页上的`button`,你可能已经注意到“哦，没有控制台日志...我的 JavaScript 去哪了！？!"。

因为`renderToString()`就没了。它呈现一个 HTML 字符串，但是它删除了 JavaScript 的所有内容。为了解决这个问题，我们需要为我们的应用程序创建一个客户端包，并在收到 HTML 代码后立即从访问者客户端请求它。

要做到这一点，让我们回到 Webpack。我们需要前面提到的客户端设置。我们这样做是因为一些服务器代码在客户端是不必要的，或者可能我们有一些超级机密的业务逻辑，我们不想分享，或者可能是 API 的秘密...你明白了。

```
// webpack.client.js

const webpack = require('webpack');
const path = require('path');
const merge = require('webpack-merge');
const baseConfig = require('./webpack.base.js');

const config = {
    context: __dirname,
    entry: ['./client/index.js'],
    output: {
        path: path.join(__dirname, 'public'),
        filename: 'bundle.js',
    publicPath: '/',
    pathinfo: false,
    },
    resolve: {
        extensions: ['.js', '.json', '.scss']
    },
    optimization: {
        minimize: true
    },
};

module.exports = merge(baseConfig, config); 
```

与之前的服务器端一样，我们构建一个客户端配置，然后将其与我们的基本配置合并。需要注意的是，我们使用了另一个入口点，并创建了另一个输出文件。因此，在两种配置下运行 webpack 后，我们得到了两个文件:`server.js`和`bundle.js`

要启动 webpack，我们需要向 npm 添加一个新脚本:

```
// package.json add to scripts

{
    scripts: {
        ...
        "client": "webpack --watch --config webpack.client.js --mode development"
    }
} 
```

为了让 webpack 真正构建我们的`bundle.js`,我们需要首先编写它。那么我们开始:

```
// client/index.js

import React from 'react';
import ReactDOM from 'react-dom';
import Homepage from './homepage';

const jsx = <Homepage />

ReactDOM.hydrate(jsx, document.getElementById('app')); 
```

对于 React 应用程序来说，这是一个非常标准的设置。我们使用我们的主页组件，并将其渲染到一个 ID 为“app”的 div 中。因为我们在这里讨论的是客户端，所以当这段代码到达时，页面已经呈现出来了。为了让 React 理解，我们不希望它重新呈现整个事情，我们需要使用`hydrate`而不是`render`来告诉 React 只是在我们的 HTML 代码中加入一些生命。

现在我们有了 JavaScript 文件来服务客户端，但是当我们刷新页面时，这个文件仍然没有显示出来。这是因为我们需要告诉客户端实际请求`client.js`文件。为此，我们向我们的`renderer()`函数添加了一点代码:

```
// renderer.js

import { renderToString } from 'react-dom/server';
import Homepage from './client/Homepage';

function renderer() {

    const content = renderToString(
        <Homepage />
      );

    const jsx = `
        <!DOCTYPE html>
        <html>
        <head>
            <meta charset="utf-8">
            <meta name="viewport" content="width=device-width, initial-scale=1">
        </head>

        <body>
            <div id="app">${content}</div>
            <script src="/bundle.js"></script>
        </body>
        </html>
    `;

    return jsx;
}

export default renderer; 
```

我们的渲染器返回的不再是简单的 React 组件，而是一个包含了`bundle.js`的完整网站。一旦客户端呈现 HTML 代码，它将请求我们的 JavaScript 和整个代码，您想要创建的按钮将开始工作。或者至少应该如此；)

# 路由器，到处都是路由器！

现在，我们已经在前端获得了一个由服务器渲染的 React 应用程序，我们可以讨论路由问题了。你还记得吗？无论我们使用什么途径，我们总是最终在我们的主页上。为了解决这个问题，我们安装了一个叫做`react-router`的工具。我想你们知道一个`BrowserRouter`是如何工作的。问题是...我们不能使用 BrowserRouter，因为它专门用于根据浏览器 url 呈现路线。但是服务器上没有浏览器。所以我们现在有点麻烦。

我们要做的是，我们使用一个静态路由器作为服务器部分，使用一个好的浏览器路由器作为前端。

但是每当我们在软件中添加一个新的路径时，我们都不想写两个单独的路径声明，不是吗？

因此，让我们将所有路由放在一个文件中，并用它来配置两台路由器。为此，有一个叫做`react-router-config`的小工具。它允许我们编写一个配置数组，并提供一个函数来呈现路由。

```
// routes.js

import Homepage from './client/Homepage';
import AboutUs from './client/AboutUs';

const routes = [
    {
        component: Homepage,
        path: "/",
        exact: true
    },
    {
        component: AboutUs,
        path: "/",
        exact: true
    }    
]

export default routes; 
```

数组中的每个对象都接受与来自`react-router`的`Route`组件相同的参数。

在我们的客户端代码中，我们现在可以做以下事情来获得我们的浏览器:

```
// client/index.js

import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter as Router } from 'react-router-dom';
import { renderRoutes } from 'react-router-config';

import routes from '../routes.js';

const jsx = (
    <Router>
      <div class="wrapper">{renderRoutes(routes)}</div>
    </Router> );

ReactDOM.hydrate(jsx, document.getElementById('app')); 
```

我们使用来自`react-router-config`的
`renderRoutes`，并将其传递给我们的 routes 数组。结果将是一个完全呈现的`Route`组件包。

服务器端稍微复杂一点。这里我们使用`StaticRouter`。静态路由器没有浏览器 url，所以我们需要向它传递一个请求，在那里它可以获得我们想要呈现的路由的必要信息。

请求从我们的 express 应用程序传递到我们之前编写的用于呈现网站的`renderer`函数中。

```
// renderer.js

import { renderToString } from 'react-dom/server';
import { StaticRouter as Router } from 'react-router-dom';
import { renderRoutes } from 'react-router-config';

import routes from './routes.js';

function renderer(req) {

    const context = {};

    const content = renderToString(
        <Router context={context} location={req.path} query={req.query}>
            <div>{renderRoutes(routes)}</div>
        </Router>
    );

    const jsx = `
        <!DOCTYPE html>
        <html>
        <head>
            <meta charset="utf-8">
            <meta name="viewport" content="width=device-width, initial-scale=1">
        </head>

        <body>
            <div id="app">${content}</div>
            <script src="/bundle.js"></script>
        </body>
        </html>
    `;

    return {jsx, context};
}

export default renderer; 
```

如你所见，我们做的和客户端几乎一样，但是我们使用了`StaticRouter`。我们将请求中的位置传递给它，这样它就可以决定做什么。我们还给它一个查询，这样我们就可以对请求中的参数做出反应。最后是语境。可以在路由器呈现的组件中访问上下文。您可以使用它进行错误处理或设置所呈现页面的状态。

例如，假设您想从一个 API 加载一个产品，而请求的产品不在那里。您希望服务器的初始呼叫用 404 状态码而不是 200 来应答。这就是你可以使用上下文的地方。在组件中只需设置`context.status = 404`并在渲染后设置`res.status(context.status)`如下:

```
// index.js

import express from 'express';
import renderer from './renderer';

const app = express();

app.get('*', (req, res) => {
    const result = renderer(req);
    const context = result.context;
    if(context && context.status !== undefined) {
        res.status(context.status);
    }
    res.send(result.jsx);
});

app.listen(3000, () => {
    console.log('Server is listenting to port 3000');
}); 
```

我们开始吧。您的应用程序中的路由有效。客户端和服务器端。创建新路由时，您只需将它们添加到 routes 数组中，两台路由器都知道该做什么。

# 把 Redux 打开

你现在头疼吗？没有吗？很好。因为我救了最讨厌的(对，讨厌！)最后一部分:Redux。

事实上，我最后想谈的不仅仅是 Redux，我还想谈一谈在服务器端呈现 React 应用程序时的各种异步数据获取。正如您所记得的，问题是我们使用`renderToString`来预呈现我们的页面，然后将其发送给客户端。但是`renderToString`只渲染一次，因此不关心在渲染之前我们可能需要从 API 获取的数据。但是我们希望搜索引擎和其他工具能够拥有我们所缺少的那些有价值的数据。

所以我们需要做的是，在我们真正调用`renderToString`之前，获取渲染一个完整视图所需的所有数据。因此，对于我们需要放在一起的每个视图，某种“内容获取”功能会加载所请求视图需要的每一条内容。

然后，在内容可用后，我们需要将数据传递给我们的 React 应用程序，然后，在一切可用的情况下，调用`renderToString`。

首先，我们将 Redux 放入组合中:`npm install redux react-redux redux-thunk`

现在我们已经安装了 Redux，让我们首先做简单的部分，前端。

我们将在这里使用某种非标准的方法，但你最终会明白我们为什么这样做。

```
 // createStore.js

import { applyMiddleware, createStore } from 'redux';
import thunk from 'redux-thunk';

import rootReducer from './rootReducer';

const middleware = applyMiddleware(thunk);

export default initialState => createStore(rootReducer, initialState, middleware); 
```

我们编写了一个函数，它采用 initialState 并返回创建的存储。我们将在客户端和服务器端使用它。

```
// client/index.js

import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter as Router } from 'react-router-dom';
import { renderRoutes } from 'react-router-config';

import {Provider} from 'react-redux';

import createStore from '../createStore';
import routes from '../routes';
import rootReducer from '../rootReducer';

const store = createStore({});

const jsx = (
    <Provider store={store}>
        <Router>
          <div class="wrapper">{renderRoutes(routes)}</div>
        </Router>
    </Provider> );

ReactDOM.hydrate(jsx, document.getElementById('app')); 
```

我们用新的`createStore()`功能创建了一个商店，并将应用程序包装在一个`Provider`中。

为了让一切按照我们的计划进行，我们需要在完成数据加载过程后，获得视图渲染所需的所有数据。只要有一个视图组件，所有的数据都被加载，然后传递给子组件，就很容易发现我们需要加载什么。如果您的视图中有许多自己加载数据的组件，那么很难不忘记这里或那里的一些读取。

为了给我们一个加载数据的机会，我们需要一个加载数据的函数，这样我们就可以在渲染之前调用它。为了实现这一点，我们需要稍微重写我们的视图组件。

我添加了一个到 Redux 存储的连接。我不想谈论如何做到这一点以及这一切意味着什么，我一周前写了一篇关于它的文章。

[![markusclaus image](img/ff73ad658ced7822534fc007aa031004.png)](/markusclaus) [## 使用 React/Redux 从 api 获取数据

### 马库斯·克劳斯 3 月 5 日 197 分钟阅读

#react #javascript #beginners #webdev](/markusclaus/fetching-data-from-an-api-using-reactredux-55ao)

随意看！
T3】

```
// client/Homepage.js

import React from 'react';
import {bindActionCreators} from 'redux';
import {connect} from 'react-redux';

import fetchSomeData from './somewhere';

class Homepage extends React.Component {
    componentDidMount() {
        const {fetchSomeData: doFetch} = this.props;
        doFetch();
    }

    render() {
        return (
            <div class="wrapper">
                <h1>Welcome, this is the homepage</h1>
                <button onClick={() => console.log("I', clicked!"}>
                    Click me!
                </button>
            </div>
        )
    }

}

const mapStateToProps = state => ({
    someData: getSomeData(state);
})

const mapDispatchToProps = dispatch => bindActionCreators({
    fetchSomeData,
}, dispatch);

function loadData(store, match, cookie) {
    const actionToBeDispatched = [];
    actionToBeDispatched.push(store.dispatch(fetchSomeData()));

    return Promise.all(actionsToBeDispatched);
}

export default {
  loadData,
  component: connect(mapStateToProps, mapDispatchToProps)(Homepage),
}; 
```

在这个代码示例中，有两件事非常重要。首先我们得到了`loadData()`函数。它的目的是加载我们需要的每一位数据。`loadData()`返回一个`Promise`，让我们知道什么时候一切都完成了。

此外，我们不像以前那样导出组件，而是导出一个带有`loadData()`的对象和组件本身。这个对象将在我们的路由中被分割，因此每个路由仍然有一个组件，但也有一个 loadData 键。

```
// routes.js

import Homepage from './client/Homepage';
import AboutUs from './client/AboutUs';

const routes = [
    {
        ...Homepage,
        path: "/",
        exact: true
    },
    {
        ...AboutUs,
        path: "/",
        exact: true
    }    
]

export default routes; 
```

好的，让我们把所有的东西放在一起！

```
// index.js

import express from 'express';
import { matchRoutes } from 'react-router-config';
import renderer from './renderer';
import createStore from './createStore';

const app = express();

app.get('*', (req, res) => {
    const store = createStore({});
    const promises = matchRoutes(routes, req.path).map(
        ({ route, match }) => (route.loadData ? route.loadData(store, match,             req.get('cookie') || {}, req.query) : null)
    );

    Promise.all(promises).then(() => {
        const result = renderer(req, store);
        const context = result.context;
        if(context && context.status !== undefined) {
            res.status(context.status);
        }
        res.send(result.jsx);
    });
});

app.listen(3000, () => {
    console.log('Server is listenting to port 3000');
}); 
```

在我们的路线中，我们使用之前写的`createStore`创建了一个商店。然后我们使用一个名为`matchRoutes()`的函数，它接受我们的 routes 数组和请求路径。它返回匹配路由的数组。每一项都包含来自`react-router`的路线和比赛数据。

这样我们就可以调用 routes 对象中的`loadData()`函数，并等待它完成数据加载。之后，我们将存储传递给我们的`renderer()`函数，以正确地显示所有内容。

本例中的`loadData()`接受一个 cookie 和一个查询参数。它们不是必需的，但是我决定把它们留在那里，这样你就可以看到你是如何把 cookie 或者查询参数添加到你的`loadData()`函数中的。

我们快完成了。最后要做的是确保客户机在加载时与服务器处于相同的状态。现在，服务器将正确设置所有内容，然后将其发送给客户端。但是客户端会把事情弄糟，因为它不知道服务器端存储包含什么。因此，我们需要将商店发送给客户端。

让我们更新我们的`renderer()`

```
// renderer.js

import { renderToString } from 'react-dom/server';
import { StaticRouter as Router } from 'react-router-dom';
import { renderRoutes } from 'react-router-config';

import routes from './routes.js';

function renderer(req, store) {

    const context = {};
    const state = store.getState();

    const content = renderToString(
        <Router context={context} location={req.path} query={req.query}>
            <div>{renderRoutes(routes)}</div>
        </Router>
    );

    const jsx = `
        <!DOCTYPE html>
        <html>
        <head>
            <meta charset="utf-8">
            <meta name="viewport" content="width=device-width, initial-scale=1">
        </head>

        <body>
            <div id="app">${content}</div>
            <script>
                window.STORE_DATA = ${JSON.stringify(state).replace('<script>', '')} </script>
            <script src="/bundle.js"></script>
        </body>
        </html>
    `;

    return {jsx, context};
}

export default renderer; 
```

我们使用`JSON.stringify()`将整个商店放入一个变量中。
我们去掉了所有的`script`标签，因为它们会把事情弄糟，而且是潜在的安全问题。

你还记得我们写了一个名为`createStore()`的函数，它在那里没有多大意义，因为...咄...多余？现在我们的状态在一个变量中，我们可以把这个状态放到我们的`createStore()`函数中，从这个状态创建一个存储。一分钱一分货；)

```
// client/index.js

import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter as Router } from 'react-router-dom';
import { renderRoutes } from 'react-router-config';

import {Provider} from 'react-redux';

import createStore from '../createStore';
import routes from '../routes';
import rootReducer from '../rootReducer';

const store = createStore(window.STORE_DATA);

const jsx = (
    <Provider store={store}>
        <Router>
          <div class="wrapper">{renderRoutes(routes)}</div>
        </Router>
    </Provider> );

ReactDOM.hydrate(jsx, document.getElementById('app')); 
```

好吧。就是这样。这是一条漫长的道路，但我们终于走到了尽头。我们的 React 应用程序正在工作，路由已经设置好，Redux 商店已经在那里，我们有一个服务器端渲染的应用程序，它在客户端进行了水合处理。您可以通过在浏览器中停用 JavaScript 来测试这一点。页面应该如您所愿显示，但是没有任何 JavaScript 函数。

这里有几件事我没有解决。前端的认证呢？您可以使用 cookies 来完成这项工作，但这仍然有点棘手。文章够长了，可能这个我改天再说。

一如既往，我希望你学到了一些东西，我希望你喜欢阅读这篇文章，如果你喜欢，请随时喜欢它或留下评论。我总是喜欢暗示、提示或者只是一句友好的“你好”。

感谢阅读！
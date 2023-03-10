# 反应中的疼痛:SSR 背后的挑战

> 原文：<https://dev.to/nickcis/a-pain-in-the-react-challenges-behind-ssr-3po3>

*TL；在这篇文章中，我将尝试展示，在我看来，在 React 中进行 ssr 常用方法的当前难点是什么，以说教的方式比较现有的解决方案。*

[![](img/b74e87f2f50f53990d4745de136abc2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ya1LE0O0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nckweb.com.ar/static/d52354f720656e0f8d6a6d4b9b97b5ac/5cdc3/cover.png)

首先，什么是 SSR？。SSR 是*服务器端渲染*的缩写。在高层次上，这意味着在服务器上生成完整的网页，而不必依赖客户端 javascript。

我们不会进入为什么我们想要这样做的细节，但是，它可能主要是由 [SEO 关注](https://www.youtube.com/watch?v=PFwUbgvpdaQ)、[可访问性](https://www.svtplay.se/klipp/16183939/reactjs-meetup-svt---english-subtitles?position=173)或者仅仅是[性能](https://developers.google.com/web/updates/2019/02/rendering-on-the-web)所驱动的。

## SSR 背后的问题

如果我们快速浏览一下 [react 文档](https://reactjs.org/docs/react-dom-server.html#rendertostring)，服务器端渲染可能会被视为非常简单的事情。只需导入`react-dom/server`并调用`renderToString`方法。*易皮斯:*

```
const http = require('http');
const ReactDOMServer = require('react-dom/server');
const App = require('./App.js');

const server = http.createServer((req, res) => {
  const html = ReactDOMServer.renderToString(
    <App />
  );
  res.send(html);
});

server.listen(8000); 
```

很遗憾，这是行不通的。主要是因为我们习惯了在 React 中编写 [*jsx*](https://reactjs.org/docs/jsx-in-depth.html) ，我们往往会忘记它不是有效的 javascript。我们可以将`<App />`行改为使用 [React.createElement](https://reactjs.org/docs/react-api.html#createelement) ，但是这种方法不能对所有的`App.js`文件、其余的组件和 *css* 文件进行 escale(如果使用 css 预处理器，情况会变得更糟)。那么，第一个问题来了:*trans piling 服务器代码*的需求。

[![Won't somebody please think of the data?](img/974ba9ac61e5ea4f852bd90868ee50d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z_GRz_dI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nckweb.com.ar/static/81fc311fcefe9de36ac480d13ee30cbe/40fad/wont-think-data.png)

一种常见的做法是在 [*组件的生命周期*](https://reactjs.org/docs/react-component.html#componentdidmount)中分派数据获取。但是，我们在服务器端有生命周期方法吗？、*剧透* : ***无*** 。实际上，在服务器上有`componentDidMount`没有任何意义，记住`renderToString`是一个同步单通道渲染，而在客户端，我们会在数据获取完成后调用`setState`来触发另一个渲染阶段。生命周期之间的差异导致了几个问题，首先，在服务器端渲染之前，我们如何确定和获取数据？。第二，我们如何在服务器和客户端之间共享状态(这可能是用`setState`生成的)？。

最后但同样重要的是，在客户端，我们将使用 ajax 触发数据获取。类似于对一个端点进行一个 [fetch](https://developer.mozilla.org/docs/Web/API/Fetch_API) 调用。这个请求会有特定的信息(主要是主机信息和报头，比如 *cookie* one)，如何在服务器端复制这些信息呢？

总而言之，我们必须处理以下问题:

1.  为服务器生成有效的 JS 代码
2.  确定数据依赖关系
3.  实际获取数据
4.  共享状态

## 为服务器生成有效的 JS 代码

React 以拥有陡峭的配置以使其运行而闻名。如果我们检查一下被认为是 *hello world* 的例子(使用 [*创建 react app*](https://facebook.github.io/create-react-app/) )，我们会意识到我们包括了类似 [1300 的依赖关系](https://npm.anvaka.com/#/view/2d/react-scripts)。所有这些依赖项处理了很多我们可能不需要的特性和需求，但是，你要明白，让 react 运行起来并不简单。

[![React scripts dependencies](img/923a2de828bf926ed7f27bf71a31d864.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4N3lKCLE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nckweb.com.ar/static/8a046683165ca5171bbc053d80ef993c/40fad/react-dependencies.png)

至于我们如何获得有效的节点 js 代码，我们有几种选择:

*   Webpack :应用与客户端代码相似的构建步骤
*   **Babel:** 使用 [babel-cli](https://babeljs.io/docs/en/babel-cli) 传输代码，不捆绑。

有更多的选择，我们可以使用另一个捆绑器(或用 zeit 的 ncc 编译)，但扔新工具没有多大意义。

迂腐，我们不应该需要 webpack，babel 可能是唯一一个用于生成有效节点 js 代码的工具。事实上，webpack 将使用 babel 进行传输，所以我们可以跳过中介。在 nodejs 的情况下，捆绑不是我们需要的，我们可以有许多文件，并通过[节点的模块系统](https://nodejs.org/api/modules.html)包括它们，即。用一种不那么花哨的方式，使用`require`。

[![Babel: One tool to rule them all](img/f8337bc1049f9e5cad950487a0c254ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iEOePa7Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nckweb.com.ar/static/4c499bd7f7f413642b393898160e3147/f8fb9/one-tool-to-rule-them-all-min.jpg)

的问题*一个工具来统治他们所有的方法(即。一般来说，webpack 做的任务比 transpiling 多。例如，我们是否在使用 css 模块？因此，webpack 通过 css 加载器的[对类进行名称篡改，以重新生成唯一的名称。我们使用构建时间常数了吗？，我们可能用](https://github.com/webpack-contrib/css-loader) [webpack 的定义插件](https://webpack.js.org/plugins/define-plugin/)来定义它们。还有更多 webpack 正在执行的任务的例子(静态文件，等等)，但是对于这些任务中的每一个，我们必须找到一个 babel 预置或插件来执行这项工作。*

如果我们坚持使用 webpack 路径，尽管我们不会有相同的客户机和服务器配置文件，但这两个文件将非常相似，共享其大部分代码。此外，大多数 webpack 加载器都有一种关于如何使用它们进行服务器端渲染的解释(例如，css loader 有 [*exportOnlyLocals* 选项](https://github.com/webpack-contrib/css-loader#exportonlylocals))。

回到我们的目标，我们需要添加一些包:

*   Webpack(和 webpack cli)
*   巴别塔(预设和加载器)
*   反应(和反应 dom)

```
yarn add --dev webpack webpack-cli webpack-node-externals @babel/core @babel/preset-env @babel/preset-react babel-loader
yarn add react react-dom 
```

你可能想知道`webpack-node-externals`是什么，嗯，在 node 上，我们不想捆绑可以在运行时包含的包(`require`)(来自`node_modules`和标准库的所有包)， [webpack-node-externals](https://www.npmjs.com/package/webpack-node-externals) 正是这样做的。

服务器和客户端将使用 [webpack 的阵列配置](https://webpack.js.org/configuration/configuration-types/#exporting-multiple-configurations) :
，而不是分离构建阶段

```
module.exports = [
  // Client configuration
  {
    mode,
    entry: path.join(src, 'client'),
    output: {
      path: dist,
      filename: 'client.js',
    },
    module: {
      rules: [
        {
          test: /\.js$/,
          include: [src],
          use: [
            {
              loader: 'babel-loader',
              options: {
                presets: [
                  ['@babel/preset-env', { modules: false }],
                  '@babel/preset-react'
                ],
              },
            },
          ],
        },
      ],
    },
  },
  // Server configuration
  {
    mode,
    target: 'node',
    entry: src,
    output: {
      path: dist,
      filename: 'server.js',
    },
    module: {
      rules: [
        {
          test: /\.js$/,
          include: [src],
          use: [
            {
              loader: 'babel-loader',
              options: {
                presets: [
                  ['@babel/preset-env', { targets: { node: 'current' }}],
                  '@babel/preset-react'
                ],
              },
            },
          ],
        },
      ],
    },
    externals: [
      nodeExternals(),
    ],
  },
]; 
```

我不会进入关于 babel 预置的细节: [babel-preset-env](https://babeljs.io/docs/en/babel-preset-env) 是支持新 ECMA 语法的最简单的方法， [babel-preset-react](https://babeljs.io/docs/en/babel-preset-react) 允许我们编写 jsx。

[完整示例可在此处找到](https://github.com/NickCis/a-pain-in-the-react-challenges-behind-ssr/tree/master/1-webpack-ssr)。

那么，我们完成了吗？。快速回答是否定的。这个例子是运行 React 服务器端渲染的最小例子，它缺少许多特性(没有 css，没有静态文件，没有源地图，没有产品优化，没有供应商捆绑，没有代码分割，等等)。虽然我们可以从这里开始构建一个完整的项目，但我不推荐这样做。现在一天，我们可能会使用一个工具来解决所有这些配置，如 [razzle](https://github.com/jaredpalmer/razzle) 、 [next.js](https://github.com/zeit/next.js) 或 [react-server](https://github.com/redfin/react-server) 。这个例子的目的是在更高的层次上理解这些工具是如何工作的。

对于下面的例子，我们将使用 razzle 来减少所需的样板文件。

## 确定数据依赖关系

正如我以前说过的，React 在服务器上的行为不同于在客户端上的行为。当调用[*render tostring*](https://reactjs.org/docs/react-dom-server.html#rendertostring)*，*时，我们正在进行同步一遍渲染。这意味着为了生成完整的页面，我们必须想出如何在呈现之前获取所有需要的数据。

[![](img/29f3a0b4bc35200058f9d955164acf88.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cuWythx3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nckweb.com.ar/static/3e5d78654844972eb6734727d3a7c898/40fad/render-comparison.png)

主要有两种方法来解决这个问题:

*   基于页面/路线的方法( [NextJs 的 getInitialProps](https://nextjs.org/docs#fetching-data-and-component-lifecycle) 或 [Afterjs 的*getInitialProps*T5)](https://github.com/jaredpalmer/after.js/blob/master/README.md#getinitialprops-ctx--data)
*   基于组件树的方法([阿波罗的*getDataFromTree*T3)](https://www.apollographql.com/docs/react/features/server-side-rendering#getDataFromTree)

第一种严重依赖于使用一个在 react 世界内外都工作的路由器。首先，我们将定义页面或路由，即当获取特定 url 时将呈现的 React 组件。这可以通过多种方式实现，例如， [NextJs 使用文件名约定](https://nextjs.org/docs)，或者我们可以只使用一个 routes 对象，其中 URL 被映射到特定的组件。

需要注意的是，我们将只考虑页面(或路线)的数据依赖性，子组件将被忽略。这在 [NextJs 的文档](https://nextjs.org/docs#fetching-data-and-component-lifecycle)上也有突出显示:

> *注意:*`getInitialProps`***不能**用在子组件中。仅在* `pages` *中。*

所以，这个想法应该是这样的:

1.  从请求中获取 url
2.  确定将为该 url 呈现的页面
3.  调用`getInitialProps`(或者页面的取数据方法)

我们将开始编写一个 *routes* 文件，以便定义使用每个 URL 呈现哪些页面:

```
import Home from './Home';
import Other from './Other';

const routes = [
  {
    path: '/',
    component: Home,
    exact: true
  },
  {
    path: '/other',
    component: Other,
    exact: true
  }
];

export default routes; 
```

下一步是确定哪些页面匹配所请求的 url。为此，我们将使用 [React 路由器的](https://reacttraining.com/react-router/web/api/matchPath) `matchPath`函数，然后调用`getInitialProps`静态方法(如果存在的话):

```
server
  .get('/*', async (req, res) => {
    // Requested url
    const url = req.url;
    let promise;

    routes.some(route => {
      const match = matchPath(url, route);
      const { getInitialProps } = route.component;

      if (match && getInitialProps)
        promise = getInitialProps();

      return !!match;
    });

    // XXX: should handle exceptions!
    await promise;

    // render
  }); 
```

**注意:**虽然 React router 有一个完成这项工作的包， [react-router-config](https://www.npmjs.com/package/react-router-config) ，为了简单起见，这个例子不会使用它。

在客户端，我们必须添加一些代码来运行`getInitialProps`方法(类似于 afterjs 中的 [After 组件)。](https://github.com/jaredpalmer/after.js/blob/master/src/After.tsx)

为了简单起见，我们将遵循与 *afterjs* 略有不同的方法。关于`componentDidMount`和`componentDidUpdate`方法，我们将只调用`getInitialProps` :

```
class Home extends Component {
  static async getInitialProps() {
    console.log('Fetching Home!');
  }

  componentDidMount() {
    Home.getInitialProps();
  }

  componentDidUpdate(prevProps){
    // Only fetch data if location has changed
    if (this.props.location != prevProps.location)
      Home.getInitialProps();
  }

  render() {
    return (
      <div className="Home">
        This is the home!
      </div>
    );
  }
} 
```

[完整示例可在此处找到](https://github.com/NickCis/a-pain-in-the-react-challenges-behind-ssr/tree/master/2-determining-data-dependencies)。

另一方面，Apollo GraphQL 使用基于树的方法。这种确定数据依赖关系的方式更具声明性，因为任何组件(不仅仅是*页面*)都可以拥有它们。

[![A React Component Tree - by Lewis Chung](img/d71cc905f1076c9233c5708fa0449672.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QLSwYMzA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nckweb.com.ar/static/a69ced61f10955d13cb923507c26a953/19f17/apollo-tree.jpeg)

这种解决方案的缺点是相当复杂*(我们正在做一种预渲染来获得数据依赖)*，我不会进入太多的细节，因为[阿波罗的博客有一个很好的帖子解释它是如何工作的](https://blog.apollographql.com/how-server-side-rendering-works-with-react-apollo-20f31b0c7348)。简单总结一下， [Apollo 有一个名为 getDataFromTree](https://www.apollographql.com/docs/react/features/server-side-rendering) 的函数，它遍历整个 React 树，检查组件是否需要获取信息。在`2.5`分支之前，apollo 有一个自定义的 [walkTree 方法](https://github.com/apollographql/react-apollo/blob/apollo-client-2.0/src/getDataFromTree.ts#L25)，它以某种方式重新实现了渲染算法(这在提到的 Apollo 的博客上有解释)。

现在(从阿波罗`2.5`开始)， [getDataFromTree 方法使用 React-Dom 的 renderToStaticMarkup](https://github.com/apollographql/react-apollo/blob/release-2.5.0/src/getDataFromTree.ts#L98) 隐藏起来。[查询组件](https://github.com/apollographql/react-apollo/blob/release-2.5.0/src/Query.tsx#L246)仅在获取数据时呈现其子组件。并且调用`renderToStaticMarkup`,直到不再需要获取更多信息。所以，阿波罗调用`renderToStaticMarkup`来收集*查询*组件的所有承诺。因为那些组件，如果它们有数据依赖，就不要呈现它的子组件(当然，提取的数据可能会影响子组件)，当那些承诺被解析时，必须调用`renderToStaticMarkup`。重复这个过程，直到不再收集承诺。这种方法允许在 react 树中的任何节点上声明数据依赖关系，但是存在必须多次渲染树的性能问题。

尽管我们能够在客户机和服务器上确定我们需要获取哪些数据依赖项，但我们实际上并没有获取任何数据，也没有在客户机和服务器之间共享！

## 实际抓取数据

在前面的步骤中，我们已经检测到需要什么数据，但是我们还没有开发出一种方法来实际获取这些数据。从根本上说，获取数据将是一个简单的 ajax 调用(在客户端调用 [fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) ，在服务器端调用[节点兼容的 fetch 实现](https://www.npmjs.com/package/node-fetch))。但是，我们必须记住，在客户端，fetch 做了一些额外的工作:

```
const fetch = require('node-fetch');

fetch('/data');

// UnhandledPromiseRejectionWarning: TypeError: Only absolute URLs are supported 
```

除了绝对 url 之外，客户端存储并发送 http 头(例如: *Cookie* )，我们在进行 SSR 时需要转发这些头。

[![A nice drawing](img/94785962957a2db7369c18a5b37a6f21.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0tA5xtmt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nckweb.com.ar/static/cb6679f9661b97977dc1bcd6210e7150/5cdc3/browser-node.png)

两个 API， [NextJs getInitialProps](https://nextjs.org/docs#fetching-data-and-component-lifecycle) 和 [AfterJs getInitialProps](https://github.com/jaredpalmer/after.js#getinitialprops-ctx--data) ，实现了一个相似的接口。这个方法是用一堆参数调用的:

*   `req`:请求对象(仅限服务器端)
*   `res`:响应对象(仅限服务器端)
*   位置(url)相关信息

这里的问题是，在解决服务器和客户机之间的差异时，我们被孤立了。接下来或之后的文章为我们提供了解决这个问题的方法。这通常会导致使用一个包，比如 [fetch-ponyfill](https://www.npmjs.com/package/fetch-ponyfill) ，以便拥有一个同构的获取函数[，这可能会导致向客户端](https://arunoda.me/blog/ssr-and-server-only-modules)发送不必要的代码，并且如果`req`参数存在的话，添加一个基本 url(也转发请求头:

```
// ...
const fetch = require('fetch-ponyfill')();

const Component = () => /* ... */;

Component.getInitialProps = async ({ req }) => {
  let url = '/data';
  let opts = {};
  if (req) {
    url = `${req.protocol}://${req.headers.host}${url}`;
    opts.headers = req.headers;
  }

  return fetch(url, opts);
}; 
```

**注意:**在服务器端复制 ajax 请求的最简单的方法是向自身发出请求，这就是为什么我们将主机放在请求的 url 前面。这不是最有效的解决方案，但它确实有效。

那么，为了提供一个统一的数据获取 api，我们将稍微修改一下`getInitialProps` api，添加一个`fetch`函数。在服务器端，这个函数将负责添加基本 url 内容和标题，而在客户端，它将是默认的`fetch`。

```
import nodeFetch from 'node-fetch';

//...

server
  // ...
  .get('/*', async (req, res) => {
    const fetch = (url, opts = {}) =>
      nodeFetch(`${req.protocol}://${req.headers.host}${url}`, {
        ...opts,
        headers: {
          ...req.headers,
          ...opts.headers
        }
      });

    // Requested url
    const url = req.url;
    let promise;

    routes.some(route => {
      const match = matchPath(url, route);
      const { getInitialProps } = route.component;

      if (match && getInitialProps)
        promise = getInitialProps({ fetch, match });

      return !!match;
    });

    // XXX: should handle exceptions!
    await promise;

    // ...
  }); 
```

在客户端:

```
class Home extends Component {
  static async getInitialProps({ fetch }) {
    return fetch('/data')
      .then(r => r.json())
  }

  // ...

  componentDidMount() {
    Home.getInitialProps({
      fetch,
      match: this.props.match,
    }).then(r => this.setState(r));
  }

  componentDidUpdate(prevProps){
    // Only fetch data if location has changed
    if (this.props.location != prevProps.location)
      Home.getInitialProps({
        fetch,
        match: this.props.match,
      }).then(r => this.setState(r));
  }

  // ...
} 
```

如果我们有一个 redux 堆栈，也可以使用不使用全局`fetch`函数并依靠设置来区分服务器和客户端实现的概念。在建立商店时，我们可以添加一个[中间件](https://redux.js.org/advanced/middleware)，它为数据获取提供相同的接口，但实现不同。使用 [redux-thunk](https://github.com/reduxjs/redux-thunk) ( **注意:** [我们还可以编写一个定制的中间件，并分派定制的动作](https://redux.js.org/recipes/reducing-boilerplate#async-action-creators):

```
// createStore.js
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import rootReducer from './reducers/index';

export default function (fetch) {
  return createStore(
    rootReducer,
    applyMiddleware(thunk.withExtraArgument(fetch))
  );
}

// client
import createStore from './createStore';

const store = createStore(fetch);

// server
import createStore from './createStore';

server
  // ...
  .get('/*', async (req, res) => {
    const fetch = (url, opts = {}) =>
      nodeFetch(`${req.protocol}://${req.headers.host}${url}`, {
        ...opts,
        headers: {
          ...req.headers,
          ...opts.headers
        }
      });
    const store = createStore(fetch);

    // ...
  }) 
```

在任何动作创建器上，我们将使用第三个参数作为`fetch`函数:

```
const actionCreator = (dispatch, getState, fetch) => {
  dispatch(loading());

  return fetch('/data')
    .then(data => {
      dispatch(receivedData(data));
    });
} 
```

如果我们检查 [Apollo 的 GraphQL 方法](https://www.apollographql.com/docs/react/features/server-side-rendering#server-initialization)，我们会看到一个类似的解决方案:

```
server
  // ...
  .get('/*', (req, res) => {
    const client = new ApolloClient({
      ssrMode: true,
      // Remember that this is the interface the SSR server will use to connect to the
      // API server, so we need to ensure it isn't firewalled, etc
      link: createHttpLink({
        uri: `${req.protocol}://${req.headers.host}`,
        credentials: 'same-origin',
        headers: {
          cookie: req.header('Cookie'),
        },
      }),
      cache: new InMemoryCache(),
    });

    // ...
}); 
```

回到这个例子([你可以在这里下载完整的例子](https://github.com/NickCis/a-pain-in-the-react-challenges-behind-ssr/tree/master/3-actually-fetching-data))，如果我们运行它，我们会看到:

[![](img/a129424c5a86b99f746907c3e6f2a938.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VfKArEhH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://nckweb.com.ar/flash-f9419bc712279d3839b83bf2c2a88e9e.gif)

嗯，虽然我们在服务器端获取数据，但我们不会与客户端共享数据，也不会在服务器上使用数据！。

## 共享状态

[![](img/e71a7203a927efc56e4a0aad169c7053.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mZs4B-qi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nckweb.com.ar/static/1618afde30994098327405c2179ab2ce/34e00/sharing-state.png)

我们已经设法解决了如何检测我们需要的数据请求，实际上获取了数据，但是我们没有在服务器和客户端之间共享该状态。

首先，我们必须开发一种方法来使用服务器生成的初始状态。对于组件，这意味着用一个道具而不是一个空对象来初始化它的状态:

```
class Home extends Component {
  // ...

  state = this.props.initialState || {};

  // ...
} 
```

*(是的，我们没有对 initialState prop change 作出反应，这在 react world 中被认为是一种反模式，但这个例子的想法是描绘使 ssr 工作的概念，而不是开发一个生产就绪代码。此外，initialState 应该是不可变，因此，在这里采用这个前提条件应该不成问题)。*

父组件也必须通过初始状态:

```
const App = ({ initialState }) => (
  <Switch>
    {routes.map(({ path, exact, component: Component }) => (
        <Route
          key={path}
          path={path}
          exact={exact}
          render={props => (
            <Component
              initialState={initialState[props.location.pathname]}
              {...props}
            />
          )}
        />
    ))}
  </Switch> ); 
```

虽然`initialState`只会带来一个组件的数据(只会有由匹配组件的`getInitialProps`创建的已解析承诺的值)，但它是一个字典，其关键字是获取数据的 url。背后的原因只是简化了访问数据所需的代码:如果是服务器端获取的组件，`initialState[props.location.pathname]`将返回数据，否则将返回`undefined`。

就服务器而言，我们将存储解析后的值，并将其传递给`App`组件:

```
server
  .get('/*', async (req, res) => {
    // ...
    const initialState = {
      [url]: await promise,
    };
    const markup = renderToString(
      <StaticRouter location={url}>
        <App initialState={initialState} />
      </StaticRouter>
    );

    // ...
  }); 
```

我们仍然需要将状态从服务器传递到客户端。为了实现这一点，我们将添加一个`script`标签，将`initialState`附加到一个全局变量(例如:`window.__INITIAL_STATE__` ):

```
res.status(200).send(
  `<!doctype html>
    <html lang="">
    <head>
        <!-- ... -->
    </head>
    <body>
        <div id="root">${markup}</div>
        <script>
          window.__INITIAL_STATE__ = ${JSON.stringify(initialState)};
        </script>
    </body>
</html>`
); 
```

这是大多数库( [Redux](https://redux.js.org/recipes/server-rendering#inject-initial-component-html-and-state) ，NextJS， [Apollo GraphQL](https://www.apollographql.com/docs/react/features/server-side-rendering#store-rehydration) )建议的方法。在生产中，我们可能希望使用[一个更安全的序列化库](https://github.com/yahoo/serialize-javascript)。

在客户端，我们将读取这个变量:

```
const initialState = window.__INITIAL_STATE__ || {};

hydrate(
  <BrowserRouter>
    <App initialState={initialState} />
  </BrowserRouter>,
  document.getElementById('root')
); 
```

[完整示例可在此处找到](https://github.com/NickCis/a-pain-in-the-react-challenges-behind-ssr/tree/master/4-sharing-state)

## 我们完了吗？

嗯，真的，没有。有很多事情被搁置。我写这篇文章的目的是在尝试实现一个基本的 react 服务器端渲染应用程序时，整理出我对问题的想法，以某种方式也可以帮助其他人！。就我个人而言，我认为了解我使用的工具是如何工作的，将允许我以更好的方式使用它们，或者为已知问题创建开箱即用的解决方案。

这篇文章的例子远非产品代码，仅举几个例子:

*   `Home`组件是唯一一个进行数据提取的组件。所有需要的逻辑都在该组件上实现，显然这是不可伸缩的。数据获取代码应该被抽象(这不是组件所关心的！)，或许可以用[高阶组件](https://reactjs.org/docs/higher-order-components.html) *(例如:`withInitialProps` )* 或者[渲染道具](https://reactjs.org/docs/render-props.html)来封装。*(嗯，可能出于非说教的目的，最好遵循*[*after js*](https://github.com/jaredpalmer/after.js/blob/master/src/After.tsx#L20)*/*[*next js*](https://github.com/zeit/next.js/blob/canary/packages/next-server/lib/router/router.ts#L284)*实现，并将该数据获取实现放在页面的父组件上)*
*   我们甚至还没有谈到，如果不止一个组件请求同一个资源，如何防止多次获取该资源(当遵循类似 Apollo 的方法或者实现多级页面(即子页面)时，就会发生这种情况)。
*   避免本地查询的网络:在示例中，我们一直在做`fetch`到`localhost`，但这是相当低效的。 [Apollo GraphQL 有一节讲述了如何做到这一点](https://www.apollographql.com/docs/react/features/server-side-rendering#local-queries)，但在实践中实现起来相当困难。

* * *

这篇文章附有反对通知。

正如我在本文开头所说，大多数问题的出现是因为 React 没有为我们提供一个标准的数据获取解决方案。我们必须在 react api 之上实现我们自己的一个。随着[悬念](https://reactjs.org/docs/code-splitting.html#reactlazy)、并发和[缓存](https://www.npmjs.com/package/react-cache)的引入，这一切都可能改变。问题是*还没有准备好*。React 不支持服务器端渲染上的悬念。在客户端，只支持通过`React.lazy`进行代码拆分。 [React 缓存处于其早期版本](https://github.com/facebook/react/issues/14780)，许多东西都带有前缀`unstable`。尽管有许多实验存在(例如 [react ssr prepass](https://github.com/FormidableLabs/react-ssr-prepass) 或 [react lightyear](https://github.com/Ephem/react-lightyear) )，我们将无法使用它们做任何事情，除了玩弄可能的未来。但是，让我们祈祷 [React 的数据获取未来可能看起来很光明](https://reactjs.org/blog/2018/11/27/react-16-roadmap.html)，我们只需要`await React.suspense()`。

* * *

最初发表于[https://nckweb.com.ar](https://nckweb.com.ar/a-pain-in-the-react-challenges-behind-ssr/)。
# 随 create-react-app 创建的 APP 应用程序提供的 serviceWorker.js 是什么？ unregister ( )在做什么？

> 原文：<https://dev.to/diveintohacking/create-react-app-serviceworker-js-4bf0>

[![artem-sapegin-176819-unsplash.jpg](img/d3b6221e9eeaedf0952f2ceba8363667.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--n80xqHI5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/9001/30744e54-1834-b9a0-bc48-b1d499f01264.jpeg)

是的，你好！ Dive into Hacking 是免费的。 2019 年黄金周 10 连休，您过得怎么样？

用于创建 react APP 表示的常规锅炉板 create-react-app。
前几天，主要更新为[v3.0.0](https://github.com/facebook/create-react-app/tree/v3.0.0) ，真的成为了稳定的工具。
在我的 Udemy 课程[React](https://www.udemy.com/react-application-development/?couponCode=BLOG-2019-04-29T21) 和[GraphQL](https://www.udemy.com/graphql-with-react/?couponCode=BLOG-2019-04-29T21) 的课程中也使用了这个 create-react-app，但是
APP 应用程序做得太简单了
本文想介绍一下这个 create-react-app 生成的`serviceWorker.js`文件是谁，以及使用这个模块运行的代码在做什么。
另外，本文实际使用的 create-react-app 的版本如下。

```
$ create-react-app --version
3.0.0 
```

Enter fullscreen mode Exit fullscreen mode

那么，尝试像往常一样使用上述版本的 create-react-app 创建 react APP。
APP 应用程序的名字有点长，我想叫`what-is-the-role-of-service-worker`。

```
$ create-react-app what-is-the-role-of-service-worker

Creating a new React app in /path/to/what-is-the-role-of-service-worker.

Installing packages. This might take a couple of minutes.
Installing react, react-dom, and react-scripts...

yarn add v1.15.2
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
warning "react-scripts > @typescript-eslint/eslint-plugin@1.6.0" has unmet peer dependency "typescript@*".
warning "react-scripts > @typescript-eslint/parser@1.6.0" has unmet peer dependency "typescript@*".
warning "react-scripts > @typescript-eslint/eslint-plugin > @typescript-eslint/typescript-estree@1.6.0" has unmet peer dependency "typescript@*".
warning "react-scripts > @typescript-eslint/eslint-plugin > tsutils@3.10.0" has unmet peer dependency "typescript@>=2.8.0 || >= 3.2.0-dev || >= 3.3.0-dev || >= 3.4.0-dev".
[4/4] 🔨  Building fresh packages...
success Saved lockfile.
success Saved 19 new dependencies.
info Direct dependencies
├─ react-dom@16.8.6
├─ react-scripts@3.0.0
└─ react@16.8.6
info All dependencies
├─ @babel/plugin-proposal-class-properties@7.4.0
├─ @babel/plugin-proposal-decorators@7.4.0
├─ @babel/plugin-transform-flow-strip-types@7.4.0
├─ @babel/plugin-transform-runtime@7.4.3
├─ @babel/plugin-transform-typescript@7.4.4
├─ @babel/preset-typescript@7.3.3
├─ babel-plugin-macros@2.5.1
├─ babel-plugin-named-asset-import@0.3.2
├─ babel-preset-react-app@8.0.0
├─ confusing-browser-globals@1.0.7
├─ eslint-config-react-app@4.0.0
├─ fork-ts-checker-webpack-plugin@1.0.1
├─ inquirer@6.2.2
├─ react-app-polyfill@1.0.0
├─ react-dev-utils@9.0.0
├─ react-dom@16.8.6
├─ react-error-overlay@5.1.5
├─ react-scripts@3.0.0
└─ react@16.8.6
✨  Done in 11.97s.

Initialized a git repository.

Success! Created what-is-the-role-of-service-worker at /path/to/what-is-the-role-of-service-worker
Inside that directory, you can run several commands:

  yarn start
    Starts the development server.

  yarn build
    Bundles the app into static files for production.

  yarn test
    Starts the test runner.

  yarn eject
    Removes this tool and copies build dependencies, configuration files
    and scripts into the app directory. If you do this, you can’t go back!

We suggest that you begin by typing:

  cd what-is-the-role-of-service-worker
  yarn start

Happy hacking! 
```

Enter fullscreen mode Exit fullscreen mode

那么，APP 应用程序制作完成后，用`cd`进行移动。

```
$ cd what-is-the-role-of-service-worker 
```

Enter fullscreen mode Exit fullscreen mode

然后，在编辑器中打开`src/index.js`。

```
$ vim src/index.js 
```

Enter fullscreen mode Exit fullscreen mode

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';

ReactDOM.render(<App />, document.getElementById('root'));

// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: https://bit.ly/CRA-PWA
serviceWorker.unregister(); 
```

Enter fullscreen mode Exit fullscreen mode

打开一看，应该是上述内容的 JavaScript 的代码，
这次应该关注的对象是第 5 行的`serviceWorker`这个`import`对象的模块，以及使用该模块的`src/index.js`的
实际上，这里做的事情非常简单，让`serviceWorker`这个模块执行`unregister()`这个函数。
用英语解释的话，应该就是字面上的意思，总之，就是执行了“解除”。
“嗯？ 解除是什么意思？ ”在这里，可以在名为`serviceWorker`的其他模块中启用或禁用[PWA(Progressive Web Application )](https://developer.mozilla.org/ja/docs/Web/Progressive_web_apps)的缓存功能
仔细看之前的评论文，

> 如果您希望您的应用程序脱机工作并且加载速度更快，您可以将下面的取消注册()更改为注册()。注意这伴随着一些陷阱。了解更多关于服务人员的信息:[https://bit.ly/CRA-PWA](https://bit.ly/CRA-PWA)

有这样的注释，作为前提，在 PWA 这个功能中，有离线这个功能，即使在无法连接网络的环境下，也可以很好地利用缓存内容来实现页面转移，这个【TTT 这样写得很好。
刊登本文的[博客网站](https://blog.diveintohacking.com/)中`serviceWorker`仍然存在(但是，不是`serviceWorker.js`这个文件名，而是`sw.js`这个文件名。 )，即使离线，只要是网站内的内容，也可以通过缓存进行页面迁移。 我再次感受到默认情况下启用该缓存的 GatsbyJS 很厉害。

顺便说一下，关于 service workers，请参照[https://bit.ly/CRA-PWA](https://bit.ly/CRA-PWA) ！ 虽然上面写着，但这个链接其实没有写太多详细的事情。
详细情况是谷歌发布的官方文档[Service Worker 的介绍](https://developers.google.com/web/fundamentals/primers/service-workers/)是一篇很好的报道，所以我想先读一遍就可以了。

因此，这次介绍了 create-react-app 使用的`serviceWorker`模块，以及控制该模块所具有的 PWA 的一个功能即 cache 的有效/无效的代码。

潜入黑客！

哈哈
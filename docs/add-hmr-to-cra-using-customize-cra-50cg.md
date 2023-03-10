# 使用定制-cra 将 HMR 添加到 CRA

> 原文：<https://dev.to/c01nd01r/add-hmr-to-cra-using-customize-cra-50cg>

关于在不弹出的情况下添加热模块替换到 Create-React-App 的小说明。

最终项目:[https://github.com/c01nd01r/hmr-for-cra-example](https://github.com/c01nd01r/hmr-for-cra-example)

安装包:

```
yarn add -D react-app-rewired customize-cra react-hot-loader @hot-loader/react-dom 
```

更改`package.json` :

```
 "scripts":  {  "start":  "react-app-rewired start",  "build":  "react-app-rewired build",  "test":  "react-app-rewired test --env=jsdom",  "eject":  "react-scripts eject"  }, 
```

在根目录下创建一个`config-overrides.js`文件。

在`config-overrides.js` :
中增加`react-hot-loader/babel`和`@hot-loader/react-dom`(支持 React 钩子的`react-dom`的补丁版本)

```
const { override, addBabelPlugin, addWebpackAlias } = require('customize-cra');

module.exports = override(
  addBabelPlugin('react-hot-loader/babel'),
  addWebpackAlias({
    'react-dom':  process.env.NODE_ENV === 'production' ? 'react-dom' : '@hot-loader/react-dom',
  }),
) 
```

更改`src/index.js` :

```
import React from 'react';
import ReactDOM from 'react-dom';
import { hot } from 'react-hot-loader/root';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';

const WithHotReload = process.env.NODE_ENV === 'production' ? App : hot(App);

ReactDOM.render(<WithHotReload />, document.getElementById('root'));

// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: https://bit.ly/CRA-PWA
serviceWorker.unregister(); 
```

仅此而已。编码快乐！
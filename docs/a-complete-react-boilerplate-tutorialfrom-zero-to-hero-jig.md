# 完整的 React 样板教程——从零到英雄

> 原文：<https://dev.to/leonardomso/a-complete-react-boilerplate-tutorialfrom-zero-to-hero-jig>

[![enter image description here](img/f093bc08ed85364973f96b0a399fb540.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--scDzy1oK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AIRVIWE6HiS8wQstgybKwvw.jpeg)

当我们开始学习 React 时，为了制作我们的项目，我们需要从头开始制作样板文件，或者使用社区提供的一些样板文件。几乎所有时候，我们都是用 *create-react-app* 来创建一个没有构建配置的应用。或者我们只是从头开始制作我们自己的简单样板文件。

由此，我想到:为什么不制作一个包含我经常使用的所有依赖项的样板文件，并把它准备好呢？社区也是这样想的，所以现在我们有了几个社区创建的样板文件。有些比其他的更复杂，但是他们总是有相同的目标，即最大限度地节省时间。

本文将教您如何使用 React 社区中目前使用的主要依赖项从头开始构建自己的样板文件。我们将使用一些现代的最常见的功能，从那里你可以定制你想要的任何方式。

**[本文创建的样板文件将在此处可用！](https://github.com/leonardomso/react-bolt)T3】**

## 入门

首先，我们将创建一个文件夹来启动我们的样板文件。你想怎么命名都行，我就给我的取名 ***反应——闪电*** 。

打开您的终端，像这样创建它:

```
mkdir react-bolt 
```

Enter fullscreen mode Exit fullscreen mode

现在，转到您创建的文件夹，键入以下命令:

```
npm init -y 
```

Enter fullscreen mode Exit fullscreen mode

`npm`将为您创建一个`package.json`文件，您安装的所有依赖项和您的命令都在那里。

现在，我们将为样板文件创建基本的文件夹结构。现在是这样的:

```
react-bolt    
    |--config    
    |--src    
    |--tests 
```

Enter fullscreen mode Exit fullscreen mode

## Webpack

Webpack 是当今最著名的 JavaScript 应用程序模块捆绑器。基本上，它捆绑了你所有的代码，并生成一个或多个包。你可以在这里了解更多信息。

在这个样板文件中，我们将使用它，所以安装所有这些依赖项:

```
npm install --save-dev webpack webpack-cli webpack-dev-server webpack-merge html-webpack-plugin clean-webpack-plugin img-loader url-loader file-loader 
```

Enter fullscreen mode Exit fullscreen mode

现在在我们的`config`文件夹中，我们将创建另一个名为`webpack`的文件夹，然后在这个`webpack`文件夹中创建 5 个文件。

创建一个名为`paths.js`的文件。在这个文件中，将是所有输出文件的目标目录。

在里面，放上所有这些代码:

```
import path from 'path';

module.exports = {    
    root: path.resolve(__dirname, '../', '../'),    
    outputPath: path.resolve(__dirname, '../', '../', 'build'),    
    entryPath: path.resolve(__dirname, '../', '../', 'src/index.js'),    
    templatePath: path.resolve(__dirname, '../', '../', 'src/index.html'),    
    imagesFolder: 'images',    
    fontsFolder: 'fonts',    
    cssFolder: 'css',    
    jsFolder: 'js'    
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在，创建另一个名为`rules.js`的文件，并将以下代码放在那里:

```
module.exports = [    
    {
        test: /\.js$/,    
        exclude: /node_modules/,    
        use: {    
        loader: 'babel-loader'    
    }    
},    
    {    
        test: /\.eot(\?v=\d+\.\d+\.\d+)?$/,    
        exclude: /node_modules/,    
        loader: 'file-loader'    
    },    
    {    
        test: /\.(woff|woff2)$/,    
        exclude: /node_modules/,    
        loader: 'url-loader?prefix=font/&limit=5000'    
    },    
    {
        test: /\.ttf(\?v=\d+\.\d+\.\d+)?$/,    
        exclude: /node_modules/,    
        loader: 'url-loader?limit=10000&mimetype=application/octet-stream'    
    },    
    {    
        test: /\.(jpe?g|png|gif|svg)$/i,    
        use: ['url-loader?limit=10000', 'img-loader'] 
    }    
]; 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们将再创建 3 个文件:

**webpack.common.babel.js**

```
import webpack from 'webpack';
import HtmlWebpackPlugin from 'html-webpack-plugin';

import paths from './paths';
import rules from './rules';

module.exports = {
    entry: paths.entryPath,
    module: {
        rules
    },
    resolve: {
        modules: ['src', 'node_modules'],
        extensions: ['*', '.js', '.scss', '.css']
    },
    plugins: [
        new webpack.ProgressPlugin(),
        new HtmlWebpackPlugin({
            template: paths.templatePath,
            minify: {
                collapseInlineTagWhitespace: true,
                collapseWhitespace: true,
                preserveLineBreaks: true,
                minifyURLs: true,
                removeComments: true,
                removeAttributeQuotes: true
            }
        })
    ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

**webpack.dev.babel.js**

```
import webpack from 'webpack';

import paths from './paths';
import rules from './rules';

module.exports = {
    mode: 'development',
    output: {
        filename: '[name].js',
        path: paths.outputPath,
        chunkFilename: '[name].js'
    },
    module: {
        rules
    },
    performance: {
        hints: 'warning',
        maxAssetSize: 450000,
        maxEntrypointSize: 8500000,
        assetFilter: assetFilename => {
            return (
                assetFilename.endsWith('.css') || assetFilename.endsWith('.js')
            );
        }
    },
    optimization: {
        splitChunks: {
            chunks: 'all'
        }
    },
    devServer: {
        contentBase: paths.outputPath,
        compress: true,
        hot: true,
        historyApiFallback: true
    },
    plugins: [
        new webpack.HotModuleReplacementPlugin()
    ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

**webpack.prod.babel.js**

```
import CleanWebpackPlugin from 'clean-webpack-plugin';

import paths from './paths';
import rules from './rules';

module.exports = {
    mode: 'production',
    output: {
        filename: `${paths.jsFolder}/[name].[hash].js`,
        path: paths.outputPath,
        chunkFilename: '[name].[chunkhash].js'
    },
    module: {
        rules
    },
    plugins: [
        new CleanWebpackPlugin([paths.outputPath.split('/').pop()], {
            root: paths.root
        })
    ],
    devtool: 'source-map'
}; 
```

Enter fullscreen mode Exit fullscreen mode

基本上，在我们的`webpack.common.babel.js`文件中，我们已经设置了我们的入口和输出配置，并且包括了任何需要的插件。在`webpack.dev.babel.js`文件中，我们已经将模式设置为开发。在我们的`webpack.prod.babel.js`文件中，我们已经将模式设置为生产。

之后，在我们的根文件夹中，我们将创建最后一个名为`webpack.config.js`的 webpack 文件，并放入以下代码:

```
require('@babel/register');
const webpackMerge = require('webpack-merge');

const common = require('./config/webpack/webpack.common.babel');

const envs = {
    development: 'dev',
    production: 'prod'
};

/* eslint-disable global-require,import/no-dynamic-require */
const env = envs[process.env.NODE_ENV || 'development'];
const envConfig = require(`./config/webpack/webpack.${env}.babel`);
module.exports = webpackMerge(common, envConfig); 
```

Enter fullscreen mode Exit fullscreen mode

我们的 webpack 配置已经准备好了，所以现在我们将使用 *Babel* 、 *ESLint* 、*prettle*等来处理样板文件的其他部分。

## 通天塔

我想几乎每个使用 React 的人都可能听说过 Babel 以及这个简单的 transpiler 如何帮助我们的生活。如果你不知道它是什么，Babel 它基本上是一个 transpiler，将你的 JavaScript 代码转换成普通的旧 ES5 JavaScript，可以在任何浏览器中运行。

我们将使用一堆 Babel 插件，所以在我们的根文件夹中，安装:

```
npm install --save-dev @babel/core @babe/cli @babel/node @babel/plugin-proposal-class-properties @babel/plugin-proposal-object-rest-spread @babel/plugin-syntax-dynamic-import @babel/plugin-syntax-import-meta @babel/plugin-transform-async-to-generator @babel/plugin-transform-runtime @babel/preset-env @babel/preset-react @babel/register @babel/runtime babel-eslint babel-jest babel-loader babel-core@7.0.0-bridge.0 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们将在根文件夹中创建一个名为`.babelrc`的文件，在该文件中，我们将放入以下代码:

```
{    
  presets:        
    [        
        [            
            "@babel/preset-env",                
            {                
                useBuiltIns:  "entry"
            }
        ],
        "@babel/preset-react"
    ],
    plugins:        
    [        
        "@babel/plugin-proposal-object-rest-spread",            
        "@babel/plugin-transform-runtime",            
        "@babel/plugin-transform-async-to-generator",            
        "@babel/plugin-proposal-class-properties"
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的项目是用 Babel 编译的，可以使用下一代 JavaScript 语法，没有任何问题。

## ESLint

如今林挺项目最常用的工具是 ESLint。发现某些类型的错误真的很有帮助，比如那些与变量作用域、未声明变量的赋值等相关的错误。

首先，安装以下依赖项:

```
npm install --save-dev eslint eslint-config-airbnb eslint-config-prettier eslint-loader eslint-plugin-babel eslint-plugin-import eslint-plugin-jsx-a11y eslint-plugin-prettier eslint-plugin-react 
```

Enter fullscreen mode Exit fullscreen mode

然后，在我们的根文件夹中，创建一个名为`.eslintrc`的文件，并将以下代码放在那里:

```
{
    "parser": "babel-eslint",
    "extends": ["airbnb", "prettier", "prettier/react"],
    "plugins": ["prettier"],
    "parserOptions": {
        "ecmaVersion": 6,
        "ecmaFeatures": {
            "jsx": true
        }
    },
    "env": {
        "browser": true,
        "node": true,
        "mocha": true,
        "es6": true,
        "jest": true
    },
    "rules": {
        "indent": ["error", 4],
        "space-before-function-paren": "off",
        "react/prefer-stateless-function": "warn",
        "react/jsx-one-expression-per-line": "off",
        "import/no-extraneous-dependencies": [
            "error",
            { "devDependencies": true }
        ],
        "react/jsx-filename-extension": [1, { "extensions": [".js", ".jsx"] }],
        "linebreak-style": "off",
        "global-require": "off",
        "semi": "warn",
        "arrow-body-style": "off",
        "no-multiple-empty-lines": ["warn", { "max": 1 }],
        "no-unused-expressions": [
            "error",
            {
                "allowTaggedTemplates": true
            }
        ],
        "no-underscore-dangle": [
            2,
            { "allow": ["__REDUX_DEVTOOLS_EXTENSION__"] }
        ]
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 漂亮些

漂亮基本上是一个代码格式化程序。它分析您的代码，并根据自己的规则重新打印，这些规则考虑了最大行长度，并在必要时换行。

你只需要安装它:

```
npm install --save-dev prettier 
```

Enter fullscreen mode Exit fullscreen mode

在我们的根文件夹中，创建一个名为`.prettierrc`的文件，并将以下代码放在那里:

```
{
    "printWidth": 80,
    "tabWidth": 4,
    "semi": true,
    "singleQuote": true,
    "bracketSpacing": true
} 
```

Enter fullscreen mode Exit fullscreen mode

## 做出反应

React 是一个用于构建用户界面的开源 JavaScript 应用程序库。它是由脸书开发的，背后有一个庞大的社区。如果你正在阅读这篇文章，我假设你已经了解了 React，但是如果你想了解更多，你可以在这里阅读。

我们将安装以下依赖项:

```
npm install --save react react-dom cross-env 
```

Enter fullscreen mode Exit fullscreen mode

在我们的 src 文件夹中，我们将创建一个简单的 HTML 文件`index.html`，并放入以下代码:

```
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <meta http-equiv="X-UA-Compatible" content="ie=edge" />
        React Bolt
    </head>

    <body>
        <div id="root"></div>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们将创建一个简单的 React 项目。在我们的`src`文件夹中，创建一个`index.js`文件，如下所示:

```
import React from 'react';
import ReactDOM from 'react-dom';

import App from './components/App';

ReactDOM.render(
    <App />,
    document.getElementById('root')
); 
```

Enter fullscreen mode Exit fullscreen mode

在我们的`src`文件夹中，我们将有如下结构:

```
*src
    |--actions
    |--components
    |--reducers
    |--reducers
    |--store* 
```

Enter fullscreen mode Exit fullscreen mode

在`components`文件夹中创建一个名为`App.js`的文件，并放入以下代码:

```
import React from 'react';

const App = () => <h1>React Bolt</h1>;

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

## Redux

Redux 使管理应用程序的状态变得容易。从另一个角度来看，它可以帮助您管理显示的数据以及如何响应用户的操作。如今，很多人更喜欢其他选项，比如 MobX 或 T2 setState 本身，但我将坚持使用 Redux 作为样板。

首先，我们将安装一些依赖项:

```
npm install --save redux react-redux redux-thunk 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将创建 Redux 存储，并在其中放置一些状态。在我们存储文件夹中，创建一个`index.js`文件，并将以下代码放在那里:

```
import { createStore, applyMiddleware, compose } from 'redux';
import thunk from 'redux-thunk';
import logger from 'redux-logger';

import rootReducer from '../reducers';

const middleware = applyMiddleware(thunk, logger);

const reduxDevTools =
    window.__REDUX_DEVTOOLS_EXTENSION__ &&
    window.__REDUX_DEVTOOLS_EXTENSION__();

const store = createStore(
    rootReducer,
    compose(
        middleware,
        reduxDevTools
    )
);

export default store; 
```

Enter fullscreen mode Exit fullscreen mode

现在，在我们的`reducers`文件夹中创建一个`index.js`并放入以下代码:

```
import { combineReducers } from 'redux';

const rootReducer = combineReducers({
    state: () => ({})
});

export default rootReducer; 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将把我们的`index.js`放到我们的`src`文件夹中，用`<Provider />`包装代码，并把它作为道具传递给我们的应用程序。

事情会是这样的:

```
import React, { Fragment } from 'react';
import ReactDOM from 'react-dom';
import { Provider } from 'react-redux';

import store from './store';
import App from './components/App';

ReactDOM.render(  
    <Provider store={store}>
        <App />
    </Provider>,
    document.getElementById('root')
); 
```

Enter fullscreen mode Exit fullscreen mode

完成了。我们的 Redux 商店已经配置完毕，随时可以使用。

## React 路由器

React 路由器是 React 的标准路由库。基本上，它*让你的 UI 与 URL* 保持同步。我们将在样板文件中使用它，所以安装它:

```
npm install --save react-router-dom 
```

Enter fullscreen mode Exit fullscreen mode

之后，转到我们的`src`文件夹中的`index.js`，用`<BrowserRouter>`将所有代码打包。

我们的`src`文件夹中的`index.js`将会这样结束:

```
import React, { Fragment } from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import { Provider } from 'react-redux';

import store from './store';
import App from './components/App';

ReactDOM.render(
    <BrowserRouter>       
        <Provider store={store}>
            <App />
        </Provider>
    </BrowserRouter>,
    document.getElementById('root')
); 
```

Enter fullscreen mode Exit fullscreen mode

## 样式组件

Styled Components 使 CSS 对每个人来说都很容易，因为它可以帮助您组织 React 项目。它的目标是编写更多小的和可重用的组件。我们将使用它，如果你想了解更多，请阅读这里的。

首先，安装它:

```
npm install --save styled-components 
```

Enter fullscreen mode Exit fullscreen mode

然后，在我们的`components`文件夹中的`App.js`文件中，我们将使用样式化的组件创建一个简单的标题。我们的标题是这样的:

```
const Title = styled.h1`
    color: black;
    font-size: 2.5rem;
    font-weight: 700;
`; 
```

Enter fullscreen mode Exit fullscreen mode

在我们的文件中，我们需要导入`styled-components`，所以我们的文件将会像这样结束:

```
import React from 'react';
import styled from 'styled-components';

const Title = styled.h1`
    color: black;
    font-size: 2.5rem;
    font-weight: 700;
`;

const App = () => React Bolt;

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

## Jest &反应测试库

Jest 是一个来自脸书的开源 JavaScript 测试库。它使测试您的应用程序变得容易，并为我们提供了许多关于什么给出了正确的输出，什么没有的信息。React 测试库是一个非常轻量级的 React 组件测试解决方案。基本上，这个文库是酶的替代品。

每个应用程序都需要某种测试。我不会在本文中编写测试，但是我会向您展示如何配置这些工具来开始测试您的应用程序。

首先，我们要安装这两个:

```
npm install --save-dev jest jest-dom react-testing-library 
```

Enter fullscreen mode Exit fullscreen mode

之后再去我们的`package.json`把下面的终究放了:

```
 "jest": {
    "setupFiles": [
        "<rootDir>/config/tests/jest.config"
    ],
    "transform": {
        "^.+\\.js$": "babel-jest"
    }
 } 
```

Enter fullscreen mode Exit fullscreen mode

然后，转到我们的`config`文件夹，并在其中创建了另一个名为`tests`的文件夹，并在该文件夹中创建 2 个文件。

首先，创建一个名为`jest.config.js`的文件，并放入以下代码:

```
module.exports = {
    automock: false,
    browser: false,
    bail: false,
    collectCoverageFrom: [
        'src/**/*.{js,jsx}',
        '!**/node_modules/**',
        '!**/vendor/**'
    ],
    coverageDirectory: '<rootDir>/coverage',
    globals: {
        __DEV__: true
    },
    moduleFileExtensions: ['js', 'json', 'jsx', 'node'],
    transform: {
        '^.+\\.js?$': 'babel-jest'
    },
    verbose: true,
    setupTestFrameworkScriptFile: './rtl.setup.js'
}; 
```

Enter fullscreen mode Exit fullscreen mode

然后，创建一个名为`rtl.setup.js`的文件，并放入以下代码:

```
// See https://github.com/kentcdodds/react-testing-library#global-config
import 'jest-dom/extend-expect';
import 'react-testing-library/cleanup-after-each'; 
```

Enter fullscreen mode Exit fullscreen mode

完成了。我们的样板文件已经准备好了，你现在可以使用它了。

现在转到我们的文件`package.json`并放入以下代码:

```
"scripts": {
    "start": "cross-env NODE_ENV=development webpack-dev-server --open",
    "build": "cross-env NODE_ENV=production webpack",
    "lint": "eslint ./src/**/**.js",
    "lint:fix": "eslint ./src/**/**.js --fix",
    "test": "jest",
    "test:watch": "npm run test --watch",
    "test:cover": "npm run test --coverage"
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果您运行命令 npm start 并转到`localhost:8080`，我们应该看到我们的应用程序工作正常！

**[如果想看我的最终代码，这里有这篇文章创建的样板文件！](https://github.com/leonardomso/react-bolt)T3】**

我对一些特性有一些想法，我很想包含在样板文件中，所以请随意贡献！

**🐦[在 Twitter 上关注我！](https://twitter.com/leonardomso)**
**⭐ [跟着我上 GitHub 吧！](https://github.com/leonardomso)**
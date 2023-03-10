# 使用 Redux 和 Sagas 的 React 认证应用程序

> 原文：<https://dev.to/srivasthava12/react-authentication-app-using-redux-and-sagas-anp>

### 概述

在这篇文章中，我要讲述的是:

*   [还原](https://github.com/reduxjs/redux)
*   [Redux-Saga](https://github.com/redux-saga/redux-saga/)

当我们在开发我们的认证应用程序时，我们将访问上述地点。
让我们确立 Redux 与 React 无关的事实。Redux 也可以用于 Angular。说，Redux 凝胶与反应完美无缺。所以，我来回答一下 redux 是什么这个问题？
简单来说 Redux 就是“管理应用状态的开源 JavaScript 库”。它帮助您编写行为一致的应用程序。

为了增加趣味，让我们使用真正的 API。你可以使用后端 API 服务，我在我的回购，即..，使用节点进行身份验证，并按照自述文件中提到的说明运行服务，或者您可以使用自己的基于 json web token 的 API。

## 好戏开始了！！

[![Alt Text](img/4c86106fe93b5540a9c46f510883dd47.png)](https://i.giphy.com/media/oadZJB3hwMFjy/giphy.gif)

为了开始表演，我们必须展示我们的 react 应用程序。为此，请遵循命令。

确保你已经安装了`create-react-app`
a)`$ npm install -g create-react-app`

创建 react 应用程序
b) `$ npx create-react-app my-app`

进入目录 my-app
c) `$ cd my-app`

运行 react 应用程序
d) `$ npm start`

让我们安装所有需要的包
e) `$ npm i redux react-redux redux-saga react-router redux-form --save`

现在，您已经准备好了 react 应用程序。所以，让我们开始吧。

在`code`目录下。结构概要如下:

```
src/  
  login/       
    actions.js
    constants.js
    sagas.js
    selector.js
    reducer.js
    index.js
    components/
             login.js
  register/
    actions.js      
    constants.js
    sagas.js
    selector.js
    reducer.js
    index.js
    components/
             register.js

  user/
    reducer.js
    actions.js
    constants.js

  share/
    Messages.js

  helpers/
    api-errors.js
    check-auth.js

Main.js
index-reducer.js  
index-sagas.js 
```

让我们浏览一下我们刚刚创建的文件夹和文件:
`login/`、`register/` & `user/`
`login/` & `register/`是我们将要使用的路线。

现在来看看我们创建的文件:
a) `actions.js`是将数据从您的应用程序发送到您的商店的信息负载。它们是普通的 Javascript 对象
b) `reducer.js`指定应用程序的状态如何改变以响应发送到商店。
c) `constants.js`我们将拥有动作和归约器的常量的地方
d) `sagas.js`这是我们管理不纯操作的地方，也就是说，像数据获取
e) `index-reducer.js` & `index-sagas.js`这样的异步操作是我们组合所有归约器和序列的中心

让我们进入代码

1)在目录`src/index`中

*   `index.js`的完整代码是:

```
import React from 'react';
import ReactDOM from 'react-dom';
import { applyMiddleware, createStore, compose } from 'redux';
import { Provider } from 'react-redux';
import createSagaMiddleware from 'redux-saga';
import { routerMiddleware } from 'react-router-redux';
import { history } from './history';
import { Router } from 'react-router-dom';

// Import all of our components
import App from './App';
import './index.css';

// Import the index reducer and sagas
import IndexReducer from './index-reducer';
import IndexSagas from './index-sagas';

import registerServiceWorker from './registerServiceWorker';

// Setup the middleware to watch between the Reducers and the Actions
const sagaMiddleware = createSagaMiddleware();

const routersMiddleware = routerMiddleware(history);

/*eslint-disable */
const composeSetup =
    process.env.NODE_ENV !== 'production' && typeof window === 'object' && window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__
        ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__
        : compose;
/*eslint-enable */

const store = createStore(
    IndexReducer,
    composeSetup(applyMiddleware(sagaMiddleware, routersMiddleware)) // allows redux devtools to watch sagas
);

// Begin our Index Saga
sagaMiddleware.run(IndexSagas);

ReactDOM.render(
    <Provider store={store}>
        <Router history={history}>
            <App />
        </Router>
    </Provider>
    document.getElementById('root')
);
registerServiceWorker(); 
```

*   现在让我们看一下上面的锅炉代码

```
 import React from 'react';
  import ReactDOM from 'react-dom';
  import { applyMiddleware, createStore, compose } from 'redux';
  import { Provider } from 'react-redux';
  import createSagaMiddleware from 'redux-saga';
  import { routerMiddleware } from 'react-router-redux';
  import { history } from './history';
  import { Router } from 'react-router-dom';

  // Import all of our components
  import App from './App';
  import './index.css';

  // Import the index reducer and sagas
  import IndexReducer from './index-reducer';
  import IndexSagas from './index-sagas'; 
```

```
import the required pakages 
```

*   中间件

    ```
    const  sagaMiddleware  =  createSagaMiddleware()
    const  routersMiddleware  =  routerMiddleware(history); 
    ```

创建一个 Redux 中间件，并将 Sagas 连接到 Redux 存储

*   创建商店

    ```
    /*eslint-disable */
    const composeSetup = process.env.NODE_ENV !== 'production' && typeof window === 'object' &&
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?
    window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ : compose
    /*eslint-enable */ 
    ```

const store = createStore(
index reducer，
compose setup(applyMiddleware(saga middleware，routersMiddleware))，
)

```
 Here ` composeSetup(applyMiddleware(sagaMiddleware, routersMiddleware)),` allows redux to watch sagas and router

+ To Start the Saga middleware

  ```javascript
  sagaMiddleware.run(IndexSagas) 
```

只能在`applyMiddleware`阶段后运行。

*   现在让我们渲染 Index.js

```
 ReactDOM.render(
    <Provider  store={store}>
      <Router  history={history}>
        <App  />
      </Router>
    </Provider>,
    document.getElementById('root')
  );
  registerServiceWorker(); 
```

因此，我们在这里为 react app 提供了一个商店，让它提供商店`<Provider store={store}>`

2)现在让我们进入`index-reducer.js`

```
 ```javascript 
 import { combineReducers } from  'redux'
 import { reducer  as  form } from  'redux-form'

 const  IndexReducer  =  combineReducers({
        form
 })
 export  default  IndexReducer
 ``` 
```

`combineReducers`辅助函数将一个值不同的减函数的对象变成一个你可以传递给`createStore`的减函数

3)让我们进入`index-saga.js`

```
import { all } from  'redux-saga/effects';
export  default  function*  IndexSaga() {
yield  all([ ]);
} 
```

4)现在我们已经完成了减速器的索引，saga 和主索引让我们进入`App.js` :

```
import  React, { Component } from  'react';
import  './App.css';
import  Main  from  './Main';
class  App  extends  Component {
  render() {
    return (
      <div  className="App">
        <Main  />
      </div>
    );
  }
}
export  default  App; 
```

在 App 中，`<Main />`是我们的路线所在

5)我们已经分离出一个组件来处理应用程序的路由，即主组件

```
import React from 'react';
import { Switch, Route } from 'react-router-dom';
import Login from '../src/login';
import Register from '../src/register';

export default class Main extends React.Component {
    render() {
        return (
            <main>
                <Switch>
                    <Route path="/login" component={Login} />
                    <Route path="/register" component={Register} />
                </Switch>
            </main>
        );
    }
} 
```

再来深究一下，
`Switch`
`<Switch>`的独特之处在于它独家渲染了一条路线。相比之下，每一个与位置匹配的`<Route>`都会进行包容性渲染。
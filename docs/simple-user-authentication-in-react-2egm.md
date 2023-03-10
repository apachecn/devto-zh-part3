# React 中的简单用户认证

> 原文：<https://dev.to/oktadev/simple-user-authentication-in-react-2egm>

在 2019 年，几乎所有东西都很容易找到 React 组件。例如，如果你想在你的应用程序中添加用户认证，你可以通过 Okta 的 React 组件轻松实现。在这里，我将带你创建一个简单、有趣的 React 应用程序，获取随机的 Chuck Norris 笑话。然后，我将向您展示如何添加用户身份验证和定制您的用户体验，这样笑话将会用自己的名字替换 Chuck Norris 的名字。

## 引导您的 React 应用

要快速启动和运行 React 而没有很多麻烦，您可以使用 React 的 create-react-app 脚本。React 也更喜欢`yarn`，如果你已经安装了的话。您可以用这个命令安装这两个命令:

```
npm install --global create-react-app@2.1.5 yarn@1.12.3 
```

一旦你安装了这些，你就可以创建一个新的应用程序如下:

```
create-react-app chuck-norris-jokes 
```

该脚本将创建一个包含一些启动文件的新目录，安装一些启动和运行所需的依赖项，并用 git 初始化项目。现在，您可以切换到目录并启动开发服务器，这将在您的 web 浏览器中打开应用程序。每当您更改任何源文件时，您的浏览器都会自动更新。

```
cd chuck-norris-jokes
yarn start 
```

[![React start app](img/d0b13b3c48451df4d5ffa7736107f59c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MZS0pa-f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/simple-user-auth-react/react-starting-app-dc883f27c64cfb04eae6838fb092345c9e4d5a0d638da38be7d79c3ff2580576.png)

## 给你的 React 应用添加一些风格

首先，你需要改变默认的标志。因为这将展示查克·诺里斯的笑话，找到你最喜欢的查克·诺里斯的形象(或者用下面的一个)。

[![Chuck Norris bckground](img/d58e9e437e0c8ec469ef76314da93f66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8B7B5zUb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/simple-user-auth-react/chuck-norris-bg-d6ab3fa9a36033fdd2014d8fc3d5893bdf7cfba1b8a8bdeb28e425ee093befe3.png)

将文件保存到`src/chuck-norris.png`。然后打开`src/App.js`，将线路`import logo from './logo.svg'`改为`import logo from './chuck-norris.png'`。

如果你使用上面的图片，默认的深色背景会让你很难看到。你也不需要他的脑袋转来转去，所以打开心扉，做一些改变。你可以去掉`animation`线(也可以去掉`@keyframes`块。你也会想把`background-color`改成类似`gray`的东西。你可能想要添加一个按钮，但是你可以把它设计成和已经存在的`Learn React`链接一样的样式。最后，你的 CSS 看起来应该是这样的(我也为小屏幕的标题添加了一些填充):

```
.App {
  text-align: center;
}

.App-logo {
  height: 40vmin;
  pointer-events: none;
}

.App-header {
  background-color: gray;
  padding: 10px;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  font-size: calc(10px + 2vmin);
  color: white;
}

.App-link {
  color: #61dafb;
}

button.App-link {
  background: none;
  border: none;
  padding: 0;
  font: inherit;
  text-decoration: underline;
  cursor: pointer;
} 
```

## 使用 React 钩子获取一些笑话

随着 React 16.8 的发布，您现在可以使用钩子来使您的代码组件稍微简单一些。不完全必要，但是，为什么不呢？在您的`src/App.js`中，将`App`组件从类更改为函数。默认情况如下:

```
class App extends Component {
  render() {
    return (
      // ...
    );
  }
} 
```

要使它成为一个功能组件，请将其更改为更像这样:

```
const App = () => {
  return (
    // ...
  )
} 
```

这不是很大的区别，但是它允许我们添加挂钩，这比使用类生命周期方法要简单一些。

要添加带有钩子的状态，您需要使用从 React 导出的`useState`函数。稍后您还将使用`useEffect`,因此您需要确保导入这两者。将导入`React`的行修改成这样:

```
Import React, { useState, useEffect } from 'react'; 
```

然后要使用一个状态，在你的`App`函数的顶部，在`return statement`之前添加下面的代码:

```
const [joke, setJoke] = useState(''); 
```

你现在有了一个阅读笑话并改变其价值的方法。默认情况下，它只是一个空字符串，直到你获取它。说到抓取，你可以使用[互联网查克·诺里斯数据库](http://www.icndb.com/api/)来抓取笑话。它们已经为 HTML 编码，但是 React 期望字符串被解码(例如，`"`而不是`&quot;`)。您可以安装`he`库来处理这个问题，然后使用`import { decode } from 'he'`导入它。使用
进行安装

```
yarn add he@1.2.0 
```

现在你需要写一个函数来获取笑话。因为这将需要引用`setJoke`，所以它应该写在`App`组件内部。`fetch` API 支持一个`signal`，允许你取消呼叫。这对于确保电话以正确的顺序回复是很重要的。如果你接连打了两三个电话，其实你并不在乎前两个，而是，只想要最后的结果。

```
const fetchJoke = async signal => {
  const url = new URL('https://api.icndb.com/jokes/random');
  const response = await fetch(url, { signal });
  const { value } = await response.json();

  setJoke(decode(value.joke));
}; 
```

你还需要一个“效果”挂钩来获取没有的笑话。组件渲染时，`useEffect`钩子将会运行。但是，您可以添加一组要监视的值，这将导致它只在其中一个值发生变化时运行效果。在这种情况下，您只需要在`joke`改变时运行效果，或者在没有笑话集的情况下获取一个效果。如果效果有返回值，它将在清理应用程序时运行，或者在再次渲染之前运行。您可以在这里提供一个取消`fetch`呼叫的功能。

```
useEffect(() => {
  if (!joke) {
    const controller = new AbortController();
    fetchJoke(controller.signal);

    return () => controller.abort();
  }
}, [joke]); 
```

现在你只需要显示这个笑话，并添加一个按钮来获取一个新的笑话。单击该按钮会将笑话设置回一个空字符串，效果是获取一个新的笑话。将`<p>`标签的内容替换为笑话(`<p>{joke || '...'}</p>`)，将`<a>`标签替换为按钮:

```
<button className="App-link" onClick={() => setJoke('')}>
  Get a new joke
</button> 
```

您的`App.js`的最终代码应该是这样的:

```
import React, { useState, useEffect } from 'react';
import { decode } from 'he';

import logo from './chuck-norris.png';
import './App.css';

const App = () => {
  const [joke, setJoke] = useState('');

  const fetchJoke = async signal => {
    const url = new URL('https://api.icndb.com/jokes/random');
    const response = await fetch(url, { signal });
    const { value } = await response.json();

    setJoke(decode(value.joke));
  };

  useEffect(() => {
    if (!joke) {
      const controller = new AbortController();
      fetchJoke(controller.signal);

      return () => controller.abort();
    }
  }, [joke]);

  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>{joke || '...'}</p>
        <button className="App-link" onClick={() => setJoke('')}>
          Get a new joke
        </button>
      </header>
    </div>
  );
}

export default App; 
```

[![Jokes app running](img/b86ee893a5b6390cc52fb11b808ecb23.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wzjmb2B3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://developer.okta.com/assets-jekyll/blog/simple-user-auth-react/chuck-norris-jokes-3e8feb1f3f7c874df677c482424625015f031c0393778adf3ef1858edaa217fe.gif)

## 向您的 React 应用添加用户认证

现在您已经有了一个功能性的 web 应用程序，您可以通过允许用户登录来添加一些个性化内容。向项目添加身份验证的一个简单方法是使用 Okta，这就是我将要演示的。我们的 API 使您能够:

*   [认证](https://developer.okta.com/product/authentication/)和[授权](https://developer.okta.com/product/authorization/)您的用户
*   存储用户数据
*   执行基于密码的[社交登录](https://developer.okta.com/authentication-guide/social-login/)
*   通过[多重身份验证](https://developer.okta.com/use_cases/mfa/)保护您的应用
*   还有更多！查看我们的[产品文档](https://developer.okta.com/documentation/)

如果你还没有，那么[注册一个永远免费的开发者账户](https://developer.okta.com/signup/)。登录你的开发者控制台，导航到**应用**，然后点击**添加应用**。选择**单页 App** ，然后点击**下一步**。

因为 Create React App 默认运行在端口 3000 上，所以您应该将其添加为基本 URI 和登录重定向 URI。您的设置应该如下所示:

[![Okta app settings](img/cfe6cc2a1cb646a0d410921f1031e71d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--S8bZM0fs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/simple-user-auth-react/okta-app-settings-d64bd752eb430fecd822ec90eef71625b91fbce03be71a8591ba8758531ef7ec.png)

点击**完成**保存你的应用，然后复制你的**客户端 ID** 并作为一个变量粘贴到你的项目根目录下的一个名为`.env.local`的文件中。这将允许您在代码中访问该文件，而无需在源代码管理中存储凭据。您还需要添加您的组织 URL(不带`-admin`后缀)。环境变量(除了`NODE_ENV`之外)需要以`REACT_APP_`开头，以便 Create React App 读取它们，所以文件应该看起来像这样:

```
REACT_APP_OKTA_ORG_URL=https://{yourOktaDomain}
REACT_APP_OKTA_CLIENT_ID={yourClientId} 
```

您可能需要重新启动服务器才能识别这些更改。您可以找到正在运行的实例，然后点击`ctrl-c`关闭它。然后用`yarn start`再运行一次。

向 React 应用添加 Okta 认证的最简单方法是使用 [Okta 的 React SDK](https://github.com/okta/okta-oidc-js/tree/master/packages/okta-react) 。您还需要添加路由，这可以使用 [React 路由器](https://reacttraining.com/react-router/)来完成。继续添加这些依赖项:

```
yarn add @okta/okta-react@1.2.0 react-router-dom@4.3.1 
```

现在，为了在 React 应用程序中使用 Okta，您需要将应用程序包装在一个提供程序中。在您的`src/index.js`中，您需要呈现一个`<Router>`组件，而不是单独呈现`<App />`组件。它使用 React Router 来控制应用程序中不同的 URL 路径。接下来，您需要包含`<Security>`组件作为子组件。这允许 Okta 将一个`auth`对象添加到上下文中，可以从 React 树中的子节点读取该对象。然后你需要几个`Route`来让路由器知道每条路由显示什么。对于默认路线，您将显示`App`，所以一切看起来应该和以前一样。但是您还将添加一个`/implicit/callback`路由，让 Okta 处理 OAuth 回调。将对`ReactDOM.render`的调用替换为以下内容:

```
import { BrowserRouter as Router, Route } from 'react-router-dom';
import { Security, ImplicitCallback } from '@okta/okta-react';

ReactDOM.render(
  <Router>
    <Security
      issuer={`${process.env.REACT_APP_OKTA_ORG_URL}/oauth2/default`}
      client_id={process.env.REACT_APP_OKTA_CLIENT_ID}
      redirect_uri={`${window.location.origin}/implicit/callback`}
    >
      <Route path="/" exact component={App} />
      <Route path="/implicit/callback" component={ImplicitCallback} />
    </Security>
  </Router>,
  document.getElementById('root')
); 
```

要将 Okta 的 auth 绑定到 React 组件，您需要将它包装在一个`withAuth`中，这将添加一个`auth`属性。不过，在这样做之前，您可以通过再次使用钩子来简化事情。在这里，我将让您创建一个自定义钩子，它接受`auth`属性并返回`authenticated`状态以及当前的`user`。创建一个新文件`src/auth.js`，包含以下内容:

```
import { useState, useEffect } from 'react';

export const useAuth = auth => {
  const [authenticated, setAuthenticated] = useState(null);
  const [user, setUser] = useState(null);

  useEffect(() => {
    auth.isAuthenticated().then(isAuthenticated => {
      if (isAuthenticated !== authenticated) {
        setAuthenticated(isAuthenticated);
      }
    });
  });

  useEffect(() => {
    if (authenticated) {
      auth.getUser().then(setUser);
    } else {
      setUser(null);
    }
  }, [authenticated]);

  return [authenticated, user];
}; 
```

这里您使用了两种状态:`authenticated`和`user`，它们都以`null`开始。每次呈现组件时，auth 都会检查用户是否通过了身份验证。如果身份验证已更改，它会更新状态。最初会是`null`，但是第一次之后，总是不是`true`就是`false`。每当`authenticated`状态改变时，就会触发一个调用来获取更多关于用户的信息。如果用户没有通过认证，您可以跳过检查，只需将用户设置为`null`。

回到`src/App.js`，你需要导入 Okta 提供的`withAuth`高阶组件，以及你新的`useAuth`钩子。

```
import { withAuth } from '@okta/okta-react';
import { useAuth } from './auth'; 
```

用`withAuth`包装`App`，并更改函数以接受一个`auth`参数。之前我们有:

```
const App = () => {
  // ...
} 
```

现在你应该已经:

```
const App = withAuth(({ auth }) => {
}); 
```

在`joke`状态下添加新的钩子:

```
const [authenticated, user] = useAuth(auth); 
```

您现在可以在“获取新笑话”按钮后添加一个新按钮。当`authenticated`变量为`null`时，意味着 Okta 还没有初始化，所以你不能确定用户是否登录。您可以显示某种加载指示器，或者您可以简单地不呈现按钮，直到您确定身份验证的状态:

```
{authenticated !== null && (
  <button
    onClick={() => authenticated ? auth.logout() : auth.login()}
    className="App-link"
  >
    Log {authenticated ? 'out' : 'in'}
  </button>
)} 
```

现在，用户可以登录或退出，但除了将按钮从“登录”更改为“退出”之外，它实际上并不做任何事情。ICNDb API 支持向 URL 添加名和姓，用这个名字替换 Chuck Norris。在调用`fetch`之前，修改`url`如果有`user` :

```
if (user) {
  url.searchParams.set('firstName', user.given_name);
  url.searchParams.set('lastName', user.family_name);
} 
```

最后，添加一个新的效果，每当`user`状态改变时重置笑话。这将导致应用程序在用户登录后获取一个带有用户姓名的新笑话，或者在用户注销时获取一个默认的 Chuck Norris 笑话。

```
useEffect(() => {
  setJoke('');
}, [user]); 
```

您的最终代码应该是这样的:

```
import React, { useState, useEffect } from 'react';
import { decode } from 'he';
import { withAuth } from '@okta/okta-react';

import logo from './chuck-norris.png';
import './App.css';
import { useAuth } from './auth';

const App = withAuth(({ auth }) => {
  const [joke, setJoke] = useState('');
  const [authenticated, user] = useAuth(auth);

  const fetchJoke = async signal => {
    const url = new URL('https://api.icndb.com/jokes/random');
    if (user) {
      url.searchParams.set('firstName', user.given_name);
      url.searchParams.set('lastName', user.family_name);
    }
    const response = await fetch(url, { signal });
    const { value } = await response.json();

    setJoke(decode(value.joke));
  };

  useEffect(() => {
    if (!joke) {
      const controller = new AbortController();
      fetchJoke(controller.signal);

      return () => controller.abort();
    }
  }, [joke]);

  useEffect(() => {
    setJoke('');
  }, [user]);

  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>{joke || '...'}</p>
        <button className="App-link" onClick={() => setJoke('')}>
          Get a new joke
        </button>
        {authenticated !== null && (
          <button
            onClick={() => authenticated ? auth.logout() : auth.login()}
            className="App-link"
          >
            Log {authenticated ? 'out' : 'in'}
          </button>
        )}
      </header>
    </div>
  );
});

export default App; 
```

[![Jon Snow joke](img/b220576ea44b57a93195a2b6018aa4fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zG2PoO8S--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://developer.okta.com/assets-jekyll/blog/simple-user-auth-react/jon-snow-joke-0e7e8750bfa5f03240bcaa7095bfbbef950406a0dd62da45ffcc1e6d29a485dc.png)

## 了解 React 和安全用户认证的更多信息

希望您已经从这个应用程序中获得了一些乐趣，并了解了向 React 应用程序添加身份验证是多么容易。如果想看最终的代码样本做参考，可以在 GitHub 上找到[。](https://github.com/oktadeveloper/okta-react-user-auth-example)

要获得更多使用 Okta 和 React 的例子，请查看其他一些帖子，或者浏览 Okta 开发者博客。

*   [用 Node、React 和 Okta 建立用户注册](https://dev.to/blog/2018/02/06/build-user-registration-with-node-react-and-okta)
*   [在 15 分钟内构建带有用户认证的 React 应用](https://dev.to/blog/2017/03/30/react-okta-sign-in-widget)
*   [用 Laravel 和 React 构建一个基本的 CRUD 应用](https://dev.to/blog/2018/12/06/crud-app-laravel-react)
*   [用 Node 和 React 构建一个基本的 CRUD 应用](https://dev.to/blog/2018/07/10/build-a-basic-crud-app-with-node-and-react)

如果你对这篇文章有任何问题，请在下面添加评论。更多精彩内容，请在 Twitter 上关注 [@oktadev](https://twitter.com/oktadev) ，就像我们在脸书关注[，或者订阅](https://www.facebook.com/oktadevelopers/)[我们的 YouTube 频道](https://www.youtube.com/channel/UC5AMiWqFVFxF1q9Ya1FuZ_Q)。
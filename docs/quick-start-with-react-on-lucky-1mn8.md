# 从对幸运做出反应开始

> 原文：<https://dev.to/jwoertink/quick-start-with-react-on-lucky-1mn8>

这将是一个快速的开始，让一些简单的[反应](https://reactjs.org/)代码以最少的设置进入一个简单的[幸运](https://luckyframework.org/)应用程序，也以最少的设置。

## 第一步，设置幸运

确保您已经在本地安装了 lucky。你可以查看[安装指南](https://luckyframework.org/guides/installing/)来安装。

一旦安装完成，我们就可以生成我们的应用程序了！

```
$ lucky init
Project name? simple_react_on_lucky

Lucky can generate different types of projects

...

API only or full support for HTML and Webpack? (api/full): full

Lucky can be generated with email and password authentication

...

Generate authentication? (y/n): n

----------------------------

Done generating your Lucky project

  ▸ cd into simple_react_on_lucky
  ▸ check database settings in config/database.cr
  ▸ run bin/setup
  ▸ run lucky dev to start the server 
```

Enter fullscreen mode Exit fullscreen mode

运行 lucky 告诉您运行的最后几个步骤，然后您的应用程序将被设置。

## 第二步，加入 react

接下来，我们需要在我们的应用程序中添加 react。

```
yarn add react
yarn add react-dom 
```

Enter fullscreen mode Exit fullscreen mode

## 第三步，制作一个使用 react 的页面

现在，我们需要一个页面，在这里我们可以编写一些代码来安装我们的 react 应用程序。我们将创建一个简单的主页来覆盖 lucky 的默认主页。

打开`src/actions/home/index.cr`

```
# change
render Lucky::WelcomePage
# to
render Home::IndexPage 
```

Enter fullscreen mode Exit fullscreen mode

现在创建一个新的`src/pages/home/index_page.cr`文件。

```
class Home::IndexPage < MainLayout
  def content
    div(id: "app")
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 第四步，写出一些反应

打开`src/js/app.js`并添加您的 react 导入。你会看到里面已经有一些代码了，你可以在下面添加这些东西。担心以后是否需要现有的代码。

```
import React from 'react';
import ReactDOM from 'react-dom';

const mountElement = document.getElementById('app');

class App extends React.Component {
  render() {
    return React.createElement('h1', null, 'WOOT!');
  }
}

ReactDOM.render(React.createElement(App), mountElement); 
```

Enter fullscreen mode Exit fullscreen mode

现在在你的终端中运行`lucky dev`，如果我们没有搞砸任何事情，你应该会看到“WOOT！”在你的主页上！

显然，你会想为一个真正的应用程序添加更多的代码，而使用`React.createElement`函数会变得超级混乱。这至少应该给你一个跳跃点，告诉你如何开始对你的幸运申请做出反应。

你也可以看看[这个帖子](https://dev.to/mikeeus/react-components-in-lucky-with-laravel-mix-and-lucky-react-nlb)由 [@mikeeus](https://dev.to/mikeeus) 发上一些比较幸运的反应。
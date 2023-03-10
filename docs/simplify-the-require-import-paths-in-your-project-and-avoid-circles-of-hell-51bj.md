# [更新]简化项目中的需求/导入路径，避免../../../地狱的圈子

> 原文：<https://dev.to/mjraadi/simplify-the-require-import-paths-in-your-project-and-avoid-circles-of-hell-51bj>

你讨厌看到../../../代码中的任何地方？来吧，我将向你展示为什么你应该使用`babel-plugin-module-resolver`来工作得更快，写更干净的代码。

#### 更新 1(19 年 3 月 31 日):

正如 Pavel Lokhmakov 所建议的，我在这里创建了一个新的 GitHub repo [来实现本文中解释的功能，而不需要`eject`应用程序。](https://github.com/mjraadi/babel-plugin-module-resolver-customize-cra) [`react-app-rewired`](https://github.com/timarney/react-app-rewired) 和 [`customize-cra`](https://github.com/arackaf/customize-cra) 都是库，可以让你调整`create-react-app` webpack/babel 配置，而不需要使用“弹出”。
只需将这些包作为开发依赖项安装，并在项目的根目录下创建一个名为`config-overrides.js`的新文件，并将您的自定义配置放在那里。然后你所要做的就是根据`react-app-rewired`文件更新你的 npm 脚本。

#### 灵感

我从来不喜欢这样写代码:

```
import NavBar from '../../components/NavBar'; 
```

Enter fullscreen mode Exit fullscreen mode

对我来说，它似乎非常混乱，不干净，不可维护。想象一下，您需要改变项目的目录结构。您将不得不仔细检查每个文件并更新您的代码以反映您的更改。说到不可维护性！
但是我喜欢从`node_modules`目录:
导入包的方式

```
// ES6 import syntax
import React, { Fragment } from 'react';

// CommonJS require syntax
const nodemailer = require('nodemailer'); 
```

Enter fullscreen mode Exit fullscreen mode

所以我渴望找到一种方法来导入/要求我的定制模块/组件，就像这样。 [`babel-plugin-module-resolver`](https://github.com/tleunen/babel-plugin-module-resolver) 来救援了！

### TL；速度三角形定位法(dead reckoning)

可以找到与本文相关的 GitHub repos:
[https://GitHub . com/mjraadi/babel-plugin-module-resolver-test-app](https://github.com/mjraadi/babel-plugin-module-resolver-test-app)
[https://GitHub . com/mjraadi/babel-plugin-module-resolver-customize-CRA](https://github.com/mjraadi/babel-plugin-module-resolver-customize-cra)

#### 它有什么作用？

我让插件作者解释一下:

> 这个插件可以简化项目中的需求/导入路径。例如，不使用像`../../../../utils/my-utils`这样复杂的相对路径，可以写成`utils/my-utils`。它会让你工作得更快，因为你不需要在访问文件之前计算你必须要上多少级目录。

如果你不知道`babel`是什么，它是一个 JavaScript 编译器，主要用于将 ECMAScript 2015+代码转换为当前和旧浏览器或环境中的 JavaScript 向后兼容版本。如果你在用`create-react-app`或类似的库构建一个应用，他们在幕后使用的是 babel。

#### 我们开始吧

在这里，我将向你展示如何在由`create-react-app`创建的应用程序中使用这个插件。用下面的命令创建一个新的应用程序:

```
$ create-react-app babel-plugin-module-resolver-test-app 
```

Enter fullscreen mode Exit fullscreen mode

`create-react-app`封装项目设置和所有配置，并为您提供创建生产就绪应用的工具。因为我们需要改变巴别塔的配置，我们需要`eject`我们的应用程序。弹出会将`create-react-app`的配置文件和开发/构建/测试脚本移动到你的应用目录中。

**注意:这是单向操作。一旦你`eject`，你就不能回去了！**
这对我们的用例来说很好，因为我们正在构建一个测试应用。继续运行下面的命令:

```
$ npm run eject 
```

Enter fullscreen mode Exit fullscreen mode

确认并继续。

**注:在写这篇文章的时候，有一个用`create-react-app`解释的 bug[在这里](https://github.com/facebook/create-react-app/issues/6099)。解决方法是删除`node_modules`目录并重新安装依赖项。**

安装依赖项:

```
$ npm install 
```

Enter fullscreen mode Exit fullscreen mode

通过在项目目录中执行以下命令来安装`babel-plugin-module-resolver`插件:

```
$ npm install --save-dev babel-plugin-module-resolver 
```

Enter fullscreen mode Exit fullscreen mode

打开`package.json`文件，寻找 babel 配置。这是弹出后的样子:

```
...
"babel": {
  "presets": [
      "react-app"
    ]
  },
... 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要告诉 babel 使用我们的模块解析器，并定义我们的根目录和别名。编辑你的 babel 配置部分，使它看起来像这样:

```
...
"babel": {
    "presets": [
      "react-app"
    ],
    "plugins": [
      ["module-resolver", {
        "root": ["./src"],
        "alias": {
          "dir1": "./src/Dir1",
          "dir2": "./src/Dir2"
        }
      }]
    ]
  },
... 
```

Enter fullscreen mode Exit fullscreen mode

现在在`src`目录下创建两个目录，分别叫做`Dir1`和`Dir2`。我们定义的别名将分别指向这些目录。

在`Dir1`目录下创建一个名为`ComponentA.js`的组件文件，并将下面的代码放入其中:

```
import React from 'react';
import ComponentB from 'dir2/ComponentB';

const ComponentA = () => (
  <p>
    Hello from <ComponentB />
  </p> );

export default ComponentA; 
```

Enter fullscreen mode Exit fullscreen mode

现在用下面的代码在`Dir2`目录下创建`ComponentB.js`:

```
import React from 'react';

const ComponentB = () => (
  <a 
    href="https://www.bitsnbytes.ir"
    className="App-link"
    target="_blank"
    rel="noopener noreferrer"
  >
    Bits n Bytes Dev Team
  </a> );

export default ComponentB; 
```

Enter fullscreen mode Exit fullscreen mode

现在编辑`src`目录下的`App.js`文件:

```
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';
import ComponentA from 'dir1/ComponentA';

class App extends Component {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <ComponentA />
        </header>
      </div>
    );
  }
}

export default App; 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我不必上下移动一个目录来导入组件。我们现在准备运行我们的应用程序，在您的终端中运行下面的命令:

```
$ npm start 
```

Enter fullscreen mode Exit fullscreen mode

您应该可以在浏览器中看到您的应用程序，没有任何问题。如果您有任何疑问或问题，请随时评论。

### 结论

自定义模块解析器将节省您的时间和处理的挫折../到处飞溅。它们需要一点时间来设置，并确保与现有工具的充分合作，但结果和视觉满意度永远不必看到../../../../../..大项目的初始投资是非常值得的。

### 关于我

我是一名全栈 web 开发人员，也是一个由才华横溢的专业自由开发人员组成的小组 Bits n Bytes Dev Team 的联合创始人，我们提供基于尖端技术的定制 web 应用程序开发服务，以满足客户独特的业务需求。

我可以被雇佣，你可以通过[https://www.bitsnbytes.ir/portfolio](https://www.bitsnbytes.ir/portfolio)查看我的投资组合网站，或者通过 [raadi@bitsnbytes.ir](mailto:raadi@bitsnbytes.ir) 联系我。
# 如何设置累计配置

> 原文：<https://dev.to/proticm/how-to-setup-rollup-config-45mk>

> 最初发布在[我的博客](https://www.devinduct.com/blogpost/9/how-to-setup-rollup-config)上。

随着 ECMAScript 和 NPM 时代的开始，前端开发变得非常有趣。我们可以在项目中使用许多软件包和工具，让我们的生活变得更加轻松。其中一个工具是 [rollup.js](https://rollupjs.org/guide/en) 。

让我们以一个简短的介绍开始这篇文章，并找出 rollup 实际上是什么，它对我们和我们的应用程序有什么作用。

官方声明:

> Rollup 是 JavaScript 的一个模块捆绑器，它将小段代码编译成更大更复杂的东西，比如一个库或应用程序。它为 JavaScript 的 ES6 修订版中包含的代码模块使用新的标准化格式，而不是以前的特殊解决方案，如 CommonJS 和 AMD。

我们来分解一下上面的说法。

> Rollup 是 JavaScript 的一个模块捆绑器，它将小段代码编译成更大的代码

如果我们将应用程序分成逻辑上独立的小部分，开发起来会容易得多。这样，我们在开发过程中降低了代码的整体复杂性，从而使其更易接近和维护。如果某人能够专注于较小的部分，而不是分析整个应用程序逻辑并试图隔离特定的代码块，那么他/她加入团队会更容易、更快。在中间插入一段新代码后，这会极大地增加出错的可能性，这是我们不希望的。

Rollup 帮助我们解决上述用例。它把我们的小片段打包成一个单一的代码库。为此，我们可以使用命令行或名为`rollup.config.js`的特定配置文件。

在本文中，我将介绍一种配置文件方法。

> 它对 ES6 中包含的代码模块使用新的标准化格式

这是非常整洁的。这使我们能够在 JavaScript 文件中编写导入/导出语句。我们可以导入数据、常数、函数、整个逻辑块...所有这些我们都可以写在下一代 JavaScript 中，让 rollup(及其插件)来创建浏览器可读的输出。可以指定输出格式，我们将在本文后面看到。

只是总结和回答了**什么**和**为什么**的问题。Rollup 是一个 JavaScript bundler(也可以用插件缩小输出),如果我们想使用 ECMAScript 规范中的新语法糖，比如`import`和`export`,我们就需要它。

**注意，下面的代码假设已经安装了 Node.js 和 NPM 包管理器，并且已经用`npm init`命令初始化了应用程序。**

# 安装汇总

要安装 rollup 并将其保存为开发依赖项，我们应该运行以下命令:

```
npm install rollup --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将安装 rollup 节点包，并更新位于我们的应用程序根文件夹中的`package.json`文件。

```
"devDependencies": {
    "rollup": "^1.10.0" // the version might be different in your case depending on the time reading this
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在应用程序根文件夹中创建一个名为`rollup.config.js`的新文件。在内部，添加以下内容。

```
export default {
    input: './src/main.js',
    output: {
        file: './build/bundle.min.js',
        format: 'iife',
        name: 'bundle'
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看看这些配置选项都为我们做了些什么:

1.  `input` -这是一个必需的设置，它代表了我们希望汇总处理的文件。它应该是应用程序的主要入口点，在这里我们可以导入应用程序所需的所有内容

2.  这是一个类似配置的对象，我们在其中设置处理结果。最低配置包括:

    2.1 `file` -这是我们的包将被创建的位置。它表示要写入的文件。通常在`build`或`dist`文件夹下。文件夹和文件将由汇总自动生成

    2.2 `format` -汇总支持多种输出格式。在我们的例子中，我们将使用一个立即调用的函数表达式(iife)

    2.3 `name` -代表已创建包的全局变量名称

> 其他格式可以在[这里](https://rollupjs.org/guide/en#big-list-of-options)找到，章节`output.format`

### 测试配置

现在，当我们有了我们的设置，我们可以测试是否一切正常。

首先，创建一个源文件夹`src`。这个文件夹将包含我们的应用程序源文件。在其中，创建应用程序入口点，一个名为`main.js`的文件和`index.html`页面。

接下来，让我们创建一个测试模块。在`src`文件夹中，创建一个子文件夹`modules`和一个名为`MyModule.js`的文件。在内部，添加以下内容:

```
const sayHello = (message) => {
    alert(message);
}

export default sayHello; 
```

Enter fullscreen mode Exit fullscreen mode

在`main.js`文件中添加导入语句并使用导入的函数:

```
import sayHello from './modules/MyModule';

sayHello('Hello from Rollup'); 
```

Enter fullscreen mode Exit fullscreen mode

打开`package.json`文件，在脚本设置下添加以下脚本:

```
"scripts": {
    "build": "rollup -c"
} 
```

Enter fullscreen mode Exit fullscreen mode

并运行以下命令:

```
npm run build 
```

Enter fullscreen mode Exit fullscreen mode

这将在我们的项目中创建一个名为`build`的新文件夹，其中包含生成的`bundle.min.js`文件。我们可以看到，通过将它添加为对我们的`index.html`页面的引用并在浏览器中打开它，这个包被正确地创建了。

```
<!DOCTYPE html>
<html lang="en">
    <meta charset="utf-8">
    <head>
        Rollup Example
    </head>

    <body>
    </body>

    <script src="../build/bundle.min.js"></script>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，打开页面后会立即弹出一个警告。

> 在这一点上，只有现代的浏览器才能正常工作。为了让它在不支持 ECMAScript 特性的浏览器上工作，我们需要包含一些插件。

# 下一代 JavaScript

### 安装通天塔

为了正确解析我们的模块并使其与旧浏览器兼容，我们应该包含 [babel](https://babeljs.io/) 来编译输出。如果你不熟悉它，那么 babel 是一个 JavaScript 编译器，它通过将下一代 JavaScript 代码编译到旧版本中来使其跨浏览器兼容。

为了继续这个例子，我们需要安装所需的包:

```
npm install @babel/core @babel/preset-env rollup-plugin-babel --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令将像这样更新我们的开发依赖关系:

```
// the versions might be different in your case depending on the time reading this
"devDependencies": {
    "@babel/core": "^7.4.3",
    "@babel/preset-env": "^7.4.3",
    "rollup": "^1.10.0",
    "rollup-plugin-babel": "^4.3.2"
 } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要在应用程序文件夹中创建一个 babel 配置文件`.babelrc`，内容如下:

```
{
  "presets": [
      "@babel/env"
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

完成这些操作后，babel 就配置好了，可以使用了。考虑到这篇文章是关于 rollup 的，请访问[官方巴别塔网站](https://babeljs.io/)了解更多信息。

### [T1】更新 rollup.config.js](#updating-rollupconfigjs)

上面的改动本身什么也做不了，因为我们没有告诉 rollup 它需要使用新安装的包。我们通过更新如下所示的`rollup.config.js`文件来做到这一点:

```
import babel from 'rollup-plugin-babel';

export default {
    input: './src/main.js',
    output: {
        file: './build/bundle.min.js',
        format: 'iife',
        name: 'bundle'
    },
    plugins: [
        babel({
            exclude: 'node_modules/**'
        })
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

我们保留了之前的`input`和`output`配置，添加了一个导入语句来包含`rollup-plugin-babel`，并引入了`plugins`配置选项。插件用于自定义汇总行为。在这种情况下，我们希望它将我们的 ECMAScript 编译成它的前身。

此外，我们已经排除了`node_modules`文件夹，以避免第三方脚本和库被编译。现在，我们准备再次运行我们的构建命令:

```
npm run build 
```

Enter fullscreen mode Exit fullscreen mode

预期的结果是，我们的捆绑包现在应该具有跨浏览器兼容的不同内容。

`bundle.min.js`没有巴别塔:

```
(function () {
    'use strict';

    const sayHello = (message) => {
        alert(message);
    };

    sayHello('Hello from Rollup');

}()); 
```

Enter fullscreen mode Exit fullscreen mode

与巴别塔:

```
(function () {
    'use strict';

    var sayHello = function sayHello(message) {
      alert(message);
    };

    sayHello('Hello from Rollup');

}()); 
```

Enter fullscreen mode Exit fullscreen mode

显然，我们可以看到不同之处。保留字`const`不再存在，它已被转换为`var`。此外，我们的`arrow function`已经被转换为跨浏览器兼容版本。

在浏览器中打开`index.html`页面后，结果应该是相同的，并且应该再次显示一条弹出消息。

# 处理非 ES 模块

到目前为止，我们的项目在没有任何节点模块依赖的情况下工作，唯一导入的模块是我们创建的测试模块。然而，在现实世界中，这种情况很少发生，我们的应用程序需要一个非 ES 模块。

rollup 没有提供对 CommonJS 模块的支持，因此我们需要更多的插件。为了使我们的项目与节点模块依赖项一起工作，我们需要安装以下软件包:

```
npm install rollup-plugin-node-resolve rollup-plugin-commonjs --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

`rollup-plugin-node-resolve`插件允许我们加载第三方模块，而`rollup-plugin-commonjs`插件将它们转换成 ES6 版本。

我们的`package.json`文件应该是这样的:

```
// the versions might be different in your case depending on the time reading this
"devDependencies": {
    "@babel/core": "^7.4.3",
    "@babel/preset-env": "^7.4.3",
    "rollup": "^1.10.0",
    "rollup-plugin-babel": "^4.3.2",
    "rollup-plugin-commonjs": "^9.3.4",
    "rollup-plugin-node-resolve": "^4.2.3"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 更新 rollup.config.js -第二部分

同样，rollup 需要知道它需要使用新的插件。我们用和`rollup-plugin-babel`插件一样的方式配置它们:

```
import babel from 'rollup-plugin-babel';
import resolve from 'rollup-plugin-node-resolve';
import commonjs from 'rollup-plugin-commonjs';

export default {
    input: './src/main.js',
    output: {
        file: './build/bundle.min.js',
        format: 'iife',
        name: 'bundle'
    },
    plugins: [
        babel({
            exclude: 'node_modules/**'
        }),
        resolve(),
        commonjs()
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

### 安装第三方库

现在我们已经准备好安装和使用我们的第一个第三方依赖项。`lodash`比如说。要安装它，运行以下命令:

```
npm install lodash --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

我们的`package.json`文件应该是这样的:

```
"devDependencies": {
    "@babel/core": "^7.4.3",
    "@babel/preset-env": "^7.4.3",
    "lodash": "^4.17.11",
    "rollup": "^1.10.0",
    "rollup-plugin-babel": "^4.3.2",
    "rollup-plugin-commonjs": "^9.3.4",
    "rollup-plugin-node-resolve": "^4.2.3"
} 
```

Enter fullscreen mode Exit fullscreen mode

### 更新 rollup.config.js -第 3 部分

为了使用它，我们再次需要稍微调整一下`rollup.config.js`文件。我们需要告诉 rollup 我们正在使用一个带有全局变量`_`的外部库。这是必需的，因为我们将把它导入到我们的`main.js`文件中。像这样更新配置:

```
import babel from 'rollup-plugin-babel';
import resolve from 'rollup-plugin-node-resolve';
import commonjs from 'rollup-plugin-commonjs';

export default {
    input: './src/main.js',
    output: {
        file: './build/bundle.min.js',
        format: 'iife',
        name: 'bundle',
        globals: {
            'lodash': '_',
        }
    },
    plugins: [
        babel({
            exclude: 'node_modules/**'
        }),
        resolve(),
        commonjs()
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

通过添加`globals`配置，我们确保 rollup 知道如何处理外部导入。

接下来，我们应该通过尝试使用 lodash 库来测试是否一切正常。例如，让我们使用`_concat`函数。

像这样更新`main.js`文件:

```
import sayHello from './modules/MyModule';
import _ from 'lodash';

const arr = _.concat([1, 2, 3], 4, [5]);
sayHello('Hello from Rollup and lodash: ' + arr); 
```

Enter fullscreen mode Exit fullscreen mode

并运行`build`命令:

```
npm run build 
```

Enter fullscreen mode Exit fullscreen mode

创建的`bundle.min.js`应该包含这两个模块。我们创建的测试模块和外部导入的`lodash`模块。

如果我们此时运行`index.html`页面，我们应该会看到一个不同消息的警告。它应该可以毫无问题地打印出`Hello from Rollup and lodash: 1,2,3,4,5`。

# 压缩输出

生产环境需要最终包的缩小版并不罕见。这是出于各种原因需要的，例如减小尺寸、加载速度、网络交付...等等。为了缩小它，我们需要安装另一个插件`rollup-plugin-uglify` :

```
npm install rollup-plugin-uglify --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

接下来，通过第四次更新`rollup.config.js`告诉 rollup 它需要使用它，在这个例子中:

```
import babel from 'rollup-plugin-babel';
import resolve from 'rollup-plugin-node-resolve';
import commonjs from 'rollup-plugin-commonjs';
import { uglify } from 'rollup-plugin-uglify';

export default {
    input: './src/main.js',
    output: {
        file: './build/bundle.min.js',
        format: 'iife',
        name: 'bundle',
        globals: {
            'lodash': '_',
        }
    },
    plugins: [
        babel({
            exclude: 'node_modules/**'
        }),
        resolve(),
        commonjs(),
        uglify()
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

并运行构建命令:

```
npm run build 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们看一下我们的`bundle.min.js`文件，代码应该可读性差很多:)如果你比较缩小前后的文件，应该有明显的大小差异。

# 出现

在下一篇文章中，我将介绍 CSS 和 HTML 文件的导入。

感谢您的阅读，下一篇文章再见。
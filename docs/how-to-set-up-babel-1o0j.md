# 如何设置巴别塔 101

> 原文：<https://dev.to/devinroche/how-to-set-up-babel-1o0j>

> 我经常需要查找如何设置 babel 来将 ES6 转换为 ES5，所以我想我应该写一篇文章来帮助自己和其他人！

## 项目设置

好了，让我们创建一个项目目录并初始化我们的包

```
mkdir babel-dummy-dir
npm init 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们安装所需的巴别塔软件包。

```
npm install --save-dev babel-cli babel-preset-env 
```

Enter fullscreen mode Exit fullscreen mode

babel-preset-env 是我们用来将 ES6 转换成 ES5 的主库。它通过版本映射工作，允许 babel 获取我们的 ES6 特性并找到 ES5 特性。

完成后，我们需要在根目录下创建我们的`.babelrc`文件。

```
touch .babelrc 
```

Enter fullscreen mode Exit fullscreen mode

然后我们需要添加以下内容。

```
{
  "presets": ["env"]
} 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们创建我们的`src`文件夹！

```
mkdir src
cd src && touch index.js 
```

Enter fullscreen mode Exit fullscreen mode

## 开始黑客攻击

现在我们可以在 index.js 中输入一些有趣的 ES6 代码。

```
class Dog {
  constructor (name) {
    this.name = name;
  }

  static bark(){
    console.log('woof');
  }
}

Dog.bark(); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们准备运行我们的程序，但是首先我们需要进入我们的`package.json`并添加我们的构建命令。我们可以在包中添加两个命令！

```
"build": "babel src -d build",
"start": "node build/index.js" 
```

Enter fullscreen mode Exit fullscreen mode

## 建造&运行

我们现在准备运行我们的代码。让我们用`npm run build`调用我们的构建命令，这将把 src 目录中的所有文件构建到我们的构建目录中。现在是时候运行我们的 ES5 代码了！输入`npm start`，您应该会看到类似下面的输出...

```
babel-dummy@1.0.0 start .../babel-dummy
node build/index.js

woof 
```

Enter fullscreen mode Exit fullscreen mode

好了，我们用 babel 把 ES6 代码编译成了 ES5！
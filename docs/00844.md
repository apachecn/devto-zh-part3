# 为节点设置类型脚本

> 原文：<https://dev.to/wrrnwng/setting-up-typescript-for-node-3ej2>

今天，我想从头开始讨论如何为 NodeJS 建立一个 TypeScript 项目。

这是我经常做的事情，所以把它作为我未来的一些 NodeJS 项目的启动报告是很好的。

让我们首先为这个项目创建一个新目录。我现在就称它为`ts-node-demo`,但是你可以在以后用它开始一个新项目的时候重新命名它。

```
mkdir ts-node-demo
cd ts-node-demo 
```

Enter fullscreen mode Exit fullscreen mode

让我们把它变成一个 git 仓库，并使用`yarn`作为我们的包管理器。最好不要将我们的任何依赖关系也提交到我们的 repo 中。

```
git init
yarn init
echo node_modules/ > .gitignore 
```

Enter fullscreen mode Exit fullscreen mode

现在我将为我们的 TypeScript NodeJS 项目添加最基本的依赖项。我不想做一个显式的构建步骤，然后运行作为构建结果的 plan JavaScript 文件，而是想使用`ts-node`库来完成这项工作。当然，我还会添加`TypeScript`以及 NodeJS 的类型定义。

```
yarn add ts-node typescript @types/node 
```

Enter fullscreen mode Exit fullscreen mode

如果我不用每次保存文件都重启开发服务器就好了，所以让我们添加`ts-node-dev`。如果你曾经使用过`nodemon`，这几乎是一样的，但是对于 TypeScript。

```
yarn add -D ts-node-dev 
```

Enter fullscreen mode Exit fullscreen mode

`package.json`

```
{  "name":  "ts-node-demo",  "version":  "1.0.0",  "main":  "index.js",  "author":  "Warren Wong<me@warrenwong.org>",  "license":  "MIT",  "dependencies":  {  "@types/node":  "^12.0.4",  "ts-node":  "^8.2.0",  "typescript":  "^3.5.1"  },  "devDependencies":  {  "ts-node-dev":  "^1.0.0-pre.39"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

我将通过将`index.js`改为`index.ts`来更新`package.json`以反映使用 TypeScript 的变化。我应该继续创建那个文件。

```
touch index.ts 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们添加一些脚本来启动生产应用程序以及启动开发服务器。因为像 Heroku 这样的服务会使用`yarn start`或`npm start`来启动生产服务器，所以我将把`"start"`定义为`"yarn ts-node index.ts"`。要启动开发服务器，我只想输入`yarn dev`，所以我将`"dev"`定义为`"yarn ts-node-dev index.ts"`。

`package.json`

```
{  "name":  "ts-node-demo",  "version":  "1.0.0",  "main":  "index.ts",  "author":  "Warren Wong <me@warrenwong.org>",  "license":  "MIT",  "dependencies":  {  "ts-node":  "^8.2.0",  "typescript":  "^3.5.1"  },  "devDependencies":  {  "@types/node":  "^12.0.4",  "ts-node-dev":  "^1.0.0-pre.39"  },  "scripts":  {  "start":  "yarn ts-node index.ts",  "dev":  "yarn ts-node-dev index.ts"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，为了检查是否一切顺利，我需要`index.ts`实际做一些事情。

`index.ts`

```
console.log("Hello, World!"); 
```

Enter fullscreen mode Exit fullscreen mode

好了，是时候测试一下了。

```
yarn dev

Using ts-node version 8.2.0, typescript version 3.5.1
Hello, World!

yarn start

Hello, World! 
```

Enter fullscreen mode Exit fullscreen mode
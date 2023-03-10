# 使用 TypeScript 设置 web pack 4–第 3.2 部分–程序员新手

> 原文：<https://dev.to/tirthbodawala/setting-up-webpack-4-with-typescript--part-32--the-novice-programmer-4oi3>

正如我们在前一集看到的，用 TypeScript 管理项目是困难的，如果我们不能正确地编译它，理解模块也是困难的。所以我们说，使用 TypeScript，我们可以输出单独编译的 JS 文件，也可以将所有内容输出到一个文件中，编译器选项、模块设置为 AMD 或 System

## 目标

让我们创建一个简单的配置，它可以输出 server.js 和 client.js，一个用于运行我们的服务器，一个用于包含在我们的客户端。

## Webpack 4

随着第 4 版的推出，Webpack 已经接近于零配置，但是这对我们没有帮助，因为我们需要处理。带有 docker 的 ts TypeScript 扩展。

因此，让我们从安装 webpack 和 webpack-dev-server 开始，然后在我们的根文件夹中创建一个简单的 webpack.config.js。

```
npm i webpack webpack-cli 
```

webpack.config.js 的内容如下:

```
const path = require('path');

const isProduction = typeof NODE_ENV !== 'undefined' && NODE_ENV === 'production';
const mode = isProduction ? 'production' : 'development';
const devtool = isProduction ? false : 'inline-source-map';
module.exports = [
  {
    entry: './src/client.ts',
    target: 'web',
    mode,
    devtool,
    module: {
      rules: [
        {
          test: /\.tsx?$/,
          use: 'ts-loader',
          exclude: /node_modules/,
          options: {
            compilerOptions: {
              "sourceMap": !isProduction,
            }
          }
        }
      ]
    },
    resolve: {
      extensions: ['.tsx', '.ts', '.js']
    },
    output: {
      filename: 'client.js',
      path: path.join(__dirname, 'dist', 'public')
    }
  },
  {
    entry: './src/server.ts',
    target: 'node',
    mode,
    devtool,
    module: {
      rules: [
        {
          test: /\.tsx?$/,
          use: 'ts-loader',
          exclude: /node_modules/
        }
      ]
    },
    resolve: {
      extensions: ['.tsx', '.ts', '.js']
    },
    output: {
      filename: 'server.js',
      path: path.resolve(__dirname, 'dist')
    },
    node: {
      __dirname: false,
      __filename: false,
    }
  }
]; 
```

你可以通过这个链接获得用 WebPack 设置 TypeScript 的官方参考:[https://webpack.js.org/guides/typescript/](https://webpack.js.org/guides/typescript/)正如你所看到的，我们使用 ts-loader 来加载带有扩展名的文件。我们也可以使用[awesome-typescript-loader](https://github.com/s-panferov/awesome-typescript-loader)来达到同样的目的。我更喜欢使用 WebPack 文档中提到的 [ts-loader](https://github.com/TypeStrong/ts-loader) 。

所以让我们也安装 ts-loader:

```
npm i ts-loader 
```

另外，看看我们的 server.js 编译器中的以下部分

```
node: {
  __dirname: false,
  __filename: false,
} 
```

我们通知 node 在编译后的文件中为我们提供变量 __dirname 和 __filename。

让我们改变文件的文件夹结构和命名，使之更有意义。

*   将`src/index.ts`重命名为`src/client.ts`
*   让编译后的 server.ts 的输出文件夹为`<project-root>/dist`
*   让编译后的 client.ts 的输出文件夹为`<project-root>/dist/public`,这样我们就不会暴露我们的 server.js
*   根据`<project-root>/dist/public`中的服务器静态内容对我们的 server.js 进行相应的更改
*   更新我们的 package.json 脚本来编译 server & client，并观察它们的变化和相应的服务器。

将 package.json 中的脚本更新到下面:

```
"scripts":  {  "start":  "webpack --colors && concurrently -r -n \"Webpack,Nodemon\"  \"npm run webpack-watch\"  \"npm run server-watch\"",  "webpack-watch":  "webpack --watch --colors",  "server-watch":  "nodemon --watch dist/server.js dist/server.js",  "test":  "echo \"Error: no test specified\" && exit 1"  }, 
```

我们正在做的是:我们首先简单地用 WebPack 编译，这样我们就有了一个初始的 server.js 和 client.js，然后我们同时启动 WebPack 和 Nodemon 来观察应用程序的变化，并相应地重新启动。

该配置为我们提供了所需的输出，如下所示:

[![](img/82bd3b1535a3f060fef3f7b6cbb91567.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Cmorh5sg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/www.atyantik.com/wp-content/uploads/2019/01/image.png%3Fw%3D640%26ssl%3D1)

很漂亮，我们包含了 component/global.ts 并在全局名称空间中输出名称，即“Tirth”。

## 代码拆分

以上对于新手项目来说是好的，但是在现实生活中，我们希望实现代码分割，使我们的应用程序更加健壮。让我们实现这一点！在 TypeScript 2.4 之后，动态导入是可用的，但是将 ESNext 作为 lib 选项的一部分包含在 compilerOptions 中。

更新我们的 tsconfig.json 如下:

```
{  "compilerOptions":  {  "noEmitOnError":  true,  "target":  "es5",  "lib":  ["DOM",  "ESNext",  "DOM.Iterable",  "ScriptHost",  "es2015.promise"],  "outDir":  "dist"  },  "include":  [  "src/**/*"  ]  } 
```

此外，为了使我们的代码工作，我们需要包含各自的 polyfill，现在让我们简单地将 polyfill.io 中的 polyfill.js 包含在 server.ts 中的 HTML 中。

> 动态导入利用了 Promise，因此浏览器必须支持 Promise，否则必须添加 polyfill。

*server . ts 的内容*

```
import express = require("express");
const path = require("path");

const app: express.Application = express();
const port: number = 3000;

app.use("/static", express.static(path.join(__dirname, 'public')));

app.get('/', (req: express.Request, res: express.Response): express.Response => {
  return res.send(`<!DOCTYPE html>
    <html>
      <head>
        My experiments with TypeScript
        <!---- ADDING POLYFILL -->
        <script src="https://cdn.polyfill.io/v2/polyfill.min.js"></script>
        <style>
          html,body {
            margin:0;
            padding:0;
            font-family: monospace;
            font-size: 20px;
          }
          #logger {
            background-color: #eee; 
            padding: 20px; 
            max-width: 100%; 
            height: 600px; 
            max-height: calc( 90vh - 2em - 35px); 
            margin: 10px;
            overflow-y: auto;
          }
          .log-entry {
            max-width: 100%;
            padding: 5px;
            background-color: #f6f6f6;
          }
          .title {
            margin: 10px;
          }
        </style>
      </head>
      <body>
        <h1 class="title">Logger:</h1>
        <div id="logger"></div>
        <script src="/static/client.js" async></script>
      </body>
    </html>
  `);
});
app.listen(port, (): void => console.log(`Example app listening on port ${port}!`)); 
```

所以基本上我们希望我们的代码被分割，这对于 WebPack 来说没什么大不了的。Webpack 和 TypeScript 确实支持动态导入，但仅当 module 选项设置为“esnext”时。因此，在这种情况下，我们需要更新三个文件:

1.  tsconfig.json
2.  webpack.config.js
3.  server.ts

添加编译选项模块为“es next”

```
{  "compilerOptions":  {  "noEmitOnError":  true,  "target":  "es5",  "module":  "esnext",  "lib":  ["DOM",  "ESNext",  "DOM.Iterable",  "ScriptHost",  "es2015.promise"],  "outDir":  "dist"  },  "include":  [  "src/**/*"  ]  } 
```

在 webpack.config.js 中，我们需要通知 webpack 应该使用“splitChunks”进行优化，并更新输出和条目，如下所示:

```
 entry: {
      client: './src/client.ts'
    },
    output: {
      filename: (chunkData) => {
        // do not change the chunk name as of now as we are importing client.js in server.ts
        return chunkData.chunk.name === 'client' ? '[name].js': '[name].[chunkhash].js';
      },
      // We have the prefix /static/ thus adding public path for generated chunks
      publicPath: '/static/',
      // Chunkhash
      chunkFilename: '[chunkhash].js',
      path: path.join(__dirname, 'dist', 'public')
    },
    optimization: {
      splitChunks: {
        chunks: 'all',
        minSize: 0
      }
    }, 
```

其他配置保持不变。

由于我们已经将模块的编译选项更改为“esnext”，我们的代码

`import express = require`将不再工作，因此我们必须将代码更新为以下内容:

```
import * as express from 'express'; 
```

这就是我们的代码分割。让我们测试一下，并将我们的 client.ts 更新如下:

```
let str: string = "Hello, World!";
function log(s: any): void {
  const logger: HTMLElement = document.getElementById("logger");
  const logWrapper: HTMLElement = document.createElement("div");
  logWrapper.className = "log-entry";
  logWrapper.innerHTML = `${(new Date).toLocaleTimeString()}: (${typeof s}):: ${JSON.stringify(s)}`;
  logger.appendChild(logWrapper);
}
log(str);

setTimeout(() => {
  import('./component/global').then(Global => {
    log(Global);
  });
}, 2000); 
```

因此，我们在 2 秒后请求全局组件，它应该在 2 秒后加载块，并记录加载的内容。

[![](img/7085e0735b38cf89e22217faf9bc7dc2.png)](https://i0.wp.com/www.atyantik.com/wp-content/uploads/2019/01/image-1.png?ssl=1)

这很酷！

PS:上面的教程还没有准备好生产，应该只用于实验和学习目的！

参考以前的博客系列/剧集:

*   [用目录结构建立项目，用 TSC 编译](https://dev.to/tirthbodawala/getting-started-with-typescript--part-1--the-novice-programmer-16jh-temp-slug-8330376)
*   使用 ExpressJS 和 TypeScript 创建一个 HTTP 服务器，这样就创建了一个 web 界面，可以帮助我们提交编译后的文件，并将输出记录到日志记录器控制台。
*   [模块-理解 TypeScript 中的导入-第 3.1 部分-程序员新手](https://dev.to/tirthbodawala/modules--understanding-imports-in-typescript--part-31--the-novice-programmer-21e5-temp-slug-6980064)

本集 3.2 部分的代码库位于:

【https://github.com/Atyantik/typescript-series/tree/part-3.2 T2】

帖子[用 TypeScript 设置 web pack 4-第 3.2 部分-新手程序员](https://www.atyantik.com/setting-up-webpack-with-typescript-part-3-2-the-novice-programmer/)最早出现在 yantik Technologies 的[上。](https://www.atyantik.com)
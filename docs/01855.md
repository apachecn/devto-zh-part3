# React 项目的 TypeScript monorepo

> 原文：<https://dev.to/stereobooster/typescript-monorepo-for-react-project-3cpa>

**UPD** :在社区的帮助下，一些问题得以解决。并不是`What I did`部分的所有步骤都被更新，但是 [github repo 包含了所有最近的变更](https://github.com/stereobooster/typescript-monorepo)。

我想为 React 项目创建 TypeScript monorepo。我试过了，但我对我的结果不满意。这个帖子描述了我所做的。对如何改进设置有什么建议吗？最后还有一个小小的咆哮。源代码是[这里的](https://github.com/stereobooster/typescript-monorepo)。

## 我想实现什么

*   Monorepo 项目，能够轻松地开发几个包，这些包可以单独使用，也可以一起使用
*   使用打字稿
*   对于 React 项目
*   对于测试库，我想从 Jest 开始，但是我们也可以选择其他东西
*   使用故事书(或类似工具)进行 React 组件开发和展示
*   (很高兴有，但可选)ESlint 和 [eslint-config-react-app](https://www.npmjs.com/package/eslint-config-react-app)
*   (最好有，但可选)汇总以捆绑和缩小
*   (最好有，但可选)用更漂亮的预提交钩子

### 套餐结构

*   `a` -实用程序库
*   `b` - React 组件库，依赖于`a`
*   `c` -另一个 React 组件库，依赖于`a`
*   `stories` -展示`b`和`c`包的组件以及用于开发的组件(最初的计划，以后可能会更改)

## 我做了什么

### 纱线

`yarn`而不是`npm`，因为它支持`workspaces`来链接交叉依赖。

在没有版本的根目录下创建`package.json`，因为我们不打算发布它，并使用`workspaces` :

```
"workspaces":  [  "packages/*"  ] 
```

### [上的 T1](#lerna)

我们将使用`lerna`在所有包中运行命令，并“提升”公共依赖关系。

创建`lerna.json` :

```
{  "packages":  ["packages/*"],  "npmClient":  "yarn",  "useWorkspaces":  true,  "version":  "0.0.1"  } 
```

### 打字稿

我们将使用`typescript`来检查类型并将 TS 向下编译成所需的 JS 文件(es5 或 ES2015，CommonJS 或 ES 模块)。

创建`tsconfig.base.json`。这是您需要添加以启用 monorepo 的内容:

```
{  "include":  ["packages/*/src"],  "compilerOptions":  {  "declaration":  true,  "declarationMap":  true,  "baseUrl":  ".",  "paths":  {  "@stereobooster/*":  ["packages/*/src"]  }  }  } 
```

创建`packages/a/`、`packages/b/`、`packages/c/`、`packages/stories/`。将`tsconfig.json`添加到每一个:

```
{  "include":  ["src"],  "extends":  "../../tsconfig.base.json",  "compilerOptions":  {  //  to  override  config  from  tsconfig.base.json  "outDir":  "lib",  "rootDir":  "src",  //  for  references  "baseUrl":  "src"  },  //  references  required  for  monorepo  to  work  "references":  [{  "path":  "../a"  }]  } 
```

对于`b`和`c`包，在`package.json`中添加:

```
"peerDependencies":  {  "@stereobooster/a":  "0.0.1"  },  "devDependencies":  {  "@stereobooster/a":  "*"  } 
```

我们需要`peerDependencies`来确保当最终用户安装包(`a`、`b`、`c`)时，它们将使用包`a`的同一个实例，否则，TypeScript 会抱怨不兼容的类型(特别是如果使用继承和私有字段)。在`peerDependencies`中，我们指定了一个版本，但是在`devDependencies`中我们不需要这样做，因为我们只需要简单地指示`yarn`使用我们本地拥有的任何版本的包。

现在我们可以建立项目了。添加到根目录`package.json` :

```
"scripts":  {  "build":  "lerna run build --stream --scope=@stereobooster/{a,b,c}"  } 
```

并以`package.json`为`a`、`b`、`c`、

```
"scripts":  {  "build":  "tsc"  } 
```

**问题 1** :由于子依赖关系(包`b`和`c`依赖于`a`，`stories`依赖于`a`，`b`，`c`)，我们需要相应地构建包，例如第一个`a`，第二个`b`和`c`，第三个`stories`。这就是为什么我们不能为构建命令的`lerna`使用`--parallel`标志。

### 做出反应

安装`@types/react`、`@types/react-dom`、`react`、`react-dom`。

添加到`tsconfig.base.json` :

```
"compilerOptions":  {  "lib":  ["dom",  "esnext"],  "jsx":  "react",  } 
```

添加到子包的`package.json` :

```
"peerDependencies":  {  "react":  "^16.8.0",  "react-dom":  "^16.8.0"  } 
```

### 是

我们将使用`jest`来运行测试。安装`@types/jest`、`@types/react-test-renderer`、`jest`、`react-test-renderer`。添加`jest.json`。启用打字稿:

```
{  "moduleFileExtensions":  ["ts",  "tsx",  "js"],  "transform":  {  "\\.tsx?$":  "ts-jest"  },  "testMatch":  ["**/__tests__/**/*.test.*"],  "globals":  {  "ts-jest":  {  "tsConfig":  "tsconfig.base.json"  }  }  } 
```

启用 monorepo:

```
"moduleNameMapper":  {  "@stereobooster/(.*)$":  "<rootDir>/packages/$1"  } 
```

我们也需要改变`tsconfig.base.json`，因为 [Jest 不支持 ES 模块](https://github.com/facebook/jest/issues/4842) :

```
"compilerOptions":  {  "target":  "es5",  "module":  "commonjs",  } 
```

将命令添加到`package.json`和

```
"scripts":  {  "pretest":  "yarn build",  "test":  "jest --config=jest.json"  } 
```

**问题 2** :我们将把模块发布为 ES5 + CommonJS，这对于 React package 来说毫无意义，因为 React package 需要某种捆绑器来使用包，比如 Parcel 或 Webpack。

问题 3 :存在子依赖关系，所以我们需要先构建所有的包，然后才能运行测试。这就是为什么我们需要`pretest`脚本。

### 故事书

根据[官方指令](https://storybook.js.org/docs/configurations/typescript-config/)安装故事书。

我们将在`package.json`中需要以下内容:

```
"scripts":  {  "start":  "start-storybook -p 8080",  "build":  "build-storybook -o dist"  },  "dependencies":  {  "@stereobooster/a":  "*",  "@stereobooster/b":  "*",  "@stereobooster/c":  "*"  },  "devDependencies":  {  "@babel/core":  "7.4.3",  "@storybook/addon-info":  "^5.0.11",  "@storybook/addons":  "5.0.6",  "@storybook/core":  "5.0.6",  "@storybook/react":  "5.0.6",  "@types/storybook__addon-info":  "^4.1.1",  "@types/storybook__react":  "4.0.1",  "awesome-typescript-loader":  "^5.2.1",  "babel-loader":  "8.0.5",  "react-docgen-typescript-loader":  "^3.1.0"  } 
```

在`.storybook`中创建配置(同样基于官方指示)。现在我们可以在`/src/b`中为`b`包创建故事，在`/src/c`中为`c`包创建故事。

故事书会关注`stories/src`的变化，但不会关注`a/src`、`b/src`、`c/src`的变化。我们将需要使用 TypeScript 来观察其他包中的变化。

添加到`a`、`b`、`c`套餐的`package.json`:

```
"scripts":  {  "start":  "tsc -w"  } 
```

并且到根`package.json` :

```
"scripts":  {  "prestart":  "yarn build",  "start":  "lerna run start --stream --parallel"  } 
```

现在，开发人员可以运行`yarn start`(在一个终端)和`yarn test --watch`(在另一个终端)来获得开发环境——脚本将观察变化并重新加载。

**问题 3** :存在子依赖关系，所以我们需要首先构建所有的包，只有在我们可以运行启动脚本之后。这就是为什么我们需要`prestart`脚本。

**问题 4** :如果故事中有类型错误，它会显示在浏览器中，但是如果`a`、`b`或`c`包中有类型错误，它只会显示在终端中，这将破坏所有的 DX，因为不是在编辑器和浏览器之间切换，你还需要切换到终端来检查是否有错误。

## 咆哮

所以我花了相当多的时间(半天？)去搞清楚所有的细节，结果令人失望。尤其让我失望的是**问题 2** 和**问题 4** 。更有甚者，我没有写一行实际的代码。令人沮丧的是，JS 生态系统并不重视约定胜于配置原则。我们需要生态系统中更多的创造-反应-应用和包裹。工具的构建应该考虑到交叉集成。

可能有解决问题的方法，也许我需要尝试`ava`和`esm`来修复**问题 2** ，但是我太失望了，我花了那么多时间来对抗附带的复杂性。相反，我决定停下来写这篇文章。

> 照片由 Ruby Schmank 在 Unsplash 上拍摄
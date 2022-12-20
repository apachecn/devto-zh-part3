# 从 react-scripts-ts 升级 react 和 TypeScript 应用程序以创建 React 应用程序 v2.1

> 原文：<https://dev.to/joshtynjala/upgrading-a-react-and-typescript-app-from-react-scripts-ts-to-create-react-app-v21-4akn>

近日，React 团队更新了 [**Create React App**](https://facebook.github.io/create-react-app/) 与[官方支持 **TypeScript**](https://facebook.github.io/create-react-app/docs/adding-typescript) 。Create React App 通过将构建过程的细节置于您的方式之外，使使用 React 创建 web 应用程序变得容易。无需配置 Webpack、Babel 和其他工具，因为 Create React App 提供了非常高质量的默认值。不过，这不是样板。除非您想，否则您甚至不需要查看构建依赖项和脚本，升级到新版本非常容易。TypeScript 是一个流行的 JavaScript 扩展，增加了编译时类型检查。在我看来，这两者的结合为现代前端 web 开发提供了最好的开发体验之一。

以前，要在 Create React App 中使用 TypeScript，您必须使用 Will Monk 创建的自定义 [*react-scripts-ts* 来创建您的项目。从 Create React App 的 2.1 版本开始，TypeScript 可以开箱即用。我有几个项目是从 *react-scripts-ts* 开始的，我最近决定开始升级它们以创建 React App v2.1。我想我应该分享一下我在这个过程中遵循的步骤和我的经验。](https://www.npmjs.com/package/react-scripts-ts)

### 创建新的基线应用

首先，我决定用 Create React App 创建一个全新的应用程序。我认为拥有一个基线应用程序是个好主意，在那里我可以将它的文件——比如 *package.json* 和*ts config . JSON*——与我现有项目中的文件进行比较。这个新应用程序会给我一些线索，告诉我到底需要做些什么来让现有的应用程序与新版本的 Create React 应用程序正常工作。

为了创建一个新的应用程序，我在终端上运行了以下命令:

```
npx create-react-app my-new-app --typescript 
```

### 更新库依赖关系

在我正在升级的现有项目中，我的一些依赖项已经有一段时间没有更新了，所以我决定的下一步是更新 *react* 、 *react-dom* 、 *typescript* 依赖项，以及类型定义，如 *@types/react* 、 *@types/react-dom* 、 *@types/node* 和 *@types/jest* 。更新完 *package.json* 中的版本后，我跑`npm install`把它们都下载了。

我暂时没有动过*反应脚本 ts* 。这个想法是为了确保现有项目使用与新基线项目完全相同的依赖版本，并且它仍然应该使用 *react-scripts-ts* 正确构建。最好确保我不会在 React 或 TypeScript 的新版本中遇到任何问题，这些问题可能会让我在解决由*React-scripts*和官方 *react-scripts* 之间的差异引起的任何问题时感到困惑。

我需要对做一些小的调整，这样新版本的 TypeScript 编译器才会满意，所以我采取了这个额外的步骤是一件好事。

### 从*react-scripts*切换到 *react-scripts*

一旦我所有的库依赖项都匹配了，项目仍然会用*react-scripts*构建，是时候切换到官方的 *react-scripts* 了。在 *package.json* 里面，我用 *react-scripts* 替换了*react-scripts*依赖项。我从我的新基线项目中复制了 *react-scripts* 的版本号。然后，我需要更新 *package.json* 中的 npm 脚本，以使用 *react-scripts* :

```
"scripts":  {  "start":  "react-scripts start",  "build":  "react-scripts build",  "test":  "react-scripts test",  "eject":  "react-scripts eject"  } 
```

在上面的每个 npm 脚本中，我用 *react-scripts* 替换了*react-scripts*。然后，在**构建**脚本中，我看到我现有的项目包含了一个`--env=jsdom`选项，但是新的基线项目没有。我去掉了这个选项，这样它们就匹配了。

### 更新配置文件

接下来，我比较了两个项目中的 *tsconfig.json* 。我可以看到一些主要的区别，比如输出 es 模块而不是 CommonJS，以及保留 JSX 以便 Babel 可以处理它而不是让 TypeScript 编译器做所有的事情。我决定将 *tsconfig.json* 的内容从新的基线项目复制到现有的项目中，然后做一些小的调整。我最终将`lib`编译器选项设置为`["es2015", "dom"]`,这样我就可以访问一些更新的 JS APIs，我将`strict`编译器选项设置为`false`,因为我还没有更新我的项目以使用严格模式。除此之外，我使用了 Create React 应用程序的默认设置，这些设置似乎都运行良好。

同样，我比较了两个项目中的 *package.json* 。我已经更新了依赖项和 npm 脚本，所以这一点上的差异非常小。我看到新的基线项目包括两个新的字段，它们不在现有项目中，`eslintConfig`和`browserslist`。Create React App 提供的这些默认值(配置 eslint 和 Babel)看起来没问题，所以我把它们复制过来了。

理论上，迁移过程应该在此时完成。如果一切顺利，您应该能够运行`npm start`来启动开发服务器，或者您可以运行`npm run build`来创建生产构建。在我的项目中，我遇到了一些需要解决的问题，但不是什么大问题。

### 修复编译器错误

我使用 [*react-loadable*](https://jamie.build/react-loadable.html) 库，在运行时使用代码分割加载我的应用程序的一部分。我的输入最初是这样的:

```
import * as ReactLoadable from "react-loadable" 
```

然而，下面的代码似乎有问题:

```
let AdminHomeScreen = ReactLoadable({
  loader: () => import("./AdminHomeScreen"),
  loading: LoadingScreen
}); 
```

TypeScript 编译器给了我以下错误:

> 无法调用其类型缺少调用签名的表达式。类型“{ default:Loadable；map<props exports="" extends="" string="" any="">(options:options with map<props exports="">):(component class<props any="">& LoadableComponent)|(function component<props>& LoadableComponent)；preloadAll():许诺<...>；preloadReady():Promise<...>；捕获:component type<...>；“}”没有兼容的调用签名。</props></props></props></props>

出于某种原因，我无法将`ReactLoadable`模块作为函数调用。原来我需要将导入改为这样:

```
import ReactLoadable from "react-loadable" 
```

为了像调用函数一样调用模块，需要导入模块的*默认导出*。我认为这可能与 *tsconfig.json* 中从 CommonJS 模块切换到 es 模块有关。

修复这个问题后，我可以成功地运行`npm start`来启动开发服务器，并且我可以在 web 浏览器中打开我的应用程序并使用它！然而，我发现了我需要解决的最后一个问题。

### 修复生产版本

当`npm start`为开发工作时，当我运行`npm run build`来创建我的 React 应用程序的生产版本时，我得到了一个奇怪的错误。

> module.js:550
> 
> 抛出 err
> 
> ^
> 
> 错误:在 function . module . _ resolve filename(module . js:548:15)
> 
> 在 function . module . _ load(module . js:475:25)
> 
> 在 module . require(module . js:597:17)
> 
> 在 require(internal/module . js:11:18)
> 
> 处找不到模块“web pack/lib/request shortener”
> 
> 。(C:\ Users \ jo sht \ Development \ karaoke-butler \ karaoke \ client \ node _ modules \ terser-web pack-plugin \ dist \ index . js:19:25)
> 
> at 模块。_ compile(module . js:653:30)
> 
> at object . module . _ extensions..js(module . js:664:10)
> 
> at module . load(module . js:566:32)
> 
> at tryModuleLoad(module . js:506:12)
> 
> at function . module . _ load(module . js:498:3)
> 
> NPM ERR！代码 ELIFECYCLE
> 
> npm ERR！errno 1
> T26】NPM ERR！[react-client @ 1 . 0 . 0](mailto:react-client@1.0.0)build:`react-scripts build`
> 
> NPM ERR！退出状态 1
> 
> npm 错误！
> 
> npm ERR！在 [react-client@1.0.0](mailto:react-client@1.0.0) 构建脚本时失败。
> 
> npm ERR！这可能不是 npm 的问题。上面可能有额外的日志输出。
> 
> npm 错误！这个运行的完整日志可以在:
> 
> npm ERR！c:\ Users \ jo sht \ AppData \ Roaming \ NPM-cache _ logs \ 2018-11-25t 01 _ 57 _ 14 _ 654 z-debug . log

不幸的是，快速的谷歌搜索没有发现任何人在 Create React 应用程序上有同样的问题。然而，我看到一些人使用其他框架，如 Vue 或 Angular，也出现了类似的错误。最终，我发现有人发现这是臭名昭著的*包-lock.json* 内部的某种冲突。根据我的经验，自从 npm 开始使用这个文件，它就成了一个可怕的问题来源。

反正我删除了 *package-lock.json* 和 *node_modules* 文件夹。然后，我运行`npm install`从头开始安装我所有的依赖项。这招奏效了，因为`npm run build`开始正常工作了。

我真的不知道为什么 *package-lock.json* 似乎总是会导致这样的问题，但我发现删除这个文件和整个 *node_modules* 文件夹经常会修复我遇到的问题。不知道是我做的不对，还是 npm 的 bug。

### 我的项目升级了

在我的一些 web 项目中，更新依赖项有时会令人望而生畏。对他们中的一些人来说，我通常需要留出一整天来解决一切。考虑到这一点，我预计在从 *react-scripts-ts* 升级到 Create React App 正式版的过程中会遇到更多困难。然而，我的应用程序所需的更改相对较小。这个过程感觉非常顺利，我很高兴随着 React 团队的不断创新，我将能够在未来轻松更新到 Create React 应用程序的新版本。
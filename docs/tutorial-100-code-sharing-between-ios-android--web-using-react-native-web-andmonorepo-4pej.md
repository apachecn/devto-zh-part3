# 教程:如何使用 React Native、react-native-web 和 monorepo 在 iOS、Android 和 Web 之间共享代码

> 原文：<https://dev.to/brunolemos/tutorial-100-code-sharing-between-ios-android--web-using-react-native-web-andmonorepo-4pej>

让我们以正确的方式让我们的`react-native`应用程序在浏览器中工作。

> 本教程是为`react-native <= 0.61`制作的。如果你使用的是新版本，我建议你转而使用这个库:[brunolemos/react-native-we b-monorepo](https://github.com/brunolemos/react-native-web-monorepo)，我会保持它的更新🙌

* * *

## 我为什么要写这个？

你好👋我是[布鲁诺莱默斯](https://twitter.com/brunolemos)。我最近启动了一个名为[dev hub-TweetDeck for GitHub](http://github.com/devhubapp/devhub)的项目，而[引起人们注意的一件事](https://twitter.com/brunolemos/status/1072871009651384320?s=21)是，这是一个由单一开发者制作的应用程序，可在 6 个平台上使用:Web (react-native-web)、iOS ( `react native`)、Android ( `react native`)、macOS、Windows 和 Linux ( `electron`，目前为)，它们之间几乎 100%共享代码。它甚至与服务器共享一些代码！直到几年前，这还需要一个 3 人以上的团队。

从那以后，我收到了几十条推文和私人信息，询问如何实现同样的目标，在本教程中，我将带你浏览一遍。

## 什么是`react-native-web`？

如果你不熟悉 [react-native-web](http://github.com/necolas/react-native-web) ，这是一个由 [Necolas](https://twitter.com/necolas) (前 Twitter 工程师)开发的库，可以让你的`React Native`代码在浏览器中呈现。粗略地说，你将编写`<View />`，它将渲染`<div />`，确保所有风格渲染完全相同的东西。它做的不止这些，但是让我们保持简单。

新的推特就是用这种技术创建的，非常棒。

如果你已经知道`react-native`，你不需要学习任何新的语法。是[同一个 API](https://github.com/necolas/react-native-web#components) 。

* * *

## 总结

*   开始一个新的`React Native`项目
*   把我们的文件夹结构变成一个 monorepo
*   让`react-native`在 monorepo 中工作
*   在我们的 monorepo 包之间共享代码
*   使用`create-react-app`和`react-native-web`创建新的 web 项目
*   通过代码共享让`CRA`在我们的`monorepo`内部工作
*   ？？？
*   利润

* * *

## 循序渐进教程

### 开始新的`React Native`项目

*   `$ react-native init myprojectname`
*   `$ cd myprojectname`
*   `$ git init && git add . -A && git commit -m "Initial commit"`

> 注意:从头开始创建一个跨平台的应用程序比试图移植一个现有的纯移动(或者更难:纯 web)项目要容易得多，因为他们可能会使用许多特定于平台的依赖项。
> 
> 编辑:如果你使用 expo，似乎他们很快就会内置对 web 的支持[！](https://twitter.com/brunolemos/status/1105493974285017090?s=20)

### 把我们的文件夹结构变成一个 monorepo

Monorepo 意味着在一个库中有多个包，这样你就可以很容易地在它们之间共享代码。这并没有听起来那么简单，因为`react-native`和`create-react-app`都需要一些工作来支持 monorepo 项目。但是嘿，至少这是可能的！

为此，我们将使用一个名为`Yarn Workspaces`的特性。
要求: [Node.js](https://nodejs.org/en/) 、[纱](https://yarnpkg.com/lang/en/docs/install/)和 [React Native](https://facebook.github.io/react-native/docs/getting-started.html) 。

*   *确保你在项目根文件夹*
*   `$ rm yarn.lock && rm -rf node_modules`
*   `$ mkdir -p packages/components/src packages/mobile packages/web`
*   将所有文件(除了`.git`)移动到`packages/mobile`文件夹
*   将`packages/mobile/package.json`上的`name`字段从`packagename`编辑为`mobile`
*   在根目录下创建这个`package.json`来启用`Yarn Workspaces`:

```
{  "name":  "myprojectname",  "private":  true,  "workspaces":  {  "packages":  [  "packages/*"  ],  "nohoist":  []  }  "dependencies":  {  "react-native":  "0.61.3"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

*   在根目录下创建一个`.gitignore`:

```
.DS_Store
.vscode
node_modules/
yarn-error.log 
```

Enter fullscreen mode Exit fullscreen mode

*   `$ yarn`

### 在 monorepo 中制作 react-native 作品

*   *检查`react-native`的安装位置。如果是在`/node_modules/react-native`，好吧。如果是在`/packages/mobile/node_modules/react-native`，那就不对劲了。确保你有最新版本的`node`和`yarn`。还要确保在 monorepo 包之间使用完全相同的依赖版本，例如在`mobile`和`components`上使用`"react": "16.11.0"`，而不是在它们之间使用不同的版本。*

*   打开您最喜欢的编辑器，使用`Search & Replace`功能将所有出现的`node_modules/react-native/`替换为`../../node_modules/react-native/`。

*   对于 react-native <= 0.59，打开`packages/mobile/package.json`。您的`start`脚本目前在`/cli.js start`结束。把这个追加到最后:`--projectRoot ../../`。

*   打开`packages./mobile/metro.config.js`并在上面设置`projectRoot`字段，看起来像这样:

```
const path = require('path')

module.exports = {
  projectRoot: path.resolve(__dirname, '../../'),
  transformer: {
    getTransformOptions: async () => ({
      transform: {
        experimentalImportSupport: false,
        inlineRequires: false,
      },
    }),
  },
} 
```

Enter fullscreen mode Exit fullscreen mode

*   [解决方法]您当前需要将`react-native`依赖项添加到根`package.json`中，以便能够捆绑 JS:

```
 "dependencies":  {  "react-native":  "0.61.3"  }, 
```

Enter fullscreen mode Exit fullscreen mode

#### iOS 变化

*   `$ open packages/mobile/ios/myprojectname.xcodeproj/`
*   打开`AppDelegate.m`，找到`jsBundleURLForBundleRoot:@"index"`，用`packages/mobile/index`替换`index`
*   还是在 Xcode 里面，点击你左边的项目名称，然后进入`Build Phases` > `Bundle React Native code and Images`。将其内容替换为:

```
export NODE_BINARY=node
export EXTRA_PACKAGER_ARGS="--entry-file packages/mobile/index.js"
../../../node_modules/react-native/scripts/react-native-xcode.sh 
```

Enter fullscreen mode Exit fullscreen mode

*   `$ yarn workspace mobile start`

您现在可以运行 iOS 应用程序了！💙选择一个 iPhone 模拟器，按下 Xcode 里面的“运行”三角按钮。

[![image](img/88f1ba1587ccdc70616c0f47552934c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ab3SNC46--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hw74nbu4d0h49kh90qmv.png)

#### 安卓变化

*   `$ studio packages/mobile/android/`
*   打开`packages/mobile/android/app/build.gradle`。搜索文本`project.ext.react = [...]`。编辑它，使它看起来像这样:

```
project.ext.react = [
    entryFile: "packages/mobile/index.js",
    root: "../../../../"
] 
```

Enter fullscreen mode Exit fullscreen mode

*   Android Studio 将显示一个立即同步弹出窗口。点击它。
*   打开`packages/mobile/android/app/src/main/java/com/myprojectname/MainApplication.java`。搜索`getJSMainModuleName`方法。将`index`替换为`packages/mobile/index`，看起来是这样的:

```
@Override
protected String getJSMainModuleName() {
  return "packages/mobile/index";
} 
```

Enter fullscreen mode Exit fullscreen mode

> 如果出现`Cannot get property 'packageName' on null object`错误，尝试[禁用自动链接](https://github.com/react-native-community/cli/issues/655)

您现在可以运行 Android 应用程序了！💙按下 Android Studio 中的“运行”绿色三角按钮，选择模拟器或设备。

[![image](img/9e6d9634111c080f8c3c00fcf4afb97d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MJOJuhz6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zhpmuclw1ekywgu5fw3t.png)

### 在我们的 monorepo 包之间共享代码

我们在 monorepo 中创建了很多文件夹，但目前只使用了`mobile`。让我们为代码共享准备好我们的代码库，然后将一些文件移动到`components`包中，这样它就可以被`mobile`、`web`以及我们将来决定支持的任何其他平台(例如:`desktop`、`server`等)重用。).

*   创建包含以下内容的文件`packages/components/package.json`:

```
{  "name":  "components",  "version":  "0.0.1",  "private":  true  } 
```

Enter fullscreen mode Exit fullscreen mode

*   *【可选】如果你决定将来支持更多的平台，你可以为他们做同样的事情:创建一个`packages/core/package.json`、`packages/desktop/package.json`、`packages/server/package.json`等等。每个字段的名称必须是唯一的。*

*   打开`packages/mobile/package.json`。添加您用作依赖项的所有 monorepo 包。在本教程中，`mobile`只使用了`components`包:

```
"dependencies":  {  "components":  "0.0.1",  ...  } 
```

Enter fullscreen mode Exit fullscreen mode

*   *停止正在运行的 react-native 打包程序*
*   `$ yarn`
*   `$ mv packages/mobile/App.js packages/components/src/`
*   打开`packages/mobile/index.js`。将`import App from './App'`替换为`import App from 'components/src/App'`。*这就是神奇之处。一个包现在可以访问其他包了！*
*   编辑`packages/components/src/App.js`，用`Welcome to React Native monorepo!`替换`Welcome to React Native!`，这样我们就知道我们正在渲染正确的文件。
*   `$ yarn workspace mobile start`

耶！您现在可以刷新正在运行的 iOS/Android 应用程序，并查看来自我们共享组件包的屏幕。🎉

*   `$ git add . -A && git commit -m "Monorepo"`

## Web 项目

> 注意:您可以重用多达 100%的代码，但这并不意味着您应该这样做。建议在平台之间有一些差异，让用户感觉更自然。为此，您可以创建以`.web.js`、`.ios.js`、`.android.js`或`.native.js`结尾的特定于平台的文件。参见[示例](https://github.com/devhubapp/devhub/tree/v0.90.0/packages/components/src/libs/bugsnag)。

### 使用 CRA 和 react-native-web 创建新的 web 项目

*   `$ cd packages/`
*   `$ npx create-react-app web`
*   `$ cd ./web` *(留在此文件夹内进行下一步)*
*   `$ rm src/*` *(或者手动删除`packages/web/src`里面的所有文件)*
*   *确保`package.json`中的依赖项在所有 monorepo 包之间完全相同。例如，将`web`和`mobile`软件包上的“反应”版本更新为“16.9.0”(或任何其他版本)。*
*   `$ yarn add react-native-web react-art`
*   `$ yarn add --dev babel-plugin-react-native-web`
*   创建包含以下内容的文件`packages/web/src/index.js`:

```
import { AppRegistry } from 'react-native'

import App from 'components/src/App'

AppRegistry.registerComponent('myprojectname', () => App)
AppRegistry.runApplication('myprojectname', {
  rootTag: document.getElementById('root'),
}) 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:当我们从一个`create-react-app`项目中的`react-native`导入时，它的`webpack`配置自动[为我们将其别名为`react-native-web`](https://github.com/facebook/create-react-app/blob/ff19e0a6d22ba070f5b4c3b511edb4e8d4995686/packages/react-scripts/config/webpack.config.js#L274-L278) 。

*   创建包含以下内容的文件`packages/web/public/index.css`:

```
html,
body,
#root,
#root > div {
  width: 100%;
  height: 100%;
}

body {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   在关闭`head`标签之前，编辑`packages/web/public/index.html`以包含我们的 CSS:

```
...
React App
<link rel="stylesheet" href="%PUBLIC_URL%/index.css" />
</head> 
```

Enter fullscreen mode Exit fullscreen mode

### 通过代码共享让 CRA 在我们的 monorepo 中工作

默认情况下，CRA 不会在`src`文件夹之外构建文件。我们需要让它做到这一点，这样它就可以理解我们 monorepo 包中的代码，其中包含 JSX 和其他非纯 JS 代码。

*   *待在`packages/web/`里等待接下来的步骤*
*   用以下内容创建一个`.env`文件(`packages/web/.env`):

```
SKIP_PREFLIGHT_CHECK=true 
```

Enter fullscreen mode Exit fullscreen mode

*   `$ yarn add --dev react-app-rewired`
*   用以下内容替换`packages/web/package.json`中的脚本:

```
"scripts":  {  "start":  "react-app-rewired start",  "build":  "react-app-rewired build",  "test":  "react-app-rewired test",  "eject":  "react-app-rewired eject"  }, 
```

Enter fullscreen mode Exit fullscreen mode

*   用以下内容创建`packages/web/config-overrides.js`文件:

```
const fs = require('fs')
const path = require('path')
const webpack = require('webpack')

const appDirectory = fs.realpathSync(process.cwd())
const resolveApp = relativePath => path.resolve(appDirectory, relativePath)

// our packages that will now be included in the CRA build step
const appIncludes = [
  resolveApp('src'),
  resolveApp('../components/src'),
]

module.exports = function override(config, env) {
  // allow importing from outside of src folder
  config.resolve.plugins = config.resolve.plugins.filter(
    plugin => plugin.constructor.name !== 'ModuleScopePlugin'
  )
  config.module.rules[0].include = appIncludes
  config.module.rules[1] = null
  config.module.rules[2].oneOf[1].include = appIncludes
  config.module.rules[2].oneOf[1].options.plugins = [
    require.resolve('babel-plugin-react-native-web'),
  ].concat(config.module.rules[2].oneOf[1].options.plugins)
  config.module.rules = config.module.rules.filter(Boolean)
  config.plugins.push(
    new webpack.DefinePlugin({ __DEV__: env !== 'production' })
  )

  return config
} 
```

Enter fullscreen mode Exit fullscreen mode

> 上面的代码覆盖了一些`create-react-app`的`webpack`配置，所以它在 CRA 的构建步骤中包含了我们的 monorepo 包

*   `$ git add . -A && git commit -m "Web project"`

就是这样！您现在可以在`packages/web`(或根目录下的`yarn workspace web start`)中运行`yarn start`来启动 web 项目，与我们的`react-native` `mobile`项目共享代码！🎉

[![image](img/1ddbee57b9384027c8e03e430aae24ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jHdLq3xp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/48ij2zssunapdrb4qnog.jpg)

## 一些疑难杂症

*   `react-native-web`支持大部分的`react-native` API，但是少了几个像`Alert`、`Modal`、`RefreshControl`、`WebView`；
*   如果你遇到一个不适合 monorepo 结构的依赖项，你可以把它添加到[no huit](https://yarnpkg.com/blog/2018/02/15/nohoist/)列表中；但是如果可能的话，请避免这样做，因为这可能会导致其他问题，特别是对于 metro bundler。

## 一些提示

*   导航可能有点挑战性；你可以使用像最近增加了网络支持的 [react-navigation](https://github.com/react-navigation/react-navigation) 这样的东西，或者你可以尝试在和 mobile 之间使用两种不同的导航器，以防你想通过牺牲一些代码共享来获得两个世界的最佳效果；
*   如果你计划与服务器共享代码，我推荐创建一个`core`包，只包含逻辑和帮助器函数(没有 UI 相关的代码)；
*   对于 Next.js，你可以用 react-native-web 查看他们的[官方示例](https://github.com/zeit/next.js/tree/master/examples/with-react-native-web)
*   对于原生 windows，可以尝试[react-native-windows](https://github.com/Microsoft/react-native-windows)；
*   对于原生 macOS，你可以使用新的苹果项目 Catalyst，但对它的支持还不是 100%([见我的推文](https://twitter.com/brunolemos/status/1185636022346043392?s=20))；
*   要安装新的依赖项，请使用根目录中的命令`yarn workspace components add xxx`。要从包中运行脚本，例如运行`yarn workspace web start`；要运行所有包中的脚本，运行`yarn workspaces run scriptname`；

感谢阅读！💙

如果你喜欢 react，可以考虑在 Dev.to 和 Twitter 上关注我。

* * *

#### 链接

*   源代码:[react-native-we b-mono repo](https://github.com/brunolemos/react-native-web-monorepo)
*   DevHub: [devhubapp/devhub](http://github.com/devhubapp/devhub) (使用此结构的生产应用+桌面+类型脚本)
*   推特:[@布鲁诺勒莫斯](https://twitter.com/brunolemos)
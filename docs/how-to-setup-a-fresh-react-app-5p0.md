# 如何设置新的 React 应用程序

> 原文：<https://dev.to/oliverandrich/how-to-setup-a-fresh-react-app-5p0>

**更新 2018-03-15** 有评论者要求支持 TypeScript。这很容易实现，因为大多数包都提供了现成的 TypeScript 支持。

最近，我为我团队的部分成员举办了为期两天的 [React](https://reactjs.org) 研讨会。在一些实验中成功使用后，我们决定所有的新产品都改用[反应](https://reactjs.org)。在培训期间，我们还讨论了如何使用 [create-react-app](https://facebook.github.io/create-react-app/) 建立一个新的 [React](https://reactjs.org) 项目。

在这篇博文中，我想记录我是如何建立我的 [React](https://reactjs.org) 项目的。这和我们在工作中做的一样，但是我们也包括一些商业许可的工具。

## 第一步:安装使用创建-反应-App

```
npx create-react-app your-new-react-project
cd your-new-react-project 
```

或者支持 TypeScript。

```
npx create-react-app your-new-react-project --typescript
cd your-new-react-project 
```

开始相当简单。我们刚刚使用 [create-react-app](https://facebook.github.io/create-react-app/) 建立了一个新项目。这已经包括了启动一个由 git 管理的 [React](https://reactjs.org) 应用的所有东西。好吧，实际上它几乎涵盖了一切，否则，这篇文章就到此为止了。

## 第二步:添加代码格式化程序

我喜欢编码标准。我喜欢我的编辑自动为我处理它。我喜欢在提交前阶段重新运行代码格式化。代码库中的所有代码都应该遵循定义的编码标准。是的，有些同事讨厌我这种态度。

从我的角度来看，最好的解决方案是，在这个项目中加入一些漂亮的朋友。[漂亮点的](https://prettier.io)负责格式化。而 [husky](https://github.com/typicode/husky#readme) 和 [lint-staged](https://github.com/okonet/lint-staged#readme) 则用来给你的 git 工作流添加更漂亮的东西。

```
npm install --save-dev husky lint-staged prettier 
```

接下来，我们必须为项目添加更漂亮的配置。创造一个**。prettierrc** 文件。

```
{  "trailingComma":  "es5",  "printWidth":  100  } 
```

最后一步是将 [husky](https://github.com/typicode/husky#readme) 和 [lint-staged](https://github.com/okonet/lint-staged#readme) 的一些配置添加到您的 **package.json** 文件中。

```
"husky":  {  "hooks":  {  "pre-commit":  "lint-staged"  }  },  "lint-staged":  {  "src/**/*.{js,jsx,ts,tsx,json,css,scss,md}":  [  "prettier --write",  "git add"  ]  }, 
```

不要忘记为你的[编辑器](https://prettier.io/docs/en/editors.html)添加所需的插件。对于 Visual Studio 代码，您希望使用扩展[漂亮的代码格式化程序](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)。

## 第三步:添加 Redux、React-Router 和 Redux-Thunk

我觉得，几乎每个项目都需要一个中心店和一个路由器。所以我总是在我的项目中加入 [redux](https://redux.js.org) 、 [redux-thunk](https://github.com/reduxjs/redux-thunk) 和 [react-router](https://reacttraining.com/react-router/web/guides/philosophy) 。

开店是另一回事。我暂时不考虑这个。

```
npm install redux react-redux redux-thunk
npm install react-router-dom
npm install --save-dev @types/react-router-dom         # Only if you use TypeScript 
```

## 第四步:添加 React-头盔

react-helmet 是一个不错的小库，可以在你的组件中操作你的头部。它与[反应路由器](https://reacttraining.com/react-router/web/guides/philosophy)配合得很好。

```
npm install react-helmet
npm install --save-dev @types/react-helmet 
```

## (可选)第五步:添加道具类型

如果您使用的是 TypeScript，那么您可以跳过这一步，因为通过使用 interfaces 和 React.FC.

```
import * as React from 'react'

interface IProps {
  // ... props interface 
}

const MyNewComponent: React.FC<IProps> = (props) => {...}; 
```

但是如果你不使用 TypeScript， [prop-types](https://github.com/facebook/prop-types) 在我看来是一个必不可少的库。我一直不明白，为什么要手动添加。 [prop-types](https://github.com/facebook/prop-types) 提供了对组件属性的运行时检查。

```
npm install prop-types 
```

## 第六步:添加 React 测试渲染器

虽然[道具类型](https://github.com/facebook/prop-types)可能会被排除在项目之外，但我完全不明白为什么 [create-react-app](https://facebook.github.io/create-react-app/) 背后的团队会排除 [react-test-renderer](https://reactjs.org/docs/test-renderer.html) 。Jest 是测试工具的首选。但是 [Jest](https://jestjs.io) 的[快照测试](https://jestjs.io/docs/en/tutorial-react)功能只能与 [react-test-renderer](https://reactjs.org/docs/test-renderer.html) 一起使用。所以，帮自己一个忙，将这个库添加到您的项目中。

```
npm install --save-dev react-test-renderer
npm install --save-dev @types/react-test-renderer         # Only if you use TypeScript 
```

## (可选)第七步:添加一些 UI 糖果

我非常欣赏材料设计。 [material-ui](https://material-ui.com) 是一个很棒的库，提供材料设计组件来反应。Material-UI 本身提供了一个漂亮的图标集，可以通过在项目中添加[材质设计图标](https://github.com/TeamWertarbyte/mdi-material-ui)包来完成。

```
npm install @material-ui/core
npm install @material-ui/icons
npm install mdi-material-ui
npm install typeface-roboto 
```

## 文件系统布局

在野外，您可以找到各种各样的文件系统布局。我更喜欢所有组件都保存在组件目录下的布局。

```
$ tree
.
├── README.md
├── package-lock.json
├── package.json
├── public
│   ├── favicon.ico
│   ├── index.html
│   └── manifest.json
└── src
    ├── components
    │   └── App
    │   ├── App.css
    │   ├── App.js (or App.tsx)
    │   └── App.test.js (or App.test.tsx)
    ├── store
    │   └── ...
    ├── index.js (or index.tsx)
    └── serviceWorker.js (or serviceWorker.ts) 
```

store 文件夹的内容是另一篇文章的主题，因为我还没有决定如何构建我的 redux 代码。目前我正在用 Firebase 做更多的后端工作，所以关于 redux 的帖子可能需要一些时间。

## 最后一个建议

我认为你不应该在用[创建-反应-应用](https://facebook.github.io/create-react-app/)创建的项目中运行 **npm 运行弹出**。好吧，“从不”可能相当于 90%的时候。很多教程建议在创建后立即弹出你的项目。但是你不会从中获得太多的好处，并且会失去由 [react-scripts](https://www.npmjs.com/package/react-scripts) 应用于你的项目的神话般的工具和理智的默认值。

文章[不要弹出你的 Create React 应用](https://medium.com/curated-by-versett/dont-eject-your-create-react-app-b123c5247741)更详细地介绍了为什么你不应该这样做。

图片来源:[马库斯·斯皮斯克](https://unsplash.com/photos/70Rir5vB96U?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/hacker?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)
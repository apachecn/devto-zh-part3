# 使用 Typescript 和 TSDX 发布 React 钩子库

> 原文：<https://dev.to/juliang/publishing-a-react-hooks-library-using-typescript-and-tsdx-36cg>

我开始了我的个人 hooks 库，并决定将其发布为一个 **npm 包**。在 TypeScript 中这样做并不简单，直到: **[palmerhq/tsdx](https://github.com/palmerhq/tsdx)** 。

我是这么做的。循序渐进。

## 第一步:创建项目

```
$ npx tsdx create bananahooks 
```

Enter fullscreen mode Exit fullscreen mode

该命令是交互式的，要求您选择一个模板。

```
? Choose a template …
  basic
❯ react 
```

Enter fullscreen mode Exit fullscreen mode

我选择了`react`模板，TSDX 帮我安装好了一切。

```
✔ Choose a template · react
✔ Created bananahooks
✔ Installed dependecines

  Awesome! You're now ready to start coding.

  I already ran yarn install for you, so your next steps are:
    cd bananahooks

  To start developing (rebuilds on changes):
    yarn start

  To build for production:
    yarn build

  To test your library with Jest:
    yarn test

  Questions? Feedback? Please let me know!
  https://github.com/jaredpalmer/tsdx/issues 
```

Enter fullscreen mode Exit fullscreen mode

## 第二步:添加代码和测试

TSDX 生成一个`index.tsx`文件、一个测试文件和一个示例文件夹。

我删除了示例文件夹，添加了我自己的[代码](https://github.com/JulianG/hooks/tree/master/src)和[测试](https://github.com/JulianG/hooks/tree/master/test)。

```
bananahooks                      bananahooks
└─ examples                      └─ src
└─ src                              └─ index.tsx
   └─ index.tsx                     └─ use-promise.tsx
└─ test                          └─ test
   └─ blah.test.tsx     ==>         └─ use-promise.test.tsx
└─ .gitignore                    └─ .gitignore
└─ package.json                  └─ package.json
└─ README.md                     └─ README.md
└─ tsconfig.json                 └─ tsconfig.json
└─ yarn.lock                     └─ yarn.lock 
```

Enter fullscreen mode Exit fullscreen mode

因为我的库只包含函数，所以我决定删除`example`文件夹，我依赖测试来代替。你可以在 [use-promise.test.tsx](https://github.com/JulianG/hooks/blob/master/test/use-promise.test.tsx) 中看到我是如何测试我的定制钩子的。我正在使用[反应-测试-库](https://github.com/testing-library/react-testing-library)。

## 第四步:在本地测试包

为了确保我的包能够像预期的那样工作，我将它导入到一个新的 React 项目:

```
yarn add ../path/to/bananahooks 
```

Enter fullscreen mode Exit fullscreen mode

但是有一个问题！

```
Invalid hook call. Hooks can only be called inside of the body of a function component. This could happen for one of the following reasons:
1\. You might have mismatching versions of React and the renderer (such as React DOM)
2\. You might be breaking the Rules of Hooks
3\. You might have more than one copy of React in the same app
See https://fb.me/react-invalid-hook-call for tips about how to debug and fix this problem. 
```

Enter fullscreen mode Exit fullscreen mode

用**纱**或 **npm** 安装本地包时有一个[已知问题](https://github.com/yarnpkg/yarn/issues/1334)。所有内容都被复制到项目的`node_modules`文件夹中，而不仅仅是最终打包的文件。这意味着现在有两个`node_modules`，因此，有两个 React 副本。如果是一模一样的版本也没关系。

解决方法是`yarn remove bananahooks`，然后从库项目中删除`node_modules`文件夹，然后再次运行`yarn add ../path/to/bananahooks`。很烦。

## 第五步:发布到 NPM

TSDX 用我的包名:`bananahooks`生成了一个`package.json`文件，但是我必须给它添加`author`、`license`和`repository`键:

```
 "author":  {  "name":  "Julian Garamendy",  "email":  "me@juliangaramendy.dev",  "url":  "https://juliangaramendy.dev"  },  "repository":  {  "type":  "git",  "url":  "https://github.com/JulianG/hooks"  },  "license":  "MIT", 
```

Enter fullscreen mode Exit fullscreen mode

我运行了`npm pack`命令，该命令生成一个`.tgz`文件，其中包含将要上传到注册表的包。我发现这有助于检查并确保我包含了正确的文件。**`.tgz`文件可以事后删除；出版不需要。**

然后我运行`npm publish`和[，因为我已经登录到 npm](https://docs.npmjs.com/cli/adduser) 了，我的包被上传并添加到注册表中。

我可以稍后通过手动编辑`package.json`文件或者运行 [`npm version`](https://docs.npmjs.com/cli/version.html) 来增加版本号。

## 结论

**[TSDX](https://github.com/palmerhq/tsdx)** 让创建类型脚本库变得非常容易。我将来肯定会经常使用它。

你可以在 GitHub 上看到[我的钩子库，在 NPM 上看到已经发布的](https://github.com/JulianG/hooks) [bananahooks](https://www.npmjs.com/package/bananahooks) 包。

我希望这是有用的。
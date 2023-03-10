# 设置简单的 React & TypeScript 项目

> 原文：<https://dev.to/krzysztofzuraw/setting-up-simple-react--typescript-project-5fbm>

# 您好👋🏻

这是我在 dev.to 上的第一篇博客文章。如果你想在 dev.to 上的文章前一周收到这些文章，你可以在:[https://krzysztofzuraw.com/newsletter](https://krzysztofzuraw.com/newsletter)上加入我的时事通讯

## 为什么

你可能会问自己，既然已经有这么多模板，为什么还要费心添加一个新模板呢？答案很简单——因为我能。
我也有一个想法，随着我的项目的增长，在这个模板中呈现变化，这样你就可以
看到添加了什么，为什么。

## 模板

在那里没有什么神奇的:你有[包商](https://parceljs.org)在做大部分的工作。你只需插入`index.tsx`，就像你在`index.html` :
中看到的那样

```
<!DOCTYPE html>
<html>
  <head>
    TypeScript + React with Parcel Sandbox
    <meta charset="UTF-8" />
  </head>

  <body>
    <div id="app"></div>

    <script src="src/index.tsx"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

一个更重要的配置是`tsconfig.json`,它告诉 TypeScript 编译器如何编译一个项目。从类型安全的角度来看，最重要的条目是:

```
"strict":  true, 
```

Enter fullscreen mode Exit fullscreen mode

其次是:

```
"noUnusedLocals":  true,  "noUnusedParameters":  true,  "noImplicitReturns":  true,  "noFallthroughCasesInSwitch":  true, 
```

Enter fullscreen mode Exit fullscreen mode

类型安全意味着你必须付出更多的努力来输入你的应用程序(比如确保没有隐含的 any ),但是回报是运行时有更少的 bug。我鼓励你每次都启用它。

因为我们仍然在 tsconfig 中——有一个名为`plugin`的条目，我在那里放了 [typescript-tslint-plugin](https://github.com/Microsoft/typescript-tslint-plugin) 。这个小家伙将在你的 VSCode 中运行微软的 tslint 插件。如果不使用 VSCode，可以删除这一行。

紧挨着`tsconfig`的是`tslint.json` -按照推荐的[规则](https://github.com/palantir/tslint/blob/master/src/configs/recommended.ts)和更漂亮的规则配置的打字稿文件。这个`tslint-config-prettier`条目应该是`extends`数组中的最后一个，以确保更漂亮的将根据 tslint 格式化代码。

我还添加了一个 [jest](https://jestjs.io/) 用于使用 [ts-jest](https://www.npmjs.com/package/ts-jest) 进行测试，它支持单元测试文件的类型脚本。

## 总结&TL；速度三角形定位法(dead reckoning)

我给你打字稿+用包裹操场反应:

*   [Codesandbox](https://codesandbox.io/s/38lvk3x7l5)
*   [Github](https://github.com/krzysztofzuraw-dot-com/react-typescript-parcel)
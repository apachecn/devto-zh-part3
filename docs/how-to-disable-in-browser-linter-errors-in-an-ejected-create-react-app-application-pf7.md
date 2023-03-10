# 如何在弹出的创建-反应-应用程序中禁用浏览器中的提示错误

> 原文：<https://dev.to/mcrowder65/how-to-disable-in-browser-linter-errors-in-an-ejected-create-react-app-application-pf7>

所以，你已经退出了用 create-react-app 创建的应用程序，你正在使用你自己的 linter，现在突然，当 linter 错误发生时，你得到一个错误屏幕。这可能是一件好事，但是如果有`no-console`作为错误，并且您希望在开发时记录事情，而不是在生产中记录事情，该怎么办呢？

打开`config/webpack.config.js`，删除 293-310 行:

```
// First, run the linter.
// It's important to do this before Babel processes the JS.
{
  test: /\.(js|mjs|jsx)$/,
  enforce: "pre",
  use: [
    {
      options: {
        formatter: require.resolve("react-dev-utils/eslintFormatter"),
        eslintPath: require.resolve("eslint"),
        failOnError: false,
        failOnWarning: false,
      },
      loader: require.resolve("eslint-loader")
    }
  ],
  include: paths.appSrc
}, 
```

我们刚刚移除了 linter 检查，当您在开发模式下运行您的应用程序时，以及当您的应用程序为生产而构建时，会完成该检查。在这两种情况下运行 linter 都没有意义。

然而，在运行`npm test`或`yarn test`的同时运行棉绒机是有意义的。

在`package.json`中，将`"test": "node scripts/test.js"`改为`"test": "yarn linter && yarn jest"`。

并添加以下脚本:

```
"linter": "eslint src --ext .js,.jsx",
"jest": "jest" 
```

现在，当您运行`npm test`或`yarn test`时，它将运行您的 linter 和 jest 单元测试！
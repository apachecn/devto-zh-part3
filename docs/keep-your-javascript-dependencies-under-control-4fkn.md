# 控制您的 Javascript 依赖关系

> 原文：<https://dev.to/svyatov/keep-your-javascript-dependencies-under-control-4fkn>

如今有了所有的 Javascript 库和它们的依赖项，很容易就忘乎所以，忘记了你的前端有多重。

对于 Webpack 支持的应用程序，这里有一个很棒的工具: [Webpack Bundle Analyzer](https://github.com/webpack-contrib/webpack-bundle-analyzer) 。它显示了放在`package.json`文件中的每个依赖项的确切大小(包括解析和 gzipped)。

下面是一个如何将它添加到 Rails 项目中的示例。

首先，我们需要将它添加到`package.json` :

```
# Save the package to your `devDependencies`
yarn add -D webpack-bundle-analyzer 
```

其次，将这个添加到您的`config/webpack/environment.js`文件中，就在`module.exports = environment;`行之前:

```
if (process.env.ANALYZE_BUNDLE) {
  const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
  environment.plugins.append('BundleAnalyzer', new BundleAnalyzerPlugin());
} 
```

第三，将其作为`yarn/npm run`任务:

```
// package.json
{
  // ...
  "scripts": {
    // ...
    "analyze": "ANALYZE_BUNDLE=true RAILS_ENV=production ./bin/webpack"
  },
  // ...
} 
```

完成了。现在可以这样运行:

```
yarn run analyze 
```

愉快地控制您的 Javascript 依赖关系！:)
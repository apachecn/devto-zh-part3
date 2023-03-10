# 下一个 React 应用的 7 个 Webpack 插件

> 原文：<https://dev.to/rleija_/7-webpack-plugins-for-your-next-react-app-133p>

*原帖@ [Linguine 博客](https://linguinecode.com/post/top-webpack-plugins)T3】*

## Webpack 优化

Webpack 附带了一个名为**优化**的配置属性。

在**优化**里面还有一个名为**的属性最小化**，和**的极小化**。

如果**最小化**等于真，那么**最小化器**内的一切都将触发。

[![Webpack optimize property](img/fa31eb843dc4c51395e284cdfa1a5976.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BQ1MOY15--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/18191438/webpack-optimization-prop.png)

接下来我将介绍的几个 Webpack 插件，它们应该在 **minimizer** 属性中。

### **Terser webpack 插件**

Terser 插件是一个很好的工具，可以最小化生产应用程序的 JavaScript 包文件。

这个插件的另一个好处是它支持 ES6+。

下面是 Terser 的基本配置。

[![Terser webpack plugin basic configuration](img/fbf5b35a84803d62e91c2213a5fdc91c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Py7ZLUpt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/18191112/webpack-terser-example-1024x1021.png)

资料来源: [Terser 插件](https://webpack.js.org/plugins/terser-webpack-plugin/)

### **优化 CSS 资产 Webpack 插件**

优化 CSS 资产 Webpack 插件是另一个用于产品构建的插件。

它有助于优化和最小化您的 React CSS 代码。

[![](img/092b3bbbee16b282f88d7f588421dd1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--exvcX5Qm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/18192425/optimize-css-assets-webpack-plugin-1024x509.png)

来源:[优化 css 资产插件](https://github.com/NMFR/optimize-css-assets-webpack-plugin)

## 网页包插件

### **HTML 网页包插件**

HTML Webpack 插件对于开发和生产构建都是一个方便的插件。

它告诉 Webpack 生成一个 HTML 文件，并用 JavaScript 代码注入一个脚本标记。

你所需要做的就是提供 HTML 模板来使用。

[![HTML webpack plugin basic configuration](img/fa735ed3e5d2bcac5dc2e6f44e520835.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YPUT2dvf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/18192942/html-webpack-plugin-configuration.png)

您还可以添加缩小规则，例如删除注释、缩小 CSS 和 JavaScript。

但是如果你想只使用 minify 属性来构建，你可以这样做。

[![Mnify HTML webpack plugin only for production](img/8cbb59560c1342fa15777e3016e67ff4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EYQl5HjT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/23142733/webpack-html-plugin-prod1-780x1024.png)

资料来源:html 网页包插件

### **网页包定义插件**

这个插件附带了 Webpack 节点模块，在开发 React 应用程序时，它是一个方便的小工具。

DefinePlugin 允许您创建环境变量，并使您的 JavaScript 代码可以访问它。

[![Webpack DefinePlugin basic use](img/e6411a8f056a1419e018fb3a9d6adf9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N0KsuMqp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/23141654/webpack-define-plugin-1024x440.png)

来源: [Webpack 定义插件](https://webpack.js.org/plugins/define-plugin/)

### 迷你 CSS 提取插件

这个插件是另一个伟大的生产工具。

它允许您将 React 应用程序中的 CSS 提取到每个 JavaScript 文件的单独文件中。

与 Extract Text Webpack 插件相比，其他一些令人愉快的特性是:

*   异步加载
*   无重复编译
*   更易于使用

[![Mini CSS extract plugin webpack configuration](img/09f4cd1df950f09925d4a52d745a7394.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A1KUxdrP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/23143456/mini-css-extract-config-1024x393.png)

## Webpack 模块

### **巴别塔装载机**

这个插件允许你编写最新一代的 JavaScript 并将其转换成编译后的 ES5 版本。

[![Babel ES6 feature to transpiled version](img/c07ab1018de7a2920ab783b901b623db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FHrrqyLR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/23161010/babel-example.png)

现在来配置 babel-loader。js，。jsx，以及 Typescript(如果您正在使用它的话)。

[![Webpack babel loader configration](img/2ce70f1e45793fa8535ce1b0ace9df70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YUzsy17K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/23161503/babel-loader-config.png)

来源:[巴别塔加载器](https://github.com/babel/babel-loader)

### **巴别塔预置 react app**

该模块由 Create React App 团队维护，它为您的 React 应用程序提供了一组非常棒的预设。

我们所需要做的就是在 babel loader 配置中添加更多内容。

[![Babel loader configuration with react presets](img/f662924e86c944c016872379330b6656.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FbW-DFD3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://daqxzxzy8xq3u.cloudfront.net/wp-content/uploads/2019/04/23162057/babel-loader-config-with-react-presets-1024x490.png)

来源:[巴别预设 react app](https://www.npmjs.com/package/babel-preset-react-app)
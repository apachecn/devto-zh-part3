# 为什么没人谈论 Razzle？

> 原文：<https://dev.to/josemunoz/why-is-no-one-talking-about-razzle-1c07>

我喜欢在 React 工作，和大多数人一样，我从创建-反应-应用(CRA)开始。这给我提供了一个很大的缓冲，让我可以开始工作，忘记设置环境，我想我们大多数人都会同意，如果我们可以打开一个编辑器，开始在不设置 babel/webpack 的情况下创建 JSX，这会让我们的生活更简单。我最喜欢 React 的一点是使用 react-router-dom 进行客户端路由。在寻找 SSR 解决方案时，我注意到市场迫使我放弃我的路由方式，这意味着我无法从我的路由器免费获得的所有 API 中受益。这对我来说是一个很大的转变，我不得不考虑我现有的项目，这些项目已经达到了一定的复杂程度，并重新构建一切以适应 Next 的路由方式，或者 Gatsby 的使用静态内容模板的方式。

我不喜欢那些强迫你思考而不能发挥你创造力的工具，这是 v2 出来后我放弃 Angular 的主要原因之一。所以 SSR 在一段时间内对我来说是一个障碍，我需要 SEO 的好处，但我不打算重新考虑我的整个应用程序来做到这一点。

我需要我的工具更加灵活。所以我开始做我的研究，我发现 [Razzle](https://github.com/jaredpalmer/razzle) 是一个同构的 SSR 解决方案。Razzle 甚至有自己的 CLI，Create-Razzle-App(也是 CRA？)，这意味着我可以像最初的 Create-React-App 一样调用它。Razzle 在我身上发展得非常快，它的无配置模式意味着我可以像 CRA 一样快速地开始原型制作，但我拥有我需要的所有可扩展性，而不必调整我的工作流程。

如果我想改变 ESLint 的配置，我可以通过在项目中添加一个`.eslintrc`文件来实现，babel 的配置也可以用同样的方式来改变，添加一个带有`.babelrc`的插件，Razzle 会选择它并将其添加到默认配置中，如果我需要修改 webpack 的配置，只需添加一个`razzle.config.js`文件，它提供了一个简单、可扩展的默认 webpack 配置。我不需要购买一个新的框架，我不需要重构我的整个应用程序。我可以像使用 CRA 一样继续使用 React-router-dom，所以基本上不存在学习曲线。事实上，为了给我的应用程序添加 SEO，我唯一要做的就是复制粘贴盖茨比的 SEO 组件，就是这样。

[![But wait! There's more.](img/47dd241fa0d66833609c8e0e3be27016.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XKpzz2zW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgflip.com/2xcfb5.jpg)

由于 Razzle 的工作方式，您可以将它与 Vue、Angular 或 React 一起使用。默认情况下，Razzle 带有 React 的配置，但你可以很容易地改变配置以使用任何其他前端框架。

那么 Razzle 是如何工作的呢？

Razzle 使用并行热模块替换运行的两个不同的 webpack 实例捆绑您的客户端和服务器代码。当您的服务器被捆绑并运行在您在 src/index.js 中指定的任何端口(默认为 3000)上时，客户端捆绑包(即 src/client.js 上的入口点)通过 webpack-dev-server 在不同的端口(默认为 3001)上提供服务，其 publicPath 被显式设置为 localhost:3001(而不是/像许多其他设置那样)。

Razzle 是由 Formik、Backpack 和 After.js 的幕后策划者贾里德·帕尔默创建的，并得到了其他杰出的 OSS 贡献者的支持，如 Dan 'the man' Abramov、Jari Zwarts 和许多其他人。试试 Razzle 吧，我肯定你会爱上它的！
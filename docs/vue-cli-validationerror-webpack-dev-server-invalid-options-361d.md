# CLI 验证错误视图:webpack Dev Server Invalid Options

> 原文：<https://dev.to/therealdanvega/vue-cli-validationerror-webpack-dev-server-invalid-options-361d>

今天早上，我在使用 Vue CLI 创建新的 Vue 项目时遇到了一个问题。奇怪的是，我昨天刚刚创建了一个新项目，它运行得很好。我做了一些调查，这似乎影响了很多用户，所以我想我应该快速发布一个帖子。

## 创建&运行新的 VueJS 项目

我用 [Vue CLI](https://cli.vuejs.org/) 创建了一个新项目，开始了我的早晨。在我创建了项目之后，我切换到那个目录，并输入命令`npm run serve`来调用`vue-cli-service serve`。尝试启动几秒钟后，我在终端中收到以下错误。

```
 INFO  Starting development server...
 ERROR  ValidationError: webpack Dev Server Invalid Options

options.clientLogLevel should be {String} and equal to one of the allowed values

 [ 'info', 'warn', 'error', 'debug', 'trace', 'silent' ]

 (https://webpack.js.org/configuration/dev-server/#devserverclientloglevel)

ValidationError: webpack Dev Server Invalid Options

options.clientLogLevel should be {String} and equal to one of the allowed values

 [ 'info', 'warn', 'error', 'debug', 'trace', 'silent' ]

 (https://webpack.js.org/configuration/dev-server/#devserverclientloglevel)

    at validateOptions (/Users/vega/dev/vue/hello-vue-cli/node_modules/schema-utils/src/validateOptions.js:32:11)
    at new Server (/Users/vega/dev/vue/hello-vue-cli/node_modules/webpack-dev-server/lib/Server.js:71:5)
    at serve (/Users/vega/dev/vue/hello-vue-cli/node_modules/@vue/cli-service/lib/commands/serve.js:138:20)
    at process._tickCallback (internal/process/next_tick.js:68:7)
    at Function.Module.runMain (internal/modules/cjs/loader.js:745:11)
    at startup (internal/bootstrap/node.js:283:19)
    at bootstrapNodeJSCore (internal/bootstrap/node.js:743:3)
npm ERR! code ELIFECYCLE
npm ERR! errno 1
npm ERR! hello-vue-cli@0.1.0 serve: `vue-cli-service serve`
npm ERR! Exit status 1
npm ERR!
npm ERR! Failed at the hello-vue-cli@0.1.0 serve script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in:
npm ERR!     /Users/vega/.npm/_logs/2019-05-17T15_05_54_552Z-debug.log 
```

我通过运行命令`vue -V`检查了 Vue CLI 的版本，我正在[运行最新的 3.7.0](https://github.com/vuejs/vue-cli/releases) 。我想可能是我做错了什么，所以通过运行`rm -Rf hello-vue-cli`删除了这个项目，然后再次尝试，却遇到了同样的错误。

接下来我想知道这是影响了我所有的项目还是仅仅影响了新的项目。我进入一个现有的项目并运行`npm run serve`,它开始得很好。这让我更加挠头，因为我今天早上没有更新 NodeJS，NPM 或 Vue，所以到底发生了什么事。

## Github 上的 Vue CLI 问题

在这一点上，我很困惑，所以我的下一步是前往 [Github](https://github.com/vuejs/vue-cli/issues) 看看是否有其他人看到类似的问题。果不其然，前两个帖子看起来和我所经历的非常相似。

大声对 [dland512](https://github.com/vuejs/vue-cli/issues/4017#issuecomment-493481614) 说清楚发生了什么。问题似乎出在生成的 webpack 配置`node_modules/@vue/cli-service/lib/commands/serve.js`上，它具有以下内容:

```
const server = new WebpackDevServer(compiler, Object.assign({
      clientLogLevel: 'none', 
```

如果你回头看看最初的错误，它说:

```
ValidationError: webpack Dev Server Invalid Options

options.clientLogLevel should be {String} and equal to one of the allowed values

 [ 'info', 'warn', 'error', 'debug', 'trace', 'silent' ] 
```

仍然不确定是什么导致了这一点，但它可能是`vue-cli-service`本身。解决方法是在项目`vue.config.js`的根目录下创建一个新文件，包含以下内容:

```
module.exports = {
    devServer: {
        clientLogLevel: 'info'
    }
}; 
```

添加这个配置后，我能够运行项目，没有任何错误。如果你想了解更多关于 Vue CLI 全局配置的信息，你可以点击查看[文档。](https://cli.vuejs.org/config/#global-cli-config)

## 结论

我猜当这篇文章被编入索引时，它将不再是一个问题，但我认为写出来是很重要的。这是那些 15 年前会让我完全脱轨一天甚至一周的错误之一。凭借经验和极大的耐心，我学会了如何处理这种最终会出现的问题。我希望这篇文章要么为你解决了这个问题，要么只是让你对我解决一个令人沮丧的问题的思维过程有所了解。一如既往...

快乐编码

丹

*这篇文章最初是在 https://www.danvega.dev/blog 的[发表在我的博客上的。如果你觉得这篇文章有趣，请考虑](https://www.danvega.dev/blog)[订阅我的时事通讯](https://www.danvega.dev/signup/)或者在 [Twitter](http://twitter.com/therealdanvega) 上关注我。*
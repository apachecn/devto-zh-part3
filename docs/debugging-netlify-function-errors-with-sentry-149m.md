# 用 Sentry 调试网络功能错误

> 原文：<https://dev.to/pimterry/debugging-netlify-function-errors-with-sentry-149m>

Netlify functions 是一个快速、简单&的强大工具，但是它们可能比传统的服务器应用程序更难调试& monitor。这是一个很难在本地精确重建的环境，没有你能在紧要关头 SSH 进入的机器，也没有内置的错误通知。

您的代码最终会出错，您需要工具来修复它。

[HTTP Toolkit](https://httptoolkit.tech) 使用 Netlify 函数在幕后管理用户帐户信息并调用结帐回调。如果我们在这里遇到错误，人们的支付将会失败，或者他们将无法访问付费功能，所以这可能非常糟糕！我需要能够立即捕捉错误，调试并找出它们发生的原因，并确认我的修复工作。

调试和修复问题在这里是一个大话题，但是第一步是准确地知道错误发生的时间和方式。有一些工具可以做到这一点，但就我个人而言，最近我和 [Sentry](https://sentry.io) 在项目上取得了很多成功。他们有一个慷慨的免费计划(每月 5000 个错误)，几乎所有东西的内置集成，还有一些好的&详细错误报告工具。

如果您可以设置 Sentry，那么每当您的函数中出现错误时，您都会收到电子邮件，并且您可以详细研究错误本身，以找出失败的确切原因。完美，但是网络功能的设置有一些额外的步骤。

## 开始向哨兵报告错误

我将在这里使用 JS，我假设你已经设置了一个有效的 Netlify 函数。要从那里添加哨兵报告，您需要:

*   创建哨兵账户
*   在该帐户中为您的功能创建一个 Sentry 项目
*   获取 Sentry 项目的 DSN，并在 Netlify 构建中将它设置为 SENTRY_DSN 变量
*   `npm install --save @sentry/node`
*   初始化您的错误记录逻辑:

```
const Sentry = require('@sentry/node');

const { SENTRY_DSN } = process.env;

let sentryInitialized = false;
export function initSentry() {
    if (SENTRY_DSN) {
        Sentry.init({ dsn: SENTRY_DSN });
        sentryInitialized = true;
    }
}

// Import & call this from your function handlers:
initSentry(); 
```

有了这个功能，现在可以自动报告未发现的错误和剔除！

然而不幸的是，处理程序中的错误/拒绝被捕获并吞下，所以我们也需要捕获它们。此外，AWS Lambda(Netlify 函数背后的服务)的行为并不完全符合您的预期，因此当您的函数在执行后关闭时，一些错误报告将会延迟或丢失。

## 捕捉处理程序错误

让我们首先检测处理函数错误。首先，创建一个方便的`reportError`方法，您可以调用并等待确认错误被报告(否则您的函数可能会在报告完成之前关闭):

```
// Don't use this example quite yet! It's not complete - see below.
function reportError(error) {
    console.warn(error);
    if (!sentryInitialized) return;

    if (typeof error === 'string') {
        Sentry.captureMessage(error);
    } else {
        Sentry.captureException(error);
    }
} 
```

然后在每个函数处理程序周围添加一个包装器。包装器需要像平常一样调用处理程序，但是捕捉任何错误或承诺拒绝，并向 Sentry 报告它们。然后它需要再次抛出错误，这样仍然会返回一个 HTTP 错误:

```
// Don't use this example quite yet! It's not complete - see below.
function catchErrors(handler) {
    return async function() {
        try {
            return await handler.call(this, ...arguments);
        } catch(e) {
            // This catches both sync errors & promise
            // rejections, because we 'await' on the handler
            reportError(e);
            throw e;
        }
    };
} 
```

## 可靠的报告与哨兵& AWS Lambda

一个 Lambda 函数运行到完成，然后将被冻结。以后的调用可能会再次启动它，或者它可能会被处理掉，整个过程重新创建。这意味着当您的函数响应时，任何尚未发送的 Sentry 请求都可能会丢失。幸运的是，我们可以解决这个问题。我们需要做两件事:等待报告的错误被完全发送，并确保 Sentry 不会干扰正常的 Lambda 关闭。

当我们报告错误时，最新的 Sentry SDK 并不完全支持任何类型的回调(尽管[他们正在研究一个 flush()方法](//github.com/getsentry/sentry-javascript/issues/1449)，所以我们需要稍微深入内部来更直接地报告错误，以一种我们*可以*等待它完成的形式。

将您的报告错误功能更改如下:

```
async function reportError(error) {
    console.warn(error);
    if (!sentryInitialized) return;

    const scope = Sentry.getCurrentHub().getScope();
    const sentryClient = Sentry.getCurrentHub().getClient();

    if (typeof error === 'string') {
        await sentryClient.captureMessage(error, scope);
    } else {
        await sentryClient.captureException(error, scope);
    }
} 
```

最后，为了阻止 Sentry 回调干扰正常的 Lambda 生命周期，我们需要将`context.callbackWaitsForEmptyEventLoop`设置为 false。

我们可以在我们的处理程序包装器中这样做，我们也需要更新包装器来等待`reportError`调用，以确保它完成。

将您的`catchErrors`包装器更改为:

```
function catchErrors(handler) {
    return async function(event, context) {
        context.callbackWaitsForEmptyEventLoop = false;
        try {
            return await handler.call(this, ...arguments);
        } catch(e) {
            // This catches both sync errors & promise
            // rejections, because we 'await' on the handler
            await reportError(e);
            throw e;
        }
    };
} 
```

全部完成！有了这个，所有的处理程序错误都会被可靠地报告给 Sentry，您可以放心地知道您的函数工作得很好(或者至少，您确切地知道它们失败了多少)。

## 奖金扩展

我想提到两个可选的额外步骤，以帮助您更容易地调试您的问题。

第一，额外报道超级有用。例如， [`Sentry.addBreadcrumb`](https://docs.sentry.io/learn/breadcrumbs/) 让您记录额外的事件，这些事件将包含在任何以后的异常中。您还可以从代码中的任何地方调用我们的`reportError`函数来立即报告错误，即使您实际上并没有抛出错误并失败(但是一定要记住等待返回的承诺)。

其次，将函数的 git commit 作为您的 Sentry release，这样您就总能知道哪个版本的代码抛出了哪些错误。Netlify 将此作为一个`COMMIT_REF`环境变量提供，但遗憾的是这在运行时 Lambda 环境中是不可用的，所以我们需要确保在构建时将它烘焙进来。为此，首先扩展默认的 webpack 配置:

```
// webpack.js:
const webpack = require('webpack');
const { COMMIT_REF } = process.env;

module.exports = {
    plugins: [
        new webpack.DefinePlugin({
            "process.env.COMMIT_REF": JSON.stringify(COMMIT_REF)
        })
    ]
}; 
```

您需要修改您的构建脚本，将`-c ./webpack.js`传递到您的`netlify-lambda build`命令来使用它。

然后，更改初始的 Sentry 设置，将这个变量传递给 Sentry:

```
const Sentry = require('@sentry/node');

const { SENTRY_DSN, COMMIT_REF } = process.env;

let sentryInitialized = false;
export function initSentry() {
    if (SENTRY_DSN) {
        Sentry.init({ dsn: SENTRY_DSN, release: COMMIT_REF });
        sentryInitialized = true;
    }
}

// Import & call this from your function handlers:
initSentry(); 
```

瞧，网络功能的自动错误报告:

[![A screenshot of Sentry, showing a Netlify function error](img/7476b429b4e99fb845a64c8f72198d67.png)](https://httptoolkit.tech/static/sentry-function-error-261576e4a447a4c29e7f39e3871a5ab2-d7d85.png)

想看一个完整的例子吗？看看 [HTTP Toolkit 的记账内部](https://github.com/httptoolkit/accounts/tree/master/src)。

*最初发布于[http toolkit . tech](https://httptoolkit.tech/blog/netlify-function-error-reporting-with-sentry/)T3】*
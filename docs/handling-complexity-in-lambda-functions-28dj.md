# 处理 lambda 函数中的复杂性

> 原文：<https://dev.to/juliantellez/handling-complexity-in-lambda-functions-28dj>

此处见原发布版本[。](https://medium.com/dazn-tech/handling-complexity-in-lambda-functions-e7acfbeb920a)

### TLDR；

> 中间件可以处理 lambdas 的复杂性，同时将业务逻辑和横切关注点隔离在可以通过事件周期建模的可重用组件中。

最近我有幸和 lambda 函数一起工作，我很开心！拥有一个事件驱动的执行环境的想法既大胆又令人兴奋。

如果你是第一次接触 FaaS 世界，不要担心，社区已经为你准备了一份精选的阅读清单[这里](https://github.com/anaibol/awesome-serverless)和[这里](https://github.com/pmuens/awesome-serverless)。随便看看，喝点咖啡。☕

如你所见，(因为你至少检查了其中一个链接，不是吗？)一个巨大的重量被举起了。我们现在可以开发、运行和管理应用程序，而无需构建和维护复杂的基础架构。但是随着新的开始，新的挑战出现了。其中一个问题和这个博客的原因是函数的复杂性。

## 多复杂才算复杂？

一般来说，在编写 lambda 函数时，你会按照[单一责任原则](https://en.wikipedia.org/wiki/Single_responsibility_principle)将它们组织成一系列专用模块。如果你宁愿不这样做，请阅读这篇比较[单片与单一用途功能](https://hackernoon.com/aws-lambda-should-you-have-few-monolithic-functions-or-many-single-purposed-functions-8c3872d4338f)的优秀博客。将应用程序与业务逻辑隔离开来，可以降低代码的复杂性，简化推理，并帮助您更有效地调试代码。你的同事和未来的自己都会感谢你。

以某种 CRUD API 为例，我们希望在处理任何数据之前，确保有效载荷的格式正确。有效负载验证可能会发出一个错误，将其状态传达给使用者。如果验证成功，它将继续执行一些业务逻辑。对于这两种场景，我们还希望设置一些百分比跟踪和错误日志记录。虽然所有这些听起来都很棒，但我们实际上已经用各种[横切关注点](https://en.wikipedia.org/wiki/Cross-cutting_concern)绘制了一个请求生命周期:

*   数据验证。
*   错误检测和纠正。
*   伐木。
*   监控。

[![](img/0b0805023b086db7f6056e20cffb8d18.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A6LDVOJh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2AXT2_l9PLKtuZxKNGTYa4Uw.gif) 
*我知道你在想什么… (gif from giphy)*

遵循无服务器原则，我们应该将任何与核心业务无关的事情外包出去，所以如果我们能够专注于业务逻辑并将这些特定于应用程序的问题也外包出去，那不是很好吗？当然，这并不是一个新概念，我们已经在 web 框架中使用它很长时间了，如 [hapijs](http://hapijs.com) 和 [express](https://expressjs.com) 。

## λ中间件

lambda [中间件](https://en.wikipedia.org/wiki/Middleware)本质上是一个以一致的方式帮助管理横切关注点的功能。它提供了应用程序和业务逻辑的清晰分离，并概述了一个易于推理的配置周期。它是一种粘合剂，当团队中的每个人都采用它时，它就会成为支持和创建特性的通用语言。但是我们真的需要中间件吗？好吧，看看你的兰姆达斯的现状，问自己几个问题:

*   代码是什么样子的？
*   你能想象至少在两年内有人会支持它吗？
*   需要多少业务逻辑专业知识？(如果是专业化的，那么分离是必要的)
*   逻辑是否纠缠在一起，难以推理？
*   这个项目是否有一个清晰的路径，或者你认为它会朝着不同的方向发展？
*   专家离开的那一天，项目会是什么样子？
*   最佳实践是否得到实施和维护？

[![](img/37019a894738d17e3675428b77329333.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p_Sk-iR4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn.hashnode.com/res/hashnode/image/upload/v1549761422481/5LOA6J7hr.gif) 
*杰克·尼科尔森认可。(gif 来自 giphy)*

## 进入 Lambcycle

[Lambcycle](https://github.com/juliantellez/lambcycle) 🐑🛵是 lambda 函数的声明性中间件。它定义了一个可配置的事件周期，并允许您关注应用程序的逻辑。它有一个“功能插件”的方法，所以你可以很容易地创建你自己的插件或重用你最喜欢的软件包。

在实践中，不存在非意见化的框架，每一个想法都是一种意见，是一种处理应用程序的方式。Lambcycle 更喜欢配置而不是代码，并且认为业务逻辑应该尽可能地隔离。

[![](img/b143fc04f6d2c7d6bf2a1b61e5350bfe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FyEK_q4D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1600/1%2A5I_ZBcvd03ktQpGXuA2kgQ.png) 
*Labmcycle 流程图。*

Lambcycle 用几个扩展点增强了 lambda 函数(见图)，每个扩展点都可以用来以分解的方式与事件交互。

*   第一个扩展点是`Request`，它在 lambda 被调用后立即出现。您可以使用这个步骤进行解析、验证等...
    注意:如果你正在考虑授权，请考虑一个 [lambda 授权者](https://docs.aws.amazon.com/apigateway/latest/developerguide/apigateway-use-lambda-authorizer.html)。

*   当您需要调整数据以适应接口时,`Pre Handler`扩展就派上了用场。这也是一个获取秘密的好地方。

*   你美丽的商业逻辑所在的地方。

*   接下来是`Post Handler`，使用这个扩展来验证和/或缓存业务逻辑的输出。

*   `Error`是日志和跟踪的隐式扩展。

*   最后是`Pre Response`，您有机会格式化对消费者的响应(可以是数据或错误)。

## 使用中间件

```
import Joi from 'joi'
import lambcycle from 'lambcycle'
import joiPlugin from 'lambcycle/dist/plugin-joi'
import bodyParser from 'lambcycle/dist/plugin-body-parser'
import realTimeEventTracker from './bugFreeBizLogic'

const businessLogic = async(event, context) => {
    const {error, data} = await realTimeEventTracker(event.data)

    if(error) {
        throw error
    }

    return data;
};

const schema = Joi.object().keys({
    assetId: Joi.string().required(),
    competition: Joi.string().required(),
    sport: Joi.string(),
    tournamentCalendar: Joi.date(),
}).required()

const handler = lambcycle(businessLogic)
.register([
    bodyParser({type: 'json'}),
    joiPlugin(schema)
])

export default handler 
```

上面的例子利用了 [express 的主体解析器](https://github.com/expressjs/body-parser)和 [hapi 的 joi 验证](https://github.com/hapijs/joi)。我们已经分别为这两个插件配置了解析类型和验证模式。通过抽象这两者，我们摆脱了复杂性，并将焦点转移到业务逻辑上。请注意，更健壮的 lambda 将包括错误跟踪和响应插件。

## 制作插件

说到插件，可能性是无穷无尽的！你有什么想法吗？[投稿](https://github.com/juliantellez/lambcycle/blob/develop/contributing.md)更欢迎❤️

```
import * as Sentry from '@sentry/node';
import MyAwesomeIntegration from './MyAwesomeIntegration'

const sentryPlugin = (config) => {
    Sentry.init({
        dsn: `https://${config.key}@sentry.io/${config.project}`,
        integrations: [new MyAwesomeIntegration()]
    });

    return {
        config,
        plugin: {
            onPreResponse: async (handlerWrapper, config) => {
                Sentry.captureMessage('some percentile log perhaps?')
            },
            onError: async (handlerWrapper, config) => {
                Sentry.captureException(handlerWrapper.error);
            }
        }
    }
}

export default sentryPlugin; 
```

在你的λ里....

```
import lambcycle from 'lambcycle'
import sentryPlugin from './sentryPlugin'

const myApplicationLogic = async (event, context) => {
    return await someLogic()
}

const handler = lambcycle(myApplicationLogic)
.register([
    sentryPlugin({
        key: process.env.SENTRY_KEY,
        project: process.env.SENTRY_PROJECT,
    })
]);

export default handler; 
```

# DX

Lambcycle 在构建时考虑到了[开发者体验“DX”](https://hackernoon.com/the-best-practices-for-a-great-developer-experience-dx-9036834382b0)，并附带了[类型](https://www.typescriptlang.org)定义，以实现一致性和自动完成🚀(仅限 VScode)。

[![](img/a661a64fc2ba4ebc49c01090f0631d9e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XfoDjVgF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://user-images.githubusercontent.com/4896851/51274743-db4db500-19c7-11e9-903c-cb50d127d933.gif) 
*Lambcycle DX。*

# 结论

这是一个勇敢的新世界，无服务器将永远存在！促进可重用组件和一致的错误处理将帮助您和您的团队以一种更可控和更有组织的方式创建和支持特性。Lambcycle 的主要目标是围绕可预测的单向循环转移话题，并鼓励跨特性的组件可重用性。值得一提的是，对于更复杂的场景，你应该看看[阶跃函数](https://docs.aws.amazon.com/step-functions/latest/dg/welcome.html)。如果你想在评论中了解更多，请告诉我，同时查看这些[用例](https://aws.amazon.com/step-functions/use-cases/)！

特别感谢传奇[燃僧](https://theburningmonk.com/)对本文的深度点评。
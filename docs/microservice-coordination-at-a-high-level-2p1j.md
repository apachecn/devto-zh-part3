# 高层次的微服务协调

> 原文：<https://dev.to/jacobsee/microservice-coordination-at-a-high-level-2p1j>

[![man writing on whiteboard](img/8b1681a24db8a688fa97f31508683f1c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aK6zmjqx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1533749871411-5e21e14bcc7d%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ) 

<figcaption>照片由[活动创作者](https://unsplash.com/@campaign_creators?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit)</figcaption>

拍摄

## 为什么

我最近有幸在一个团队中工作，该团队的任务是使用可组合架构创建一个系统。我们需要一个像管道一样运行的系统——信息从一端流入，经过多次转换，最后在离开管道的另一端时返回给客户机。并非所有数据都需要应用相同的转换，或者以相同的顺序，因此我们需要一种方法，以尽可能最干净、最具可伸缩性的方式动态调整该数据管道的行为。

我们首先决定每个转换都应该是我们集群中自己的微服务。当需要将微服务串在一起形成一个完整的管道时，我们进行了小组讨论，考虑我们可以使用的可能策略——即*编排* v.s. *编排*。这篇文章将讨论中的一些观点带到了互联网上。

## 编排

[![man facing music stand with music book guide inside building](img/a5a72a09cff02ea465d08f68f269a3fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2bsATHxc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1519683384663-c9b34271669a%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ) 

<figcaption>照片由[【手册】no Geli](https://unsplash.com/@gwundrig?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit)/[【un flash】](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit)</figcaption>

拍摄

当你去音乐厅听当地交响乐团演奏时，可以说在演出中最重要的角色是指挥。管弦乐队的每个成员都以同步的方式行动，因为他们都从相同的参考点接收指令。在你听的时候，指挥不仅同步所有音乐家的声音，而且在你到达之前，指挥就在排练*中塑造了音乐本身。这里调整时间，那里调整音量，你所听到的是由指挥调整的，带给你*他们诠释的*尽可能好的表现。*

微服务环境下的编排非常相似！就像小提琴手不会指望木管乐器来学习他们应该做什么一样，一个服务不需要了解其他服务就可以在应用程序的大计划中发挥作用。所有微服务都关注一个系统( *orchestrator* -通常是同一集群上的另一个服务),该系统负责确定需要发生什么，并指导其控制下的微服务执行任何必要的任务，以达到其期望的结果。指挥者是在其控制范围内发生的所有事情的集中权威。其他服务可以简单地认为是它的工人。

[![](img/7f1f25aed3cf192edb0d6f551a03e7ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UUlt0Sfp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jacobsee.com/conteimg/2018/12/Screenshot-from-2018-12-04-17-15-27.png) 

<figcaption>没有服务不使用 orchestrator 进行通信。对周围发生的事情浑然不觉，他们执行任务并返回结果。</figcaption>

这种策略给服务可组合性表带来了一些好处和一些缺陷。本着 [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) 的精神 orchestrator 是一个放置工作流逻辑的干净地方。由于 orchestrator 对系统中应该发生的事情有着高度的了解，因此它可以提供开箱即用的高级进度监控和错误处理。不过，对于编排模式来说，这并不全是好消息...编排固有地建立了具有单点故障的结构。如果指挥者因为任何原因不能委派任务，就好像交响乐团的指挥在演奏中突然死亡。系统内的所有进程都会停止，除非故障类型可以在您的环境中自动检测和修复，否则您的待命工程师将不会有一个好的夜晚。

为了在实践中进一步阅读配器，或者亲自尝试，你可能想要检查一下[优步的节奏](https://github.com/uber/cadence)或者[网飞的指挥](https://netflix.github.io/conductor/)配器引擎。我更熟悉的系统 Conductor 使用基于 JSON 的 DSL 来定义工作流，如下所示:

```
{
  "name": "encode_and_deploy",
  "description": "Encodes a file and deploys to CDN",
  "version": 1,
  "tasks": [
    {
      "name": "encode",
      "taskReferenceName": "encode",
      "type": "SIMPLE",
      "inputParameters": {
        "fileLocation": "${workflow.input.fileLocation}"
      }
    },
    {
      "name": "deploy",
      "taskReferenceName": "d1",
      "type": "SIMPLE",
      "inputParameters": {
        "fileLocation": "${encode.output.encodeLocation}"
      }

    }
  ],
  "outputParameters": {
    "cdn_url": "${d1.output.location}"
  },
  "schemaVersion": 2
} 
```

## 编排

[![group of ballerinas dancing while raising both hands](img/a130f8be5395303885529d52b3415590.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TYnwECYz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1508807526345-15e9b5f4eaff%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ) 

<figcaption>摄影[迈克尔·阿丰索](https://unsplash.com/@mafonso?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit)</figcaption>

继续表演艺术的类比，因为这些策略的名称与这些例子非常吻合，想象一下你正在观看一场由技艺高超的舞者表演的芭蕾舞。没有指挥带领他们-舞蹈是一种视觉艺术，会挡住观众的视线！舞者们是同步的，流畅地绕着彼此移动，因为他们经过了严格的训练才能做到这一点。他们的表演中没有指挥，取而代之的是，他们每个人都知道应该发生什么，什么时候发生，并保持对周围环境和其他舞者的意识，以确保表演按计划进行。

这也是微服务协调中的有效策略。编排是一种没有指挥的设计模式。每个服务不仅必须知道如何完成自己的工作，还必须知道何时以及如何与同一系统中的其他服务进行交互。所有组件都是自治的，通常同意遵守合同，但在其他方面是完全自我管理的，不接受外部来源的指示。

[![](img/9cf3b8584b0034f93d805520072f5cd1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yltj8jOW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.jacobsee.com/conteimg/2018/12/Screenshot-from-2018-12-04-17-15-56.png) 

<figcaption>没有指挥者在场。服务不接收执行任务的指令，相反，它们*理解*给出的结果，并知道下一步该怎么做。</figcaption>

与编排相比，编排(在我看来)对于一个新项目来说更快上手。它不需要学习使用其他人的编排引擎的前期时间投入，并且让您直接进入您最熟悉的领域——代码。如果设计正确，它甚至可以比编排系统更快，因为您不会产生在每个逻辑步骤之间等待编排器的开销。但是要小心，orchestrator 提供的一些好处在这里没有对等物。由于服务直接相互交互，工作流中的错误处理完全由您负责。没有中介来干预和防止级联故障。你的开发团队内部的沟通变得*尤其*关键，因为工作流的整体完整性依赖于每个服务遵守它的契约，而*本身*依赖于开发人员之间对单个服务和整个系统本身应该如何操作的共同理解。

## 结论

编排和编排都是在复杂系统中协调工作的广为接受的方法，并且完全由您决定在解决任何给定的问题时更喜欢哪一种。同样值得一提的是，你不必只挑一个！您的系统可以进一步分解成子组件，每一个子组件都更适合单独进行编排或编排。至于我们——我们在有限的时间线上操作，并出于简单的原因为我们的系统选择了编排，使用*路由单*模式完成工作流的可组合性。我正在计划另一篇关于该模式的文章，敬请关注！
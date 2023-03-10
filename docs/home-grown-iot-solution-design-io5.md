# 本土物联网解决方案设计

> 原文：<https://dev.to/azure/home-grown-iot-solution-design-io5>

现在我对我想做的物联网项目有了一个想法是时候考虑如何着手建设它了。正如我在序言中所说的，我没有任何构建合适的物联网项目的经验，所以这在很大程度上是一件试错的事情。事实上，在整个项目的开发过程中，我实际上做了大约 3 种不同的设计，今天我想谈谈这些不同的方法以及为什么某些东西被废弃了。

## 设计基础

从一开始，我就知道解决方案的基本设计应该是什么样子。

[![Basic solution design](img/bf492b37aea95174fc7c4f30100515bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--00Vx0_o1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.aaron-powell.cimg/home-grown-iot/02-001.png)

这个想法是有一个树莓 Pi(图片由我的同事达米安·布雷迪提供)与逆变器对话，然后当它获得数据时，它会将其推送到一个 [Azure 函数](https://azure.microsoft.com/en-us/services/functions/?WT.mc_id=devto-blog-aapowell)，然后将数据放入 [Azure 表存储](https://azure.microsoft.com/en-us/services/storage/tables/?WT.mc_id=devto-blog-aapowell)。

你可能想知道为什么我选择 Azure Functions 而不是一个[应用服务](https://azure.microsoft.com/en-us/services/app-service/?WT.mc_id=devto-blog-aapowell)。最初，这是一个纯粹由成本决定的决定，我希望以尽可能低的成本来运行这个解决方案，Azure Fuctions 给了我这一点。现在，我可以使用一个免费的应用服务，但是随着设计的发展，我最终需要更多的功能，而不仅仅是 HTTP 端点。

这同样适用于在 Azure 中使用表存储而不是其他存储的决定。我的数据结构合理，但不是真正的关系型，所以我不需要完整的 SQL server。我喜欢 Cosmos DB T1 的想法，因为它本质上是一个 NoSQL 数据库，但它的设计规模远远超出了我的小项目运行的规模(最小吞吐量是每秒 4000 个 T2 请求单位，我每 20 秒处理一个请求)🤣).如果我要部署这个来监控多个住宅，我会选择 Cosmos，但是对于我的一个房子，每月 1 美元的 Azure 存储就足够了。

最后，我需要决定用什么语言来构建。假设 Raspberry Pi 是我的部署目标，在它上面运行 Linux 是有意义的，所以我需要一种可以在 Linux 上运行的语言。我还希望在整个堆栈中有一种单一的语言，部分是因为我可以共享代码(如果需要的话)，但更多的是因为我不需要在语言之间来回切换。Azure Functions 是我最无法控制的东西，所以这是我决定的原因，但它并没有真正缩小范围，因为它支持很多语言！最后，我决定选择 [F#](https://fsharp.org) ，原因无非是我喜欢这门语言，而且我已经有一段时间没有用它做任何*过于复杂的事情了。哦还有。我可以很容易地在树莓派上运行它。😉*

### 考虑安全

当谈到设计安全时，我会考虑一些事情，因为在一天结束时，你最不想做的事情就是在僵尸网络中添加另一个设备。因此，我想确保我的 Raspberry Pi 不需要互联网寻址，只需要它能够在我的网络之外与 Azure 资源通信。谈到物联网项目时，这是您应该经常考虑的事情，您正在采取什么措施来确保它们不会受到损害？如果他们不需要，你会不让他们上网吗？您是否使用 VPN 与您的总部(云或本地)通信？遵循最小特权的方法，尽可能保持事物断开连接，并使用基于推送的模型，或者让设备建立出站套接字，而不是基于家庭搜索设备。

## 设计#1

我的第一个解决方案很简单，非常简单。我的想法是创建一个. NET 核心控制台应用程序，我将它同步到 Raspberry Pi，它将与一个 [HTTP 端点 Azure 函数](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-http-webhook?WT.mc_id=devto-blog-aapowell)对话。巧合的是，这也是让我[写这篇文章](https://dev.to/azure/azure-functions-with-f-2l3c)的原因，因为我刚刚开始设置我的功能项目！

这个想法非常简单，控制台应用程序将对我在转换器中识别的端点进行 HTTP GET，然后将响应发送到 Azure 函数，该函数会将响应写入表存储。

## 设计#1.5

当我开始开发的时候，我发现我的本地工作流程有点摩擦。我将写一个关于本地开发的独立博客，但是最终我想要一个相对于生产更可预测的本地环境，所以我决定引入 [Docker](https://www.docker.com/) 。这对于[控制台应用](https://docs.microsoft.com/en-us/aspnet/core/host-and-deploy/docker/building-net-docker-images?view=aspnetcore-2.2&WT.mc_id=devto-blog-aapowell)和[功能](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-function-linux-custom-image?WT.mc_id=devto-blog-aapowell)来说都很容易，因为微软为它们都提供了 Docker 镜像。这也意味着我不必在 Azure 上托管我的函数，我可以把它们放在 Raspberry Pi 上，以减少 HTTP 调用之间的延迟。我最终没有这样做，因为我不想给树莓派增加太多负担。

## 设计#2

一切进展顺利，我开始与物联网专家戴夫·格洛弗(Dave Glover)聊天。戴夫问我是否考虑过使用[物联网中心](https://azure.microsoft.com/en-us/services/iot-hub/?WT.mc_id=devto-blog-aapowell)作为我解决方案的一部分，我没有(我只是模糊地听说过它，不知道它除了“物联网”还有什么用🤣).这导致了我的建筑的彻底革新。

### 添加物联网集线器

到目前为止，我一直通过 HTTP 从控制台应用程序直接与 Azure 函数对话。将物联网中心引入组合中彻底改变了这一点，而不是与功能对话，我与物联网中心对话，它有一个我可以使用的事件流。IoT Hub 还允许你从 Azure 向设备发送消息，如果你需要动态更新配置，这将非常有用。在[物联网中心和事件中心](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-compare-event-hubs?WT.mc_id=devto-blog-aapowell)之间有许多重叠，因为两者都是你消费消息的流(在物联网中心，你实际上订阅了事件中心来获取消息)。

当然，Azure Functions [有物联网中心绑定](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-event-iot?WT.mc_id=devto-blog-aapowell)，这意味着我们可以轻松地消费这些消息。现在我们的设计看起来像这样:

[![Solution design with IoT Hub](img/9eb084a04e7ed00609e9b270a0f3b8ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wJdx9gS6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.aaron-powell.cimg/home-grown-iot/02-002.png)

现在，我们不再直接与函数对话，而是推入物联网中心消息流，每当有新消息进来，函数就会运行。这将允许解决方案以比以前更快的速度扩展，这也意味着，如果由于某种原因，功能应用程序离线(例如，当它被部署时)，我不会丢弃消息，它们会一直留在流中，直到被使用。这也是我决定使用函数而不是应用服务的回报，因为我不再需要函数中的任何 HTTP 端点，它们都将使用物联网集线器绑定。

### 处理多种消息类型

当我在我的函数应用程序中的不同 HTTP 端点发布时，很容易处理我从转换器返回的不同数据结构(我在那里监视 3 个端点)。但移动到物联网中心改变了这一点，我不再直接与功能对话，我只将消息泵入消息流，所以我们如何处理不同的结构？

是时候看看物联网 Hub 中的[消息路由](https://docs.microsoft.com/en-us/azure/iot-hub/tutorial-routing?WT.mc_id=devto-blog-aapowell)了。路由做的和它听起来做的一样，为您提供了根据您提供的规则将消息发送到不同地方的工具。通过路由，您可以将来自物联网中心流的消息重定向到二级[事件中心](https://docs.microsoft.com/en-us/azure/event-hubs/?WT.mc_id=devto-blog-aapowell)、[服务总线队列、服务总线主题](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-queues-topics-subscriptions?WT.mc_id=devto-blog-aapowell)或 [Blob 存储](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blobs-introduction?WT.mc_id=devto-blog-aapowell)。

创建路由的方法是针对消息中重要的内容定义一个[消息查询](https://docs.microsoft.com/en-gb/azure/iot-hub/iot-hub-devguide-routing-query-syntax?WT.mc_id=devto-blog-aapowell)，可以是消息的属性，也可以是消息体本身。例如，如果您正在监控一个温度传感器并收到超过阈值的消息，您可以将该消息发送到高优先级流，而不是主要流。

对我来说，因为每个消息体都完全不同，所以我在消息被发送到物联网集线器之前为其添加了一个特殊属性，以指示消息的*类型*。然后，我将它重定向到几个不同的事件中心之一，这样函数就可以只订阅正确的事件中心，并且只处理一种数据类型。

### 多次处理一条消息

对于新的基于事件中心的功能，我想做的最后一件事是对数据进行一点分割。我监视的一个端点包含大量数据，我想更仔细地查看这些数据，特别是将两个面板组分开，以便我可以独立地报告每个面板组。

这意味着我要么必须有一个大函数来做许多不同的事情，要么我必须多次阅读消息。现在，你不能**实际上**多次读取消息，这是一个 FIFO(先进先出)模型，所以你需要设置一个[消费者群](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-features?WT.mc_id=devto-blog-aapowell#event-consumers) -per-Function，这样每个函数都有自己的消息视图。这也意味着我们不会为 Azure 功能使用物联网中心绑定，而是使用[事件中心绑定](https://docs.microsoft.com/en-us/azure/azure-functions/functions-bindings-event-hubs?WT.mc_id=devto-blog-aapowell)(实际上是一样的)，并为绑定提供适当的消费者群体。

## 完成设计

我现在已经把所有的拼图拼起来了，看起来像这样:

[![Complete Solution Design](img/227f9dd67ac2a4e33cc11732a95ce4a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uSUCW1DI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.aaron-powell.cimg/home-grown-iot/02-003.png)

我的 Raspberry Pi 与逆变器对话，然后用物联网集线器发送消息，上面有一个标签，指示消息的*类型*。然后，物联网中心将根据*类型*将消息路由至 3 个事件中心之一(所有事件中心都在同一个事件中心名称空间内)。这些事件中心有一个消费者组-每个函数，这样我的 Azure 函数就可以把消息分成不同的表和表存储！

我很喜欢这个设计的结果。它可能有很多我最初认为我会拥有的移动部分，但每一个都发挥着重要的作用，从物联网中心允许我消费消息而不用担心处理它们，事件中心允许我将消息定向到不同的消费点，Azure 函数可以大规模处理数据(我不需要，但在物联网中很重要)，以及最终我可以在未来报告的非结构化数据存储的表存储。

你觉得这个设计怎么样？你觉得我错过了什么吗？有什么想不到的吗？
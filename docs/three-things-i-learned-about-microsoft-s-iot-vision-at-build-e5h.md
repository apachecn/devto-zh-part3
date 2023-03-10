# 我从微软的物联网愿景中学到了三件事

> 原文：<https://dev.to/bsatrom/three-things-i-learned-about-microsoft-s-iot-vision-at-build-e5h>

[![](img/700cd1a3ac56ab31f75dedefd20d67bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_8EuBLCk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rgspek9xybmg1rr0uxfy.jpg)

**照片由[莱奥·麦克拉伦(@leiomclaren)在 Unsplash](https://unsplash.com/@leio?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)T3 上拍摄**

本周，我参加了微软的年度构建大会。这是我自多年前第一次迭代以来的第一次重新构建之旅——当时我是微软的员工，展会的明星是 Windows 8！——我很高兴能近距离看到微软对开发者的愿景。

在这三天里，微软业务的所有领域都出现了旋风般的公告，但作为一名制造商和嵌入式开发者，我的主要兴趣是该公司对物联网越来越多的关注。以下是我在构建期间了解到的关于微软物联网愿景的三件主要事情。

*注:这里表达的观点和意见是我个人的，不代表我的雇主[粒子](https://particle.io)的观点和意见。它们也不意味着表明任何关于粒子产品特性、集成、路线图变化或战略转移的公开声明。这篇文章仅仅是一个开发者的观点，应该被如此看待。*

## “智能边缘”是微软对混合云计算模式长期承诺的延伸

塞特亚·纳德拉在创建第一天的主题演讲是一场紧凑的两小时旋风，几乎触及了微软业务的每个领域。纳德拉围绕他所说的微软的“四个不同的平台机会”发表了他的评论，每个机会都推动了主题演讲的一个部分。他们是:

1.  智能云和边缘
2.  业务流程自动化
3.  办公室 365
4.  赌博

这位微软首席执行官在他的主题演讲中只是顺便提到了“物联网”的名称。相反，他在第一部分谈论了边缘计算，以及他认为 Azure 在“将云扩展到边缘”中扮演的角色。他多次提到“混合云”这个术语，并强调成功的混合云环境需要“云和边缘之间的一致性”。

### 阿宾别夫和斯拉尔巴克

以这些评论为框架，纳德拉继续分享已经使用 Azure 实现这种形式的边缘到云混合计算的客户，包括 ABInBev 和星巴克。星巴克在展厅展示了大量 Azure 驱动的产品，包括 Azure 机器学习驱动的定制订购引擎(Deep Brew)和 Azure 物联网中心和 Azure Sphere 驱动的系统，用于监控和管理星巴克的 Mastrena II 浓缩咖啡机。

[![](img/57437bd7b61b3ec8354099c138374088.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LOhGAAAH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tvt5u0nb8jslqnruvtex.png) 
*星巴克的 AzureML powered DeepBrew 推荐系统*

[![](img/a286eb5f5e740d05ef257335e1884c72.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R_2-9u4E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t28eyq1xjr9tmv5zczdd.png) 
*星巴克贴有白色标签的 Azure 物联网中央仪表盘，用于监控浓缩咖啡机*

如果你在过去十年里一直关注微软，那么“混合云”这个术语听起来会很熟悉。这是微软自 Azure 早期以来一直使用的一种方法，当时鲍尔默的混合愿景是无缝连接的公共云和私有云计算资源的混合。当时的想法是让谨慎的企业客户轻松过渡到云计算，同时让这些客户能够探索弹性计算资源的优势，而不仅仅是转向云。虽然亚马逊的 AWS 在早期是基于反对“拥有铁”的初创公司的计算需求而发展起来的，但微软的面包和黄油是已经拥有大量铁的 EA 签约企业。

鉴于这一历史——据我所知，即使 Azure 在过去十年中不断发展，微软也从未真正偏离这一叙事——微软将“物联网边缘”视为混合云的另一种说法也就不足为奇了。在这个世界中，您的工厂、农场、零售店或卡车是它自己的计算环境，应该与您的其他业务无缝地感知、驱动和计算。

“混合云”是一个强有力的叙述，我确信在过去十年里，它在传统业务中为公司提供了很好的服务。构建完成后，我可以看到它将如何吸引那些追求不太熟悉的连接(即物联网)解决方案的企业。

## Scott Guthrie 分享了 MSFT 对 ML on the edge with options 的愿景

在纳德拉的主题演讲后，Build 观众被邀请在两个深度技术主题演讲中进行选择:一个专注于 Azure，另一个是 Office 365 和 Dynamics 平台。我选择了 Scott Guthrie-helmed Azure 主题演讲，希望从主题演讲中获得更多关于智能边缘和物联网叙述的深度。

与主题演讲一样，Guthrie 的会议覆盖了很多领域，包括所有 Azure 堆栈及其相关开发工具的产品发布和演示。在物联网领域，对话再次集中在混合云计算上，他们的客户越来越关注构建将处理和决策转移到“边缘”的解决方案这在对具有边缘机器学习(ML)组件的演示和解决方案的关注量上尤其明显。正是在那次讨论中，我了解到一些关于微软对边缘和物联网的观点的有趣之处。

在 2019 年的物联网世界中，边缘计算的大部分焦点-特别是当它与 ML 相关时-是如何使用单板计算机和微控制器在边缘上进行 ML。随着 SBC 和 MCU 变得更强大，执行 ML 推理变得可能——也就是说，根据预先训练的模型执行预测；模型训练仍然属于更强大的计算机的领域——在受限制的设备上。

在 Edge ML 产品的世界里，SBC 和 MCU 已经占据了主导地位。谷歌位于 TPU 的 [Coral DevBoard](https://coral.withgoogle.com/) 和 NVIDIA [Jetson Nano](https://developer.nvidia.com/embedded/buy/jetson-nano-devkit) 是最近发布的具有 ML 推理技能的类 Pi SBC 的两个例子。SparkFun 已经发布了一款运行 TensorFlow lite 模型的 [Edge MCU](https://www.sparkfun.com/products/15170) ，而[就在本周](https://os.mbed.com/blog/entry/uTensor-and-Tensor-Flow-Announcement/)在谷歌 I/O 上宣布了 [TensorFlow Lite](https://www.tensorflow.org/lite/) 和[tensor](https://github.com/utensor/)项目的统一，这是基于 MCU 的推理成为现实的强烈信号。

然而，微软似乎对 edge ML 的看法有点不同。该公司没有在 Build 中宣布支持 ML 的 SBC 或 MCU，也没有贬低这些产品的存在是无聊的。相反，今天的 ML 叙事似乎是朝着在云中进行训练和在受限制的设备上进行推理的连续体发展，微软描绘了一幅画面，其中第三种计算选项位于中间:边缘服务器和堆栈之一，从云中卸载训练和从设备进行推理，同时提供与最初使 edge ML 如此诱人的速度和安全性。

在现场演示和视频中，他们展示了一些产品，如 Azure Stack，一个由 FPGA 驱动的边缘处理卡机架，用于将 TB 级数据吸收到云中的 Azure DataBox，以及 [Vision AI DevKit](https://azure.github.io/Vision-AI-DevKit-Pages/#) 。后者可能是最接近微软 SBC 风格的 ML 设备，但即使是它也可以与其他更高性能的 edge 设备协同工作。

这个领域的特色楼层演示来自 Kroger，他正在使用 Vision AI DevKit、Azure Stack 和 DataBox Edge 来执行超市货架上的缺货检测，而无需将流视频回传到云进行 ML 处理和推断。

[![](img/f4f347b844f77271223c3b7e1a8ea4a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hvx5oTRR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eag3m1il5ipj8axozwx2.png) 
*一个克罗格股票检测系统的模拟展台。*

微软并没有明确地将这个故事描绘成具有 ML 能力的 SBC 或 MCU 与其产品之间的非此即彼的关系，正如我们所说的那样，他们很可能已经有了一两个产品。但有一点对我来说很明显，当微软考虑边缘和混合云时，他们认为他们的客户在构建物联网解决方案时需要多种选择，而不仅仅是支持 ML 的 SBC/MCU。

## 微软对物联网的愿景是即插即用

也许你已经注意到微软在今年发布之前的几天和几周内发布了一些产品。有一个[你可能错过了](https://azure.microsoft.com/en-us/blog/intelligent-edge-innovation-across-data-iot-and-mixed-reality/),因为它被埋没在其他几个下面:物联网即插即用。

我看到了[公告，当时它在建造前的星期四被取消了]，但我承认我对此持怀疑态度。我阅读了公告，并快速浏览了 Azure 物联网设备目录，但整个事情对我来说像是一个营销策略:聪明的命名，但对物联网开发者来说没有什么价值。

事实证明，我错了。在周一和周二 Sam George、Peter Provost 和 Briton Zurcher 的几次会议中，我意识到即插即用的命名不仅仅是品牌。事实证明，即插即用模型可能正是物联网所需要的，以使解决方案开发更容易。

想一想当今典型的物联网解决方案。你选择一个 MCU 或 SBC 作为项目的大脑，然后用传感器和执行器填充它来做一些事情。在某些情况下，你甚至可以购买一个独立的产品，如 dashcam 或流式摄像机，并将其直接连接到 Wi-Fi。您编写固件来与设备接口，然后将产品及其各种传感器和执行器连接到云。

通常，将物理产品连接到云解决方案的这一步是最具挑战性的部分。连接性、安全性和消息传递(MQTT、TCP、AMQP 等)。)此外，即使是定义您的解决方案将捕获和发送的遥测数据，或者它响应的云到设备命令，也需要在固件和 Azure IoT Central 等云解决方案中进行手动操作。

[物联网即插即用](https://azure.microsoft.com/en-us/blog/build-with-azure-iot-central-and-iot-plug-and-play/)是微软对这个问题的开源、基于标准的回答。这里的秘密武器是一个被称为设备能力模型的 JSON-LD 模式，它可以用来定义设备元数据，如谁制造它，它运行什么固件版本，它发送和收集什么遥测数据，以及它响应什么云端命令。这里有一个来自[能力模型规范](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL) :
的能力模型的例子

```
{
    "@id": "http://example.com/thermostatDevice/1.0.0",
    "@type": "CapabilityModel",
    "displayName": "Thermostat T-1000",
    "implements": [
        {
            "@id": "http://example.com/thermostat/1.0.0",
            "@type": "Interface",
            "displayName": "Thermostat",
            "contents": [
                {
                    "@type": "Telemetry",
                    "name": "temp",
                    "schema": "double"
                },
                {
                    "@type": "Property",
                    "name": "setPointTemp",
                    "writable": true,
                    "schema": "double"
                }
            ],
            "@context": "http://azureiot.com/v0/contexts/Interface.json"
        },
        "http://azureiot.com/interfaces/deviceInformation/1.1.3"
    ],
    "@context": [
        "http://azureiot.com/v0/contexts/CapabilityModel.json",
        "http://azureiot.com/v0/contexts/Interface.json"
    ]
} 
```

像这样的能力模型的优势在于，它在物联网解决方案中的不同系统之间创建了一个契约。它可以插入 Azure IoT Central 等 SaaS 产品，并自动识别遥测和命令。它可以用来生成 C/C++代码，以收集遥测数据或响应命令。它甚至可以被开发人员工具和 ide(比如 VS 代码)用来基于特定于实现的固件自动生成功能模型。

[![](img/9f430799b7c51e610cf35fe2e2c6f7a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xqeOPTkA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ryitk3hqpm3diwppo44b.png) 
*概述物联网即插即用如何实现解决方案开发流程的各个方面。*

正如 90 年代微软 Windows 的即插即用使消费者能够购买第三方外围设备并相信它们会“正常工作”，物联网即插即用试图为物联网解决方案构建者和设备制造商提供相同的体验。因为[底层定义语言是开源的](https://github.com/Azure/IoTPlugandPlay)，我很有希望它能被广泛使用——超越微软的生态系统。

在我为 Particle 工作的过程中，我遇到了很多开发者、嵌入式和硬件工程师。我们经常发现自己对彼此说的一件事是，做我们所做的事情从来没有比现在更好的时候了。开发套件比以往任何时候都便宜；像 [YouTube](https://www.youtube.com/channel/UCpYjkSkGOXAMXeZjZkbb-PQ) 、 [Twitch](https://twitch.tv/brandonsatrom) 、 [Hackster](https://hackster.io) 和 [Hackaday](https://hackaday.io) 这样的地方资源丰富；像 [Particle](https://particle.io) 这样的物联网厂商继续构建简单、安全、可规模化的平台；Adafruit 的存在是为了确保我们在建造东西的时候不会停止享受乐趣。

如果您希望解决物联网领域的现实问题，这是深入研究的最佳时机。微软似乎同意这一点，在我本周看到这些之后，我认为我们所有在这个领域工作的人现在和将来都会受益。
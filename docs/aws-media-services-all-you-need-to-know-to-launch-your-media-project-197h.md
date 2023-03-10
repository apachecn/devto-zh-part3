# AWS 媒体服务:启动您的媒体项目所需的全部信息

> 原文：<https://dev.to/vitalykuprenko/aws-media-services-all-you-need-to-know-to-launch-your-media-project-197h>

视频处理解决方案的开发阶段可能会导致一些问题。在创建视频点播软件或直播解决方案的过程中，有必要决定您将利用何种服务来使您的软件工作。亚马逊网络服务或 AWS 是应该考虑的。现在是时候讨论如何在您的媒体项目创建期间集成 AWS 媒体服务，以及[自定义 web 应用程序开发服务](https://www.cleveroad.com/services/web-development)如何帮助您了。

## 什么是 AWS 媒体服务？

首先，我们来定义一下 AWS 媒体服务。它们是视频处理的云解决方案，提供了使用 Over-the-Top 或 OTT 技术处理、存储和传输视频的机会。此外，重要的是，只有在应用了所有内容，并且有机会为视频内容处理构建一个可扩展的解决方案而没有大量时间延迟的情况下，才收取服务费用。

AWS 媒体服务允许开发人员使用以下解决方案套件来创建媒体应用程序和网站:

*   **AWS Elemental MediaConnect** 是一个很好的安全视频流传输服务。
*   AWS Elemental MediaConvert 是一项处理视频文件的服务，为开发者提供了以视频点播格式制作流媒体内容的机会。
*   **AWS Elemental MediaLive** 是一项直播服务，允许转换视频内容进行直播。
*   **AWS Elemental MediaPackage** 是一项用于准备和保护网络视频内容的服务。它能够借助不同的标准和格式(例如 MPEG-DASH、SmoothStreaming 等)创建视频流。)用于不同的设备，如电视、手机、个人电脑、平板电脑等。
*   **AWS Elemental MediaStore** 是一款 AWS 存储设备，允许在大量请求的情况下降低读取和记录延迟。因此，用户获得了高质量的服务。
*   **AWS Elemental MediaTailor** 是一项以实时模式工作的服务，它提供了在服务器端将广告植入视频内容的机会。

AWS 媒体服务使得创建用于广播的视频点播内容成为可能。由于向媒体公司提供利用 OTT 技术的网络服务，媒体公司有可能扩大受众。此外，AWS 还能够降低基础设施的价格，并优化媒体内容提供商和广播公司的内容交付流程。

## 如何为媒体项目使用 AWS 媒体服务

您可以在项目中使用 Amazon 的两个媒体服务用例:创建直播流和创建视频点播格式的内容库。让我们更精确地讨论它们。

### 设置直播

视频广播的过程有几个部分:

1.  AWS 元素媒体直播。利用不同的编解码器、字幕叠加和水印添加对视频或音频流进行编码。
2.  AWS 媒体存储。如果需要存储实时视频流记录，这是一个优化的媒体内容存储。有时它可能无法正常工作，所以根据亚马逊的说法，最好使用 AWS S3。
3.  AWS 基本媒体包。所记录的视频流可以使用各种技术来分发，例如 Apple HLS、MPEG-DASH、Microsoft Smooth Streaming 等。
4.  AWS CloudFront。CDN 服务能够在不同的地理区域加速交付。

[![Scheme of video broadcasting process](img/ac6bfb92942fb5b3ee13c4789a82534d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2ByTOOYV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t9dx2b800w08mmjvx31h.png)

直播开发流程包括几个部分:

为了制作媒体项目的实时流，需要在 AWS Elemental MediaPackage 中创建一个通道来处理录制的视频流。这可以通过使用 AWS SDK 或利用开发人员控制台来完成。创建频道需要指明频道 ID。如今，在输入 AWS，基本媒体包只能作为 HLT 协议运行。此外，有必要定制端点。它们是可以接收流式视频的地址。支持以下选项:

*   苹果 HLS
*   微软平滑
*   CMAF 或通用媒体应用格式
*   MPEG-DASH

[![Endpoints customization](img/cc990d19b670989f79897f30200d2216.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q_EEaal2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8mblaqws8rjea8g2hcqv.png)

完成这些步骤后，是时候转向 MediaLive 的定制了。首先，重要的是做好输入(视频流源)。现在，MediaLive 能够获得一个文件(MP4)，一个通过 RTMP/RTP/HLS 协议或 AWS MediaConnent 发送的流。

让我们考虑这个例子。通过 OBS(开放广播软件)从屏幕上获取记录的输入使用 RTMP 协议。之后，是时候在 MediaLive 中创建一个视频流处理通道了。为了做到这一点，有必要选择一个名称，创建 IAM 角色，选择之前的输入，并创建一个输出组。输出组意味着转换后的媒体流的目的地。

[![Output group types](img/981c40d25066b82c478d27dfe7e430f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5IM1jRj1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/89xan2w6uzrbjuq3n59b.png)

AWS MediaLive 支持以下类型的输出组:

*   苹果 HLS
*   向 S3 传输视频流；
*   RTMP(向流媒体服务播放视频流:YouTube、Twitch 等。);
*   微软 Smooth
*   UDP/RTP。

顺便说一下，有机会创建自己的输出组或利用模板选择必要的输出组。

我希望你已经了解了直播的一切。因此，现在是时候看看如何为您的媒体项目创建视频点播格式的简短指南了。

### 视频点播启动

AWS Elemental MediaConvert 服务能够在亚马逊工具的帮助下制作流媒体点播视频。众所周知，MediaLive 实时处理视频流，但 MediaConvert 集成了来自 S3 存储的视频，结果保存在 S3 上。

[![VoD processing using Amazon tools](img/4cae932aceafbfb23e08a9fee5f75ec9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9XLOm9eu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ucu4od0ok89rdto9zxed.png)

开发人员可以遵循开源解决方案的几个步骤:

1.  源文件被上传到 S3，并附带一条注释，以便存档到 S3 冰川自动气象站。
2.  根据 AWS MediaConvert 的元数据模板验证源视频。此外，所有接收到的数据都保存到数据库中。
3.  视频处理作业是基于配置文件进行的。
4.  视频结果保存到 S3。
5.  来自 S3 的信息被缓存在 CloudFront 中以加速交付，
6.  CloudWatch 用于查找错误。

[![Source video processing scheme](img/f4df696938fb62f907dbec6121130429.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3L15siWT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wi30rkqiey0vpz41w9en.png)

当然，AWS 媒体服务整合的过程并不简单。重要的是要有特定的技能和经验，才能整合一切，达到好的效果。
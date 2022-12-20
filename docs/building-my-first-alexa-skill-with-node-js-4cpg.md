# 用 Node.js 构建我的第一个 Alexa 技能

> 原文：<https://dev.to/jonathanbrizio/building-my-first-alexa-skill-with-node-js-4cpg>

## 要求

*   亚马逊开发者账户
*   Node.js 使用体验

## Alexa 是什么？

这是亚马逊的语音控制系统。有了它，你可以控制音乐，创建购物清单或提醒，获得即时天气预报，并控制流行的智能家居产品，只需使用你的声音。

## 什么是 Alexa 技能？

这些应用为 Alexa 设备提供了更多之前提到的功能。绝对各种。通过 Alexa 应用程序，你可以合并或禁用这些技能。

## 什么是 AWS Lambda？

AWS Lambda 是一种计算服务，让您无需配置或管理服务器即可运行代码。您只需为您消耗的计算时间付费——当您的代码不运行时，不收取任何费用。这将是我们的后端，处理请求并将数据作为 JSON 返回。

## 参与流程的实体

*   说出命令的用户
*   接收初始语音输入的回声设备
*   亚马逊 Alexa 服务，从设备上接收数据
*   包含技能本身的业务逻辑的后端服务

[https://www.youtube.com/embed/Rsm7XnTQIiQ](https://www.youtube.com/embed/Rsm7XnTQIiQ)

好了，现在你已经有了开始开发所需的所有材料，我邀请你[安装我的最新技能](https://www.amazon.com/dp/B07PSBHYJ8/)叫做“美元价格”。这是一个用公共 API 获取美元和阿根廷比索之间官方汇率的实验。
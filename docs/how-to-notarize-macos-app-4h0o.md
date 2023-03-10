# macOS app 如何公证

> 原文：<https://dev.to/onmyway133/how-to-notarize-macos-app-4h0o>

## 新公证要求

[https://developer.apple.com/news/?id=04102019a](https://developer.apple.com/news/?id=04102019a)

> 随着 macOS 10.14.5 的公开发布，我们要求所有首次创建开发者 ID 证书的开发者对他们的应用进行公证，并且所有新的和更新的内核扩展也要进行公证

## 为看门人签署您的应用

[https://developer.apple.com/developer-id/](https://developer.apple.com/developer-id/)

> 未发布的软件。很容易通过导出过程或 xcodebuild 获得未发布软件的公证。用于上传的 xcrun altool 命令行工具支持自定义构建工作流，您可以使用 xcrun stapler 将标签附加到包中。
> 
> 发布的软件。要提交您已经发布的软件，请使用 xcrun altool 命令行工具进行上传。支持多种文件类型，包括。zip，。pkg，还有。dmg，因此您可以上传已经分发给用户的相同包。

## 分发前公证您的应用程序

[https://developer . apple . com/documentation/security/notarization _ your _ app _ before _ distribution](https://developer.apple.com/documentation/security/notarizing_your_app_before_distribution)

[![](img/6d454b1405fed0048164ae9daa1f0bff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x8kF9X-s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://docs-assets.developer.apple.com/published/a857ce6120/e02fdecf-0018-46d8-a750-c0719d21e326.png)

> 当您点按“下一步”时，Xcode 会将您的档案上传到公证服务。上传完成后，公证服务开始扫描过程，通常不到一个小时。当公证服务扫描您的软件时，您可以继续准备您的归档文件进行分发。例如，在向客户提供软件之前，您可以导出归档文件并执行所需的任何最终测试。
> 
> 当公证过程完成时，Xcode 会下载票证并将其装订到您的档案中。此时，再次导出您的归档文件，以获得包含公证票据的软件的可分发版本。

原帖[https://github.com/onmyway133/blog/issues/203](https://github.com/onmyway133/blog/issues/203)
# 测试 chrome 扩展中的“安装”和“更新”流程

> 原文：<https://dev.to/gokatz/testing-install-and-update-flows-in-chrome-extensions-2e40>

*原发于[我的个人博客](https://blog.gokatz.me/test-install-update-flow-chrome-extensions):[https://blog . gokatz . me](https://blog.gokatz.me)*

这是一个非常小的帖子，指出了一个现有的 chrome 应用程序开发指南，关于安装和更新流程测试 chrome 扩展。这些测试可能会变得棘手，因为在开发阶段我们使用了 [**未打包的扩展**](https://developer.chrome.com/extensions/getstarted#manifest) 。使用这种方法，我们可能无法测试实际的安装和更新流程，因为相关的事件不会被触发。

例如，当使用未打包的扩展时，很难测试*权限*流。我们可能会在清单中包含新的权限，并需要在进入网络商店之前进行测试。

有一种方法可以测试这样的场景。我们可以在本地打包扩展(到一个`.crx`文件中)并从一个本地文件安装我们实际的扩展(使用扩展的`.crx`格式)。因此，chrome 会将其视为正常安装。因此，我们可以测试所有的安装和更新工作流。

chrome 扩展开发指南的[部分介绍了以下过程:](https://developer.chrome.com/extensions/permission_warnings#view_warnings)

*   为您的扩展创建一个`.crx`文件(扩展源)和`.pem`文件(私有密钥)
*   将`.crx`文件安装到 Chrome 中
*   使用`.pem`文件模拟更新过程。如果加载的扩展没有一个`.pem`文件，它将作为一个新的安装，如果我们加载一个现有的`.pem`文件，加载的`.crx`文件将被视为一个现有扩展的更新(如果存在)

这一部分将集中在我们作为例子看到的许可部分。希望这有助于自信地部署您的扩展。

**奖金注意:**

总是为你的扩展准备一个阶段构建，比如一个单独的 webstore 扩展[，只对测试人员](https://developer.chrome.com/webstore/publish#publishing-to-test-accounts)可见。在使测试扩展生效之前，发布新的版本到测试扩展。我打赌这会让你免于一大堆尴尬。这对我帮助很大😉
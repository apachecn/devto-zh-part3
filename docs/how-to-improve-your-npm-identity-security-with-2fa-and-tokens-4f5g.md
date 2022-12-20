# 如何使用 2FA 和令牌提高您的 npm 身份安全性

> 原文：<https://dev.to/lirantal/how-to-improve-your-npm-identity-security-with-2fa-and-tokens-4f5g>

## 启用 2FA

2017 年 10 月，npm 正式宣布支持使用 npm 注册表托管其封闭和开源包的开发者进行双因素身份认证(2FA)。

尽管现在 npm 注册中心已经支持 2FA 有一段时间了，但它似乎正在慢慢被采用，一个例子是 2018 年年中的 eslint-scope 事件，当时 eslint 团队的一个被盗开发者帐户导致不良行为者发布了 eslint-scope 的[恶意版本。](https://snyk.io/vuln/npm:eslint-scope)

注册表支持在用户帐户中启用 2FA 的两种模式:

*   仅限授权—当用户通过网站或 CLI 登录 npm，或执行其他操作集(如更改配置文件信息)时。
*   授权和写入模式—配置文件和登录操作，以及管理令牌和包等写入操作，以及对团队和包可见性信息的少量支持。

给自己配备一个认证应用程序，比如谷歌认证，你可以把它安装在移动设备上，然后你就可以开始了。

为您的帐户开始使用 2FA 扩展保护的一个简单方法是通过 npm 的用户界面，它允许非常容易地启用它。如果您是命令行人员，在使用受支持的 npm 客户端版本(> =5.5.1)时，启用 2FA 也很容易:

```
$ npm profile enable-2fa auth-and-writes 
```

Enter fullscreen mode Exit fullscreen mode

按照命令行说明启用 2FA，并保存紧急身份验证代码。如果您希望仅针对登录和配置文件更改启用 2FA 模式，您可以将上面代码中的`auth-and-writes`替换为`auth-only`。

## 使用 npm 作者令牌

每次使用 npm CLI 登录时，都会为您的用户生成一个令牌，并向 npm 注册表验证您的身份。令牌使得在 CI 和自动化过程中执行 npm 注册表相关的操作变得容易，例如访问注册表上的私有模块或从构建步骤发布新版本。

可以通过 npm 注册表网站以及使用 npm 命令行客户端来管理令牌。

使用 CLI 创建仅限于特定 IPv4 地址范围的只读令牌的示例如下:

```
$ npm token create --read-only --cidr=192.0.2.0/24 
```

Enter fullscreen mode Exit fullscreen mode

要验证为您的用户创建了哪些令牌，或者在紧急情况下撤销令牌，您可以分别使用`npm token list`或`npm token revoke`。

-

我还在博客中介绍了一个完整的 [10 npm 安全最佳实践](https://snyk.io/blog/ten-npm-security-best-practices/),你应该在帖子中采用，其中包括一个高分辨率的可打印 PDF，如下图所示。

感谢阅读，也感谢 Verdaccio 团队的 Juan Picado 和我一起工作。[看看这个](https://snyk.io/blog/ten-npm-security-best-practices/)

[![Node Version](img/7581a36bdb7e2700216f6b77152f52fb.png)](https://snyk.io/blog/ten-npm-security-best-practices/)
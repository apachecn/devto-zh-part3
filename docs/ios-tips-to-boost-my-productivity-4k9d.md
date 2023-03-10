# 提升我工作效率的 iOS 技巧

> 原文：<https://dev.to/renee/ios-tips-to-boost-my-productivity-4k9d>

这是我第一次在 Dev.to 上写这个技术博客。

这篇技巧将列出所有有用的库和工具，我和我的团队每天都在使用它们。

希望这个技巧可以提高你在 iOS 开发过程中的生产力😍

### [莫亚](https://github.com/Moya/Moya)

Moya 是一个方便的库，可以帮助我轻松地对联网路线进行分类。我没有引入大的网络类，而是定义了`AppEndpoint`枚举，并在这里介绍了所有的路由。

```
public enum AppEndpoint {
    case login(LoginParam)
    case signup(SignupParam)
    case fetchUserInfo(UserParam)
}

provider = MoyaProvider<AppEndpoint>()
provider.request(.login(param)) { result in
    switch result {
    case let .success(moyaResponse):
        // Handle data
    case let .failure(error):
       // Error
} 
```

### 优点

*   抽象网络层
*   隐藏业务逻辑
*   轻松地对 API 路线进行分类
*   漂亮的结果。

### [TinyConstraints](https://github.com/roberthein/TinyConstraints)

一个轻量级类，帮助我克服使用 NSConstraintLayout 时的痛苦。

只是一行代码附加子视图填充父视图；D

```
view.edgesToSuperView() 
```

## [揭示](https://revealapp.com/)

Reveal 是我实现 pixel perfect 的重要 app 之一。大多数时候我检查视图层，Xcode 工具对我来说是不够的，因为我必须计算关闭像素。揭示是我的救命恩人。

[![Reveal](img/d4e90a42f7832a98831acfc41504dc10.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F7VYQsxG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3vsrg46bm8ba4q9nc3k1.png)

### 优点

*   轻松检查复杂的视图层次结构
*   不需要直接从 Xcode 调试
*   原生应用

## [Proxyman](https://proxyman.io/)

Proxyman 是我公司 QA 和 iOS 团队的必备工具之一。

这有助于我看到 HTTPS 的反应，并更容易捕捉到错误。过去，我们使用 Charles，但是我的团队更喜欢 Proxyman，因为它的 UI 更好
[![Proxyman](img/395c29767b755b84d8ecb3e0d4618dd1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6k3kP2eu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qpl8mtn7hkejl4oh5yhr.jpg)

*   请求和响应的内容是如此甜蜜
    [![Proxyman](img/05ed0305406c124b0649ae7e61ea6f4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GI8zy4pT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2p0mmgjjwhfg4qefc483.png)

*   轻松编辑 bug 请求，并使用新参数
    [![Repeat](img/1ac042ffc47f9d9261ac1ef581f7836f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--il1YwJuZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/28qn2fwteuk20a8icrha.png) 重试

*   能够为 QA 团队导出会话——这对以后重现 bug 非常有用
    [![Debug](img/b1717c0456e901705dcefed00878f304.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8KfrAhlu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bxkgvc9d280u3vr7w6fm.png)

### 优点

*   现代和本地应用程序
*   轻松重现 QA 团队的 bug
*   使用 iOS 模拟器和 iPhone 也可以，这太棒了😮

### 其他

*   [SwiftLint](https://github.com/realm/SwiftLint) 用于执行语法
*   [危险](https://github.com/danger/danger):Github 中伴随 SwiftLint 自动通知队友。
*   [浪子](https://github.com/fastlane/fastlane)和 CI/CD 位上升
*   [领域](https://realm.io/)是数据库存储和轻量级 [MMKV 键值存储的最佳候选](https://github.com/Tencent/MMKV)
*   [IGListKit](https://github.com/Instagram/IGListKit) 高性能上市。
*   高效重载超大 UITableView 的 Diff 算法。

如果你知道什么有用的 iOS 库或工具，请与我分享👏
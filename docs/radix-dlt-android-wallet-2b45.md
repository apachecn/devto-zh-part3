# DLT -安卓钱包

> 原文：<https://dev.to/radixdlt/radix-dlt-android-wallet-2b45>

Radix DLT Android 钱包目前与实时 ALPHANET 宇宙一起工作，并通过充分利用最新发布的 [radixdlt-kotlin 库](https://github.com/radixdlt/radixdlt-kotlin)与之交互。

该应用程序正在开发中，将很快迁移到我们网络的新版本。

[![alt text](img/e196c2487f8452bc5e917c7c07223581.png "Wallet")](https://res.cloudinary.com/practicaldev/image/fetch/s--SmZxNqgP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/radixdlt/radixdlt-wallet-android/raw/master/art/unlock_wallet.jpg)

[![alt text](img/34515de39e1b9181935c84cff360ce21.png "Wallet")](https://res.cloudinary.com/practicaldev/image/fetch/s--zBnt-Qpg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/radixdlt/radixdlt-wallet-android/raw/master/art/transactions_screen.jpg)

[![alt text](img/0bbc1a0d5d241b68ff6814719a23712e.png "Wallet")](https://res.cloudinary.com/practicaldev/image/fetch/s--Wd4nDjNz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://github.com/radixdlt/radixdlt-wallet-android/raw/master/art/contacts_screen.jpg)

# 安卓开发

-大部分是用 [Kotlin](https://kotlinlang.org/) 编写的(少数类是用 Java 编写的，但将被转换以保持 100% Kotlin)
-使用[架构组件](https://developer.android.com/topic/libraries/architecture/) : Room、LiveData 和 life cycle-Components
-使用 [dagger-android](https://google.github.io/dagger/android.html) 进行依赖注入
-使用 [RxJava 2](https://github.com/ReactiveX/RxJava) (默认包含在 radixdlt 库内)

# 开发设置

使用 Android Studio 3.2.1(或更高版本)来构建应用程序。

该应用目前使用的是最新发布的 [radixdlt-kotlin](https://github.com/radixdlt/radixdlt-kotlin) 库。有可能用 [radixdlt-java](https://github.com/radixdlt/radixdlt-java) 库来替换它，但是你需要瞄准更新的 android 设备> = API 24。此外，需要对代码做一些简单的修改，对 [rxkotlin](https://github.com/ReactiveX/RxKotlin) 的依赖必须包含在你的 gradle 文件中。

```
implementation(group: 'io.reactivex.rxjava2', name: 'rxkotlin', version: '2.2.0') {
    exclude group: 'io.reactivex.rxjava2', module: 'rxjava'
    exclude group: 'org.jetbrains.kotlin', module: 'kotlin-stdlib'
} 
```

# 代码样式

该项目通过 [Gradle](https://gradle.org/) 依赖关系使用 [ktlint](https://github.com/shyiko/ktlint) 。检查代码样式- `gradle ktlint`(也绑定到`gradle check`)。

# 投稿

欢迎投稿，我们只要求:

-派生代码库
-进行更改
-提交拉取请求以供审查
在对该存储库做出贡献时，我们建议在做出更改之前，通过问题、电子邮件或任何其他方法与该存储库的所有者讨论您希望做出的更改。

请在您与项目的所有互动中遵守我们的[行为准则](https://github.com/radixdlt/radixdlt-wallet-android/blob/master/CODE_OF_CONDUCT.md)。

代码可以在我们的 [GitHub](https://github.com/radixdlt/radixdlt-wallet-android) 中找到

# 加入 Radix 社区

[电报](https://t.me/radix_dlt)普通聊天
[不和](https://discord.gg/7Q7HSZZ)开发者聊天
[Reddit](https://reddit.com/r/radix) 普通讨论
[论坛](https://forum.radixdlt.com/)技术讨论
[推特](https://twitter.com/radixdlt)发布公告
[电子邮件](https://radixdlt.typeform.com/to/nyKvMV)简讯每周更新
邮件[hello@radixdlt.com](//hello@radixdlt.com)普通查询
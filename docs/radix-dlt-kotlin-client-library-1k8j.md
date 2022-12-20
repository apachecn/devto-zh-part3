# DLT 根-科特林客户端库

> 原文：<https://dev.to/radixdlt/radix-dlt-kotlin-client-library-1k8j>

# 简介

这个库提供了对 Kotlin/Java 项目中的 [Radix](https://www.radixdlt.com/) 分布式分类帐的访问，同时最大限度地兼容所有版本的 Android。

通过避免使用任何 Java 8 APIs，例如 Stream、Optional、Function 等，实现了与较低版本 Android 的兼容性。并使用 Kotlin 内置替代品。

# 代码样式

该项目通过 [Gradle](https://gradle.org/) 依赖关系使用 [ktlint](https://github.com/shyiko/ktlint) 。
检查代码样式- `gradle ktlint`(也绑定到`gradle check`)。

# 特性

-连接到 Alphanet 测试网络
-测试网络的免费交易
-身份创建
-本地令牌传输
-不可变数据存储
-即时消息和测试令牌钱包 DApp 实现
-基于 RXJava 2 的
-利用 Websockets 上的 JSON-RPC

# [度](#gradle)

包括以下梯度相关性:

```
repositories {
    maven { url 'https://jitpack.io' }
}​ 
```

```
dependencies {
    implementation 'com.radixdlt:radixdlt-kotlin:0.11.3'
} 
```

# 身份

身份是用户进入分类帐的凭证(更确切地说，是公钥/私钥对的管理者)，允许用户拥有和发送令牌以及解密数据。

从文件创建/加载身份:

```
val identity: RadixIdentity = RadixIdentities.loadOrCreateEncryptedFile("filename.key", "password") 
```

这将创建一个新文件，该文件存储公钥/私钥并用给定的密码加密。

# 宇宙

一个宇宙是一个基数分布式分类帐的实例，它是由一个起源原子和一组形成一个网络的不允许的动态节点定义的。
引导至 Alphanet 测试网络:

```
RadixUniverse.bootstrap(Bootstrap.ALPHANET) 
```

> 注意:除非需要，否则不会建立网络连接。

# DApp 原料药

Radix 应用程序 API 是一个客户端 API，它公开了高级抽象以使 DApp 创建更容易。

初始化 API:

```
RadixUniverse.bootstrap(Bootstrap.ALPHANET) // This must be called before RadixApplicationAPI.create()
val api: RadixApplicationAPI = RadixApplicationAPI.create(identity) 
```

# 地址

地址是对帐户的引用，并允许用户从其他用户接收令牌和/或数据。

您可以通过以下方式获得自己的地址:

```
val myAddress: RadixAddress = api.myAddress 
```

或者来自 base58 字符串:

```
val anotherAddress: RadixAddress = RadixAddress.fromString("JHB89drvftPj6zVCNjnaijURk8D8AMFw4mVja19aoBGmRXWchnJ") 
```

# 存储和检索数据

不可变的数据可以存储在分类帐中。数据可以加密，以便只有选定的身份可以读取数据。

存储只有用户可以读取的加密字符串`Hello`:

```
val myPublicKey: ECPublicKey = api.myPublicKeyval
data: Data = Data.DataBuilder().bytes("Hello".toByteArray()).addReader(myPublicKey).build()
result: Result = api.storeData(data, <address>) 
```

存储未加密数据:

```
val data: Data = Data.DataBuilder().bytes("Hello World".toByteArray()).unencrypted().build()
val result: Result = api.storeData(data, <address>) 
```

返回的`Result`对象公开了 RXJava 接口，您可以从这些接口获得存储动作状态的通知:

```
result.toCompletable().subscribe(<on-success>, <on-error>) 
```

然后读取(如有必要，解密)某个地址的所有可读数据:

```
val readable: Observable<UnencryptedData> = api.getReadableData(<address>)
readable.subscribe { data ->  ...  } 
```

> 注意:不能被用户密钥解密的数据将被忽略

# 发送和检索令牌

从我的帐户发送一定数量的测试(testnet 本地令牌)到另一个地址:

```
val result: Result = api.sendTokens(<to-address>, Amount.of(10, Asset.TEST)) 
```

检索我的账户中发生的所有代币转账:

```
val transfers: Observable<TokenTransfer> = api.getMyTokenTransfers(Asset.TEST)
transfers.subscribe { tx -> ... } 
```

获取我帐户中测试代币余额的流:

```
val balance: Observable<Amount> = api.getMyBalance(Asset.TEST)
balance.subscribe { bal -> ... } 
```

# 加入 Radix 社区

[电报](https://t.me/radix_dlt)普通聊天
[不和](https://discord.gg/7Q7HSZZ)开发者聊天
[Reddit](https://reddit.com/r/radix) 普通讨论
[论坛](https://forum.radixdlt.com/)技术讨论
[推特](https://twitter.com/radixdlt)发布公告
[电子邮件](https://radixdlt.typeform.com/to/nyKvMV)简讯每周更新
邮件[hello@radixdlt.com](//hello@radixdlt.com)普通查询
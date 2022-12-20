# 将你的第一个静态网站部署到 IPFS 的完全初学者指南

> 原文：<https://dev.to/agentofuser/the-complete-beginner-s-guide-to-deploying-your-first-static-website-to-ipfs-33po>

这将是有史以来最快的教程。几乎没什么印象。

你不需要了解任何关于 IPFS 或分布式的东西，甚至静态站点生成器也不需要。

[![static<br>
fire](img/0bdb0a085e06afd2ba326085db6ea649.png "static fire")](https://res.cloudinary.com/practicaldev/image/fetch/s--K7vwOQr---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0kei65mk4lgtevcs4e0p.jpg)

准备好了吗？好的，第一步是你要打开你的终端，输入这个:

```
mkdir -p dwebsite/public
cd dwebsite
echo '<h1>Hello, worlds!</h1>' >> public/index.html

yarn global add @agentofuser/ipfs-deploy
# or: npm install -g @agentofuser/ipfs-deploy
ipd 
```

Enter fullscreen mode Exit fullscreen mode

你和我在一起吗？好了，打好了。还有什么？没什么。

什么？没错。这里的任务已经完成。

现在你可以坐下来看胜利游行了😎

```
ℹ 🤔 No path argument specified. Looking for common ones…
✔ 📂 Found local public directory. Deploying that.
✔ 🚚 public weighs 24 B.
✔ 📌 It's pinned to Infura now with hash:
ℹ 🔗 QmQzKWGdjjQeTXrruYL2vLkCqRP8TyXnG1a9QEJjDM8WTY
✔ 📋 Copied HTTP gateway URL to clipboard:
ℹ 🔗 https://ipfs.infura.io/ipfs/QmQzKWGdjjQeTXrruYL2vLkCqRP8TyXnG1a9QEJjDM8WTY
✔ 🏄 Opened web browser (call with -O to disable.) 
```

Enter fullscreen mode Exit fullscreen mode

现在你知道了。你自己的 l33t [#dwebsite](https://twitter.com/search?q=%23dweb) 住在 hashlinked 的 Merkleverse 上。[看看这个](https://cloudflare-ipfs.com/ipfs/QmQzKWGdjjQeTXrruYL2vLkCqRP8TyXnG1a9QEJjDM8WTY)。与你的朋友分享。

很甜蜜，是吧？🍬

## 慢点说，刚才到底发生了什么？

好吧，一下子接受太多了。让我们倒回去一点，用慢动作看，有后台解说:

### 1。东西在哪里？

说实话，我本可以调用`ipd ./public`，显式地传递要部署的目录(`public`)。

但是这样你就不会看到“努力思考”表情符号作为 **ipfs-deploy** 探测器，智能地为静态站点生成器常用的众多[构建目的地中的一个进行探测。](https://github.com/agentofuser/ipfs-deploy/blob/8023d82b5df68076ca79bc684f45ea64e5e67c06/index.js#L75) 

```
ℹ 🤔 No path argument specified. Looking for common ones…
✔ 📂 Found local public directory. Deploying that. 
```

Enter fullscreen mode Exit fullscreen mode

是的，我实际上梳理了[staticgen.com](https://www.staticgen.com)，安装了一堆静态站点生成器，并建立了小测试站点，这样我就可以宣称“零配置”标题。这是小事，你知道。

这就是 ipfs-deploy 在我们懒得打出来的时候寻找的东西:

```
const guesses = [
  '_site',         // jekyll, hakyll, eleventy
  'site',          // forgot which
  'public',        // gatsby, hugo
  'dist',          // nuxt
  'output',        // pelican
  'out',           // hexo
  'build',         // metalsmith, middleman
  'website/build', // docusaurus
  'docs',          // many others
] 
```

Enter fullscreen mode Exit fullscreen mode

从这个博客的域名可以看出，我是盖茨比的粉丝，但是 **ipfs-deploy** 服务于所有需要的人。带上你自己的 SSG，我们会把星际喷气背包放在它的背上，让它飞起来。🚀

### 2。上传

这就是我们在这里的目的，也就是把网站**放到空间**(暂时是比喻)。所以几秒钟后，ipfs-deploy 提供了:

```
✔ 📌 It's pinned to Infura now with hash:
ℹ 🔗 QmQzKWGdjjQeTXrruYL2vLkCqRP8TyXnG1a9QEJjDM8WTY 
```

Enter fullscreen mode Exit fullscreen mode

头奖！这就是钱的来源。

这一小段杂乱无章的杂凑是整个网络柔道的症结所在。神奇的话语从连接的地牢的洞穴深处召唤出你的网站的名字，不关心它在哪里，只关心它**是什么**。

**固有寻址，不受位置或路线的限制。**

欢迎来到分布式的未来。

#### 等一下，我好像听到你说“分发”🧐

哦，你这个敏锐的读者。

你是对的:如果 IPFS 应该是一个点对点协议，我们为什么还要上传呢？到一个**服务器**！？🤢

难道我们不应该向网络宣布我们已经有了散列，然后等着在其他对等点请求时自己把它提供给它们吗？

是啊，你可以这样做。然后你合上笔记本电脑，无线网络关闭，💩发生了，噗的一声，✨，你的网站完蛋了。

这就像种子一样:你需要至少一个种子来获取内容。如果你的网站有成千上万的人运行他们自己的 IPFS 节点来访问它并重新提供给其他人，那么平均来说你的正常运行时间会很高。

但是现在还没有那么多这样的访问者(希望勇敢的 T1 能帮我们解决这个问题)，而且，这是一个全新的网站！🐣可怜的人不是生来就出名的。给它一些时间。

如果浏览器有一个像样的 [#asyncUX](https://twitter.com/search?q=%23asyncUX) ，访问者可以很容易地告诉他们在对等点可用时排队下载，然后在它准备好时通知(就像一个无缝的“稍后阅读”流程)，这将是一个不同的故事。

但是就目前情况来看，如果在发出请求的那一刻没有人存在，那么事情就会挂起，然后超时。肯定不是[空间就绪](https://dev.to/foreword/)。

所以我们这边需要一台高运行时间的播种机。或者，用 IPFS 的行话来说，是“平纳”

#### 用 Infura.io 进行零配置固定

ipfs-deploy 的一个很大的设计目标是让你有第一次 T2 的快乐体验，尽可能快地看到你在 ipfs 做的东西。

一种方法是运行一个本地 IPFS 守护进程，让你自己提供内容。

但是正如我们在上面看到的，这可能有点噱头，因为它并不代表您可以与朋友分享的实际部署会是什么样子。得有个稳定的 pinner。

虽然正常运行时间的钉东西需要钱，所以大多数钉服务可以理解地要求你至少注册一个免费层，然后他们才会同意托管你的网站。

不过不是 [Infura.io](https://infura.io/docs/ipfs/get/block_get.md) ！

通过谨慎的速率限制、聪明的滥用预防、增长资本或不计后果的放弃，他们让你突然上传东西，[未经认证](https://community.infura.io/t/how-often-do-you-garbage-collect-files-on-your-ipfs-node-and-how-long-will-a-file-persist-after-upload/44/2)，他们会无限期地为你服务。(这似乎违背了你的意愿，因为目前没有明确的方法来解决问题。)

因此，我们应该感谢他们的慷慨:感谢 Infura 的所有人，请保持下去！

另外，如果您自己拥有 pin 服务，并且希望成为零配置欢迎包的一部分，请考虑添加一个不需要注册的“更自由”层。

新创建的静态网站不占太多空间，流量很小，是进一步 IPFS 消费的一个很好的入口。

## 你做到了！🏁

如果你走到这一步。你真棒！🗿

你不仅部署了你的第一个 IPFS 网站，现在你可以夸耀你实际上明白了它是如何工作的，只需挥挥手说“哦，它很像 git + bittorrent，你知道，很简单！”

如果你努力按照说明去做，不知何故事情就在你面前发生了，那是我的责任，不是你的。这是我的承诺:**你的第一次快乐体验，不然就是 bug！**

所以请[告诉我哪里出了问题](https://github.com/agentofuser/ipfs-deploy/issues/new)，我会帮你解决的。

我们都想消除这里的摩擦，🧹🥌

如果你还没有足够的，留下来获得一些额外的学分。

如果你对我们目前所取得的成果感到满意，请通过广泛传播和支持本指南来与他人分享你的感受:)谢谢！

* * *

## 奖金章

### 用 Pinata.cloud 实现自由冗余

拥有一个稳定的 pinner 是很酷的，但是和普通的虚拟主机没有太大的区别。(在“分布性”的意义上，也就是说。在“[内容可寻址的](https://www.youtube.com/watch?v=YIc6MNfv5iQ) ness”意义上，它是白天和黑夜。)

体验 IPFS 分布式本质的一个方法是添加第二个 pinner，而 **ipfs-deploy** 使这变得更容易。

我们将把两者都部署到 **Infura.io** 和 **Pinata.cloud** 上，这样访问者可以同时从两者下载，或者在一个失败的情况下从另一个下载。

韧性！🤹

Pinata.cloud 是一个专门的 IPFS 锁定服务，让你对托管的内容有更多的控制。

它允许您删除 pin 并添加元数据，您稍后可以使用这些元数据来过滤和管理您的部署。

有一个 **1 GB 的空闲层**，对于开发博客、登录页面、文档和[#杨刚](https://twitter.com/search?q=%23yanggang)粉丝页面来说足够了。它*不需要注册，但它非常简单，不需要信用卡或个人信息。*

在[注册](https://pinata.cloud/signup)并获得 [API 密匙](https://www.pinata.cloud/documentation#GettingStarted)后，进入你的网站目录并将你的密匙复制到一个`.env`文件中，如下:

```
# dwebsite/.env
IPFS_DEPLOY__PINATA__API_KEY=paste-the-api-key-here
IPFS_DEPLOY__PINATA__SECRET_API_KEY=and-the-secret-api-key-here 
```

Enter fullscreen mode Exit fullscreen mode

**⚠⚠**你不想公开这些信息，所以当你在一个将要公开托管的存储库中做这件事的时候，确保将`.env`文件添加到你的`.gitignore` :
中

```
echo .env >> .gitignore 
```

Enter fullscreen mode Exit fullscreen mode

最后一个配置:要部署到 Pinata，您需要将路由器上的端口 4002 转发到您的机器。

为什么？因为部署到 Pinata 是这样工作的:

1.  我们首先启动一个临时的本地 IPFS 节点，
2.  在本地锁定网站，
3.  然后把哈希发给皮纳塔。
4.  Pinata 然后**作为对等点**连接到我们的本地节点，
5.  请求我们发送给它的散列，
6.  然后自己下载并托管它。

由于步骤 4，我们需要能够监听外部连接。

我知道手动转发端口很痛苦，但是本季度 js-ipfs 将支持 [NAT 穿越，所以我们很快就会少一个障碍🤞](https://github.com/libp2p/js-libp2p/issues/104#issuecomment-488720839)

相比之下，Infura 公开了他们的 IPFS 节点的 HTTP API，因此我们可以作为 HTTP 客户端直接上传到它，而无需运行本地节点或侦听连接。

Pinata 的定制 API 在灵活性——零可配置性谱中找到了不同点。

谢天谢地，有了 **ipfs-deploy** ，我们可以同时拥有✌️

既然 Pinata 已经设置好了，让我们回到节目中来。下面是您运行以部署到两个固定服务的内容:

```
ipd -p infura -p pinata 
```

Enter fullscreen mode Exit fullscreen mode

这就是你得到的:

```
ℹ 🤔 No path argument specified. Looking for common ones…
✔ 📂 Found local public directory. Deploying that.
✔ 🚚 public weighs 24 B.
✔ 📌 It's pinned to Infura now with hash:
ℹ 🔗 QmQzKWGdjjQeTXrruYL2vLkCqRP8TyXnG1a9QEJjDM8WTY 
```

Enter fullscreen mode Exit fullscreen mode

目前没有新消息。现在到 Pinata(以新表情符号为特色！):

```
✔ ☎️ Connected to temporary local IPFS node.
✔ 📶 Port 4002 is externally reachable.
✔ 📌 Pinned to temporary local IPFS node with hash:
ℹ 🔗 QmQzKWGdjjQeTXrruYL2vLkCqRP8TyXnG1a9QEJjDM8WTY
✔ 📌 It's pinned to Pinata now with hash:
ℹ 🔗 QmQzKWGdjjQeTXrruYL2vLkCqRP8TyXnG1a9QEJjDM8WTY
✔ ✋️ Stopped temporary local IPFS node. 
```

Enter fullscreen mode Exit fullscreen mode

就是这样:相同的散列，不同的位置。

```
✔ 📋 Copied HTTP gateway URL to clipboard:
ℹ 🔗 https://ipfs.infura.io/ipfs/QmQzKWGdjjQeTXrruYL2vLkCqRP8TyXnG1a9QEJjDM8WTY
✔ 🏄 Opened web browser (call with -O to disable.) 
```

Enter fullscreen mode Exit fullscreen mode

通过将“ipfs.infura.io”替换为:

*   [gateway.pinata.cloud](https://gateway.pinata.cloud/ipfs/QmQzKWGdjjQeTXrruYL2vLkCqRP8TyXnG1a9QEJjDM8WTY)
*   [ipfs.io](https://ipfs.io/ipfs/QmQzKWGdjjQeTXrruYL2vLkCqRP8TyXnG1a9QEJjDM8WTY)
*   [cloudflare-ipfs.com](https://cloudflare-ipfs.com/ipfs/QmQzKWGdjjQeTXrruYL2vLkCqRP8TyXnG1a9QEJjDM8WTY)

或者这里列出的任何一个:[https://ipfs.github.io/public-gateway-checker](https://ipfs.github.io/public-gateway-checker)

我以前说过，但我觉得这太酷了，值得重复:

> 固有寻址，不受位置或路由的限制。

或者:**通过*调用数据*在哪里，而不是*在*在哪里。**

那是给你的密码杂凑💪

### 🗣的告白部分

好了，我们结束了，对吧？感觉好极了。现在去电话里告诉你的朋友吧📞

嘿，杰西，你猜怎么着？

—什么？

——我已经在网上开了我的网站！！

——耶朋友，多酷啊！我打赌那一定非常困难。不，有这个 npm 包，你知道的...是的，是的，让我看看网站，在哪里？哦，它在 I-p-f-s-dot-I-o-slash-I-p-f-s-slash...嗯...

—斜线什么？

——是呃...你有笔吗？这是呃...大写 Q，小写 m，大写 Q，
大写 T...啊...我把网址发给你怎么样？好吧，当然，但是如果我只是一个在广告牌上看到你的网址的人呢？到时候你就不能给我发短信了，对吧？

—嗯，我想不会。你知道吗，当你有值得我在浏览器中输入的东西时，打电话给我怎么样？

—哇，刺耳。

好吧，那场栩栩如生的对话很快就结束了。

事实证明，内容寻址本身[与有限的人类记忆缓冲区](https://en.wikipedia.org/wiki/Zooko's_triangle)不太协调。

另外，朋友也很难相处。

那我们该怎么办？

#### 一个漂亮的网址

IPFS 网站的人类可读命名肯定是一个需要理顺的领域。

但是，如果(这是一个很大的假设)你现在还在使用免费的 Cloudflare IPFS 网关的限制之内，ipfs-deploy 以一种非常简洁的方式将所有这些打包在一起。

这里是*我部署星际宇宙飞船的真实镜头*

```
ipd -p infura -p pinata -d cloudflare 
```

Enter fullscreen mode Exit fullscreen mode

这是上传结束后我看到的:

```
✔ 🙌 SUCCESS!
ℹ 🔄 Updated DNS TXT interplanetarygatsby.com to:
ℹ 🔗 dnslink=/ipfs/QmSNf4sScZUmpNqBWAAs9S5tC4XkQRNepA3KbF4aipGGeq 
```

Enter fullscreen mode Exit fullscreen mode

它让我每次都为它的毫不费力而微笑😌

不过，要实现这一目标，你需要采取一些一次性步骤:

1.  购买域名
2.  注册一个 Cloudflare 帐户
3.  将您的域的 DNS 区域移动到 Cloudflare
4.  将你的域名连接到他们的 IPFS 网关
5.  获取您的 [API 密钥](https://support.cloudflare.com/hc/en-us/articles/200167836-Where-do-I-find-my-CloudFlare-API-key-)

在实际执行这些配置步骤和等待 DNS 信息传播之间，整个过程可能需要几个小时。

这就是为什么我把那部分放在这额外的一章，而把基本指令留在零配置。随着时间的推移，我们会消除越来越多的摩擦，让第一次快乐的经历变得更加快乐😃⬅️🧹🥌

因此，在完成步骤 1-5 之后，您需要做的最后一件事是将您的域和 Cloudflare API 凭据添加到您网站的`.env`文件:

```
# dwebsite/.env
IPFS_DEPLOY_SITE_DOMAIN=example.com
IPFS_DEPLOY_CLOUDFLARE__API_KEY=paste-your-cloudflare-api-key-here
IPFS_DEPLOY_CLOUDFLARE__API_EMAIL=the-email-you-used-to-sign-up 
```

Enter fullscreen mode Exit fullscreen mode

现在继续，用`ipd -d cloudflare`开火，告诉所有的广告牌！📣📣📣

【西班牙语】

* * *

## 海报:呼吁卷发器🧹🥌

在写这篇指南的时候，我偶然发现了“冰壶石”表情符号，并立刻感觉到了它的存在。

[![wild sweeping](img/02baff89afd5fc5a8daf1268d1b2c0e6.png "wild sweeping")](https://res.cloudinary.com/practicaldev/image/fetch/s--4vqFB-a_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ue0j9gslx87p5mddzby7.gif)

老实说，我一直觉得有点卷曲的想法...奇怪。但是，整个团队运动致力于疯狂地消除路径上的摩擦，以便这个奇特的人工制品能够优雅地、毫不费力地滑向目标，这就是原因...搬家。

这就是我希望 [ipfs-deploy](https://github.com/agentofuser/ipfs-deploy) 带给用户的感觉。一种丝绸般光滑的体验，让他们不费吹灰之力就直接登陆到分布式网络上，一群狂热的观众为他们欢呼。

如果这听起来像是你喜欢的运动，那就在[期](https://github.com/agentofuser/ipfs-deploy/issues?q=is%3Aissue+is%3Aopen+label%3A)中打个招呼，让我们来润色一下吧！
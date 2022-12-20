# 使用 Redis 在 Python 应用程序中存储信息

> 原文：<https://dev.to/hackersandslackers/using-redis-to-store-information-in-python-applications-4j0k>

[![Using Redis to Store Information in Python Applications](img/99d5e04d867ee9e62b58957d0dfc30d8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uVwQSsZ0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res-2.cloudinary.com/hackers-and-slackers/image/upload/q_auto/img/redis.jpg)

我们在黑客和懒鬼这里进入了新的一年，在这个过程中我们有很多新的礼物可以玩。不要管圣诞老人如何设法在圣诞树下安装实际上不存在的 SaaS 产品。我们每年都要抽象的企业软件，这次正好得到一个小红盒。

如果你从未亲自使用过 Redis，这个名字可能听起来很熟悉，因为你在 Heroku marketplace 或你令人难以接受的书呆子 Twitter 帐户(我向你保证，我的更糟)等地方被晦涩的技术品牌轰炸过。你会问，Redis 是什么？这是 NoSQL 的数据等等，你在哪里...不要！不要离开！不是那样的，我发誓！

## Redis 是什么，什么时候用

Redis 以熟悉的键/值对格式存储信息，但术语“NoSQL”更多的是一个技术术语，而不是用例指标，与过去的 NoSQL 数据库同义。Redis 寻找它所服务的角色:一个装满垃圾的盒子，这些垃圾可能重要也可能不重要。这是放置星巴克礼品卡或你已经穿过但还没洗好的衣服的完美地方。

### 所有用户上天堂:用户会话云存储

也许最常见的用例是美化的**会话缓存**。类似于用户在 cookies 中存储临时应用程序信息的方式，Redis 保留了稍纵即逝的信息。不同的是，我们现在在自己的盒子里拥有这些信息，因此 Redis 的座右铭是:“*你的盒子，你的规则*”*

*这是我编的:它不包含任何事实。

因为临时用户信息掌握在我们手中，而不是变化无常的浏览器，所以我们可以决定我们的“缓存”有多临时，让它跨会话甚至设备持续存在。虽然本地内存存储也可能是存放一次性信息的地方，而数据库则是存放持久或永久信息的地方，但 Redis 介于两者之间。随着用户的交互和他们在我们的应用程序中创建的信息的发展，我们可以随时选择将存储在 Redis 中的信息提升到数据库中，或者让它保留一段时间。他们会很兴奋地看到他们的购物车里仍然装满了他们昨天喝醉时差点买的愚蠢的东西。

### 当变量在一个百吉饼上时，你可以随时拥有变量

换句话说，Redis 非常适合在每个用户的基础上解决整个应用程序中全局可访问变量的需求。当用户的临时信息在一个安全的全球环境中时，那些意外退出你的应用程序、转移到一个新的环境、或者只是使用你的应用程序的时间比你的 QA 团队长的用户更容易管理。相比之下，将用户的 Pac-Man 分数保存到一个全局变量中:当像 Pac-Man 这样的应用程序崩溃或重启时，该会话就永远消失了。因此，另一个属于排行榜的三个字母的应用程序淫秽死亡。

### 说起排行榜...

Redis 非常擅长递增计数。这可能是显而易见的事实，它是一台计算机，这些是计算机做的事情。另一件通过计数变得很棒的事情是:队列！任务提示、通知、聊天、消失的裸照等等:所有这些东西都非常适合我们的红盒子。

## 得到一个属于自己的红盒子

玩一个云托管的 Redis 盒子可能要花你 5 美元(如果你忘记取消的话，每月 5 美元)。Redis 是开源的，因此有大量的供应商可供选择，它们之间几乎没有区别。我会考虑推荐最能贿赂我的供应商，但与此同时，我会把逛街的事留给你。

设置 Redis 应该感觉像设置云 SQL 数据库，除了更小更可爱。你可以为你的盒子选择可爱的特性，比如托管区域等等。设置完成后，您应该有一个主机 URL 来访问您的实例:

```
redis-1738.c62.us-east-1-4.ec2.cloud.fakeredisprovider.com:42069 
```

现在我们用煤气做饭。

## 使用 redis-py Python 库

主 Python Redis 库的类型为`redis`，如`pip install Redis`所示。在任何情况下，最简单的连接方式都是通过 URI 连接字符串，如下所示:

```
r = redis.Redis( url='rediss://:password@hostname:port/0') 
```

请注意上面 URI 的独特结构:

*   **Redis://:**先于所有 Redis URIs；*注意结尾的冒号。*
*   接下来是密码，有趣的是可以绕过用户名。
*   **主机名**是实例的 URL...几乎总是一个几乎不加掩饰的重用 EC2 实例。没错，我们销售的是基于 AWS 的简单开源软件。别想了。
*   抢劫英国商船后，港口是你首选的停靠港。只是想确定你还在。
*   **/database** 带在最后面，就是你的数据库的名字。

与常规数据库一样，还存在其他连接方法，如通过 SSL 证书等。

### 存储和获取数值

这是你与 Redis 互动的面包和黄油:

*   **。set():** 通过覆盖或创建新值来设置键/值对
*   **。get():** 通过命名相关的键来检索一个值
*   **hmget():** 接受可变数量的键，如果存在，将返回每个键的值
*   **hmset():** 为一个键设置多个值。
*   **hgetall():** 获取一个键的所有值，其中一个键被分配了多个值。

值得注意的是，Redis 默认返回字节，而不是字符串。因此，记住值的编码/解码以便正确地检索它们是很重要的。例如:

```
# Setting a Value
r.set('uri', str(app.config['SQLALCHEMY_DATABASE_URI']).encode('utf-8'))

# Getting a Value
r.get('uri').decode('utf-8') 
```

如果你碰巧有点理智，你可能不想一遍又一遍地处理编码和解码值。幸运的是，在设置 Redis 实例时，我们可以通过将参数`decode_responses`设置为`True`来确保响应总是被解码:

```
redis.StrictRedis(host="localhost", port=6379, charset="utf-8", decode_responses=True) 
```

[**redis-py** 文档](https://redis-py.readthedocs.io/en/latest/)实际上比上面列出的 5 种方法更深入。如果你曾经设法涵盖了所有这些，我有很多关于你是什么类型的人的问题。

## 更多用于 Python 的 Redis 库

如果上面的编码/解码看起来很烦人，你不是第一个。这也是为什么有 [**Redisworks**](https://github.com/seperman/redisworks) 这样的库存在的原因。Redisworks 允许与 Redis 无缝交换 Python 数据类型。想把蟒蛇皮塞进你的盒子里吗？去吧！你甚至不需要去想它。有很多类似的图书馆都旨在让悲伤的生活变得更容易。

想要更多吗？Asyncio 自己的[异步 Redis 库](https://asyncio-redis.readthedocs.io/en/latest/)怎么样？或者类似的 **[aioredis](https://hackersandslackers.com/using-redis-with-python/aioredis.readthedocs.org)** 怎么样，另一个 Asyncio Redis 插件，里面也包括纯 Python 解析，集群支持，还有我都不懂的东西！Redis 的 Python 库确实比你需要的要多。

最后，我们怎么能忘记 **Flask-Redis** ？我们已经在[中介绍过这个](https://dev.to/hackersandslackers/globally-accessible-variables-in-flask-demystifying-the-application-context-1aeg-temp-slug-2115303)，但是它很容易成为任何 Flask 开发者使用的第一个也是最后一个 Redis 库。

## 你的盒子，你的宝藏，你的世界

 **既然我们已经发现了缓存数据和存储数据之间的这个缝隙，可能性是无限的。世界是你的牡蛎，里面装满了你可以选择或不选择塞进盒子里的东西。

好吧，好吧。也许这整个概念感觉有点晦涩难懂，几乎不值得在这一页的话。只要记住那种感觉，当你也需要一个红色小方块的时候，它会带着爱和同情等待着你。一个同伴立方体，如果你愿意的话。**
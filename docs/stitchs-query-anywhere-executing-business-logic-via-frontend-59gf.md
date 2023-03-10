# Stitch 的“随处查询”:通过前端执行业务逻辑

> 原文：<https://dev.to/hackersandslackers/stitchs-query-anywhere-executing-business-logic-via-frontend-59gf>

[![Stitch's “Query Anywhere”: Executing Business Logic via Frontend](img/24a616fa4b23596be0692379858f2387.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6kmxnw----/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/hackers-and-slackers/image/upload/q_auto:good/img/stitch5.jpg)

有些工具就是适合这项工作的工具。我想这一定是类似 JSON 的 NoSQL 数据库高峰时的想法，即使在今天也是如此。如果我们认为我们将把信息作为 JSON 传递给一个端点，然后把它组织成一个模式，几秒钟后，我们的请求又被迅速分解，如果你愿意，在某些情况下质疑模式的成本效益是合理的。这些案例中有很多涵盖了我们为自己开发的应用程序:这些程序让我们做一些愚蠢的事情，比如发垃圾自拍或者在互联网上充斥大量无意识的想法。

MongoDB Atlas 本身就是一个很棒的产品，它是一个云 NoSQL 数据库，能够执行类似于 SQL 连接的查询，有无数的聚合，有更多的可能性可以整合到一个管道中，我甚至没有时间去探索(我们会实现的)。如果您曾经有过为自己构建端点的任务，那么您很可能已经喜欢避开将 JSON 传递给 Lambda 函数或类似函数时带来的手动一对一键关联。

以我们在黑客和懒鬼方面的情况为例。我们正在运行一个 Ghost blog，这是一个由非营利组织构建的年轻软件:该软件不断更新和改进，这意味着如果我们想修改我们的 Node 应用程序的逻辑，我们的选择是:

1.  修改 Ghost 源并拒绝将来的更新
2.  如果有任何更新，将我们的自定义后端与 Ghost 更改合并
3.  使用 AWS 等平台构建第三方 API

**MongoDB Stitch** 给了我们新的第四个选择:*扩展我们的应用程序，不要重复那些样板文件。*我说*扩展*不仅仅是因为标题 clickbait 的阳具参考，还因为它让我们能够在之前对我们来说是黑箱作业的东西上进行构建，比如在博客系统上开发一个主题。

## 继承遗产

**MongoDB Stitch** 延续了避免重复的哲学。与 NoSQL 消除许多开发人员的痛点类似，Stitch 希望您继续做您最擅长的事情，可能是编写 NodeJS 应用程序。永远。

如果我为 Mongo 工作，我会这样销售产品:

> MongoDB Stitch 使您能够构建强大的功能，而无需切换到开发的琐碎方面。

我真正想说的是 **MongoDB Stitch** 是 **Google Firebase** 。这两款产品都瞄准了*前端*和*移动*开发者市场，并且都非常年轻，在完全实现这一目标方面处于早期。我第一次观看 MongoDB 产品视频，感觉我使用该产品的想法与他们的销售一致(我认为他们做得很好):

尽管那段视频热情而傲慢，Mongo 对他们的云还是相当害羞。我猜这和首次公开募股有关。

另一方面， **Google Firebase** 一直在为一个年轻的产品大声自吹自擂，其增长水平有时让人感觉几乎是不计后果的(我就不多说了):

反正我们不是来比较的。我们是来评判的。

## 新数据库入门

请随意跟随[设置一个免费的层集群](https://docs.mongodb.com/manual/tutorial/atlas-free-tier-setup/)。

正确的 Mongo 账户一旦创建就在 https://cloud.mongodb.com 进行管理。这个登陆 dash 包含了大量关于集群本身的有用信息和统计数据。在创建任何应用程序之前，我们还需要确保数据库的存在，否则我们将什么都不用做。

我*高度*建议使用 **MongoDB Compass** 桌面应用连接到你的集群。它易于下载，甚至通过连接复制+粘贴的 URI 节省了您输入凭据的时间:

<figure>[![Stitch's “Query Anywhere”: Executing Business Logic via Frontend](img/102f9baacb1c1a3a5f1b36dba2aa3b25.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uFZlNiAB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res-2.cloudinary.com/hackers-and-slackers/image/upload/q_auto/img/connectcompass.gif) 

<figcaption>连接你的数据库；情感上。</figcaption>

</figure>

在 Compass 中，只需创建一个数据库和集合:

<figure>[![Stitch's “Query Anywhere”: Executing Business Logic via Frontend](img/d72d843acad20ddb2d08ae4f0afce7ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KkainE9y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res-4.cloudinary.com/hackers-and-slackers/image/upload/q_auto/img/createdatabase_o.jpg) 

<figcaption>在 MongoWorld 中，“集合”相当于“表格”。</figcaption>

</figure>

## 我们去缝合吧

说完这些，回到你在 Mongo Cloud 上的账户。现在我们的兴趣完全在左侧导航的**缝合应用**链接上:

<figure>[![Stitch's “Query Anywhere”: Executing Business Logic via Frontend](img/2b77747ea43baecd9a638aae76cd5baf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GIIKlTF2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res-2.cloudinary.com/hackers-and-slackers/image/upload/q_auto/img/selectstitch.png) 

<figcaption>你以后有的是时间去探索。</figcaption>

</figure>

创建并命名一个新的 Stitch 应用程序，我们将进入“入门”页面。

<figure>[![Stitch's “Query Anywhere”: Executing Business Logic](img/86c67406fc02957e335f438a134fb7dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f_Jumy_x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res-5.cloudinary.com/hackers-and-slackers/image/upload/q_auto/img/cf61a09bd893c453854634988b71d500.gif) 

<figcaption>启用 anons &指向您的收藏</figcaption>

</figure>

一旦我们创建了我们的应用程序，Stitch 会立即向我们展示如何与我们的数据库进行交互的快速入门。我们要用史迪奇给我们的例子。重要的是要有“啊哈”的时刻，所有的东西都聚集在一起。

在开始任何代码之前，我们只需要做两件事:

1.  **启用匿名认证**:这是一种创建用户类型的奇特语言，任何访问我们应用程序的人都可以在其中进行查询
2.  指向我们的 Mongo 集合:我们需要一个地方来存储我们将要处理的数据。

### 连接您的应用程序

我们将把这段代码复制并粘贴到我们应用程序的页面上。一旦上线，请访问该页面并关注控制台:

```
<script src="https://s3.amazonaws.com/stitch-sdks/js/bundles/4.0.8/stitch.js"></script>

<script>
  const clientPromise = stitch.StitchClientFactory.create('hackerjira-bzmfe');
  clientPromise.then(client => {
    const db = client.service('mongodb', 'mongodb-atlas').db('HackersBlog');
    client.login().then(() =>
      db.collection('jira').updateOne({owner_id: client.authedId()}, {$set:{number:42}}, {upsert:true})
    ).then(()=>
      db.collection('jira').find({owner_id: client.authedId()}).limit(100).execute()
    ).then(docs => {
      console.log("Found docs", docs)
      console.log("[MongoDB Stitch] Connected to Stitch")
    }).catch(err => {
      console.error(err)
    });
  });
</script> 
```

在实时网站上查看如下:

<figure>[![Stitch's “Query Anywhere”: Executing Business Logic via Frontend](img/325a61b3d0cfb835e581c8af771b4e55.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--W0M-tepN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res-5.cloudinary.com/hackers-and-slackers/image/upload/q_auto/img/Screenshot-2018-06-02-16.40.43.png) 

<figcaption>注意右边控制台中的“docs”。</figcaption>

</figure>

成功了，但具体是什么呢？该代码片段告诉数据库做的第一件事是向上插入一行，其中“number”等于 42:

```
db.collection('jira').updateOne({owner_id: client.authedId()}, {$set:{number:42}}, {upsert:true}) 
```

出于理智，让我们检查一下数据库，看看发生了什么:

[![Stitch's “Query Anywhere”: Executing Business Logic via Frontend](img/d301c00a54c449ef9af16db16162fa5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ACZuq_2O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res-3.cloudinary.com/hackers-and-slackers/image/upload/q_auto/img/Screenshot-2018-06-02-16.43.31.png)

果然，在我们指定的集合中，一个新条目被添加到了我们的数据库中。这很有趣，但是我们的实际数据呢？这不正是我们来这里的目的吗？考虑下一行:

```
db.collection('jira').find({owner_id: client.authedId()}).limit(100).execute() 
```

啊，我们只根据当前用户创建的条目*进行查询！因为我们粘贴的示例代码创建了一条记录，所以我们可以在数据库中查询该用户创建的记录。让我们不要忽视这实际上没有做任何工作是多么酷:我们已经有了允许匿名用户创建记录并根据他们的会话识别它们的逻辑。*

`.find()`是我们获取记录的基础，很像 SQL `SELECT`。所以理论上，要显示这个集合中的所有问题，我们只需要运行下面的代码，对吗？

```
db.collection('jira').find({}).execute() 
```

慢点，伙计-但是是的，非常慢。我们只需要首先在 MongoDB Stitch 端公开读取权限。回到 Stitch UI，从边栏中选择“Rules”。在这里，我们可以修改谁可以从哪个数据库读取/写入哪个记录的规则:

<figure>[![Stitch's “Query Anywhere”: Executing Business Logic via Frontend](img/3f3022b1967d89e29b90c2d9189c03ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G5CjhdgU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res-4.cloudinary.com/hackers-and-slackers/image/upload/q_auto/img/Screen-Shot-2018-11-24-at-4.45.50-PM_o.png) 

<figcaption>它没有看起来那么复杂。</figcaption>

</figure>

我们可以创建尽可能高级的规则，但是我们现在需要的规则足够简单，可以完全通过 UI 来处理。

## 获取所有记录

继续将一堆记录添加到数据库集合中。尝试通过 JSON 或 CSV 导入数据，或者只是逐个添加一些记录。

完成后，回到你的应用程序，看看`.find({})`返回了什么:

<figure>[![Stitch's “Query Anywhere”: Executing Business Logic via Frontend](img/b0fac47deb405457c3364ea4708364de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NbnNdBt8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://hackersandslackers.com/conteimg/2018/06/Screenshot-2018-06-02-16.59.02.png) 

<figcaption>这才叫收藏。</figcaption>

</figure>

这就是:来自数据库的所有记录，前端只有一行代码。请花点时间思考一下:我们不需要创建 API、编写逻辑或登录任何糟糕的 IAM 策略管理 ui。我们甚至不需要编写查询；这种情况下的“查询”只是一个 JSON 对象。

## 把一切缝合在一起

当我第一次达到这一点时，我经历了一种情绪的冲击:创建新功能真的这么简单吗？如果是这样的话，我们在这一刻之前都在做些什么呢——重复同样的样板文件，重新学习和我们之前数百万人一样的概念？这些知识现在都不值钱了吗？史迪奇的存在是不是把我们生命中最伟大的成就简化成了可以在几分钟内复制的东西？

虽然使用 Stitch 可以轻松完成许多事情，但也有相当一部分令人头疼的事情随之而来。许多错综复杂的流程和用户管理完全缺乏文档或示例。创建一个基于易用性的云更令人沮丧:没有什么比知道一些事情应该是简单的，但缺少几行代码来完成它更糟糕的了。

那就是我们要填补空白的地方。下一次，我们将看看 Stitch 的无服务器功能。
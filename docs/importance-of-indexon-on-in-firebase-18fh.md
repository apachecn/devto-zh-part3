# “indexOn”在 Firebase 中的重要性

> 原文：<https://dev.to/robertjgabriel/importance-of-indexon-on-in-firebase-18fh>

Firebase 允许您使用任何公共子键对数据库中的一组节点进行特别查询。如果您事先知道您的索引是什么，您可以使用 Firebase 实时数据库规则，通过使用。indexOn”规则来提高性能。

## 我为什么要在乎？

随着您的 web 应用程序或网站规模的增长，其查询性能会下降。如果在将要查询的关键字上定义索引，这将有助于提高性能和减少使用的数据量。

Firebase 将在它们的服务器上使用这些键进行索引，改进您的查询和发送的数据量。否则，如果没有定义索引，排序将在客户端完成。

请看这个片段作为例子。我将用它来解释如何”。indexOn”与内置函数 orderByChild()一起使用。

[![alt text](img/a0e4c51f0ff19bdf89d84d6dda7b402c.png "Logo Title Text 1")](https://res.cloudinary.com/practicaldev/image/fetch/s--ZcLlCJyt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5ivgvvwi1nycgryleqij.png)

因此，对于这些数据，我将通过引擎大小和/或年份关键字进行查询。假设一个节点有一千个节点长，没有索引集。它会将所有数据返回给客户端。

汽车的名称已经是一个索引，所以 Firebase 已经优化了汽车名称查询。可以用”。indexOn”来告诉 Firebase 优化引擎大小和年份的查询。这将在返回之前在 Firebases 的服务器上完成。

[![alt text](img/d7a1e0ec0c9e3de1f20078872800d10d.png "Logo Title Text 1")](https://res.cloudinary.com/practicaldev/image/fetch/s--gLPQpW4A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/i1os0srcb2zbxt6rk8fm.png)

indexon 函数的重要性在于更好的性能和查询。所以下次你设计 Firebase 实时数据库的时候，即使是一个小的应用程序，也要花时间来定义你的索引。因为随着数据库的增长，您将会看到客户端性能的提高和瓶颈的减少。

如果您想在我发布新的博客帖子或编码项目时得到提醒。你可以在 [Github](https://www.github.com/robertjgabriel) 和 [Twitter](https://www.twitter.com/robertjgabriel) 关注我。

### 脚注

*   [https://firebase . Google . com/docs/database/security/indexing-data](https://firebase.google.com/docs/database/security/indexing-data)
*   [https://stack overflow . com/questions/33206932/how-to-write-indexon-for-dynamic-keys-in-firebase](https://stackoverflow.com/questions/33206932/how-to-write-indexon-for-dynamic-keys-in-firebase)
*   [https://stack overflow . com/questions/40837952/consider-add-indexon-for-firebase](https://stackoverflow.com/questions/40837952/consider-adding-indexon-for-firebase)
*   [https://stack overflow . com/questions/43260689/add-and-index on-value-into-firebase-rules](https://stackoverflow.com/questions/43260689/adding-and-indexon-value-into-firebase-rules)
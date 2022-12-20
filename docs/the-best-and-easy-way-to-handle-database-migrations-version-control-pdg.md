# 处理数据库迁移的最佳且简单的方法(版本控制)

> 原文：<https://dev.to/arojunior/the-best-and-easy-way-to-handle-database-migrations-version-control-pdg>

你知道数据库版本控制吗？这就是所谓的迁移，在这里，我将告诉你如何用一种简单的方式。

如果您了解它并使用 JavaScript，您可能已经尝试过这些工具中的一种来处理数据库迁移:Sequelize、TypeORM、Bookshelf 等等…但是，说真的…您对这些选项满意吗？我没有。

自从我开始用 Java 工作，遇到了漂亮的[fly way](https://flywaydb.org/)之后，我就缺少了一个像在其他语言中一样伟大的工具。现在好消息来了:有一个“dockerized”版本，你可以使用任何你想要的语言！

[![](img/6db147b555c2f8fd40a15f698a933d4a.png)](https://cdn-images-1.medium.com/max/400/1*i1CR1OLgeD_FWxkiMyRmnQ.png)

所以如果你相信我，继续阅读这个神奇的工具。

下面的例子是:[https://github.com/arojunior/tests-with-nodejs-graphql](https://github.com/arojunior/tests-with-nodejs-graphql)
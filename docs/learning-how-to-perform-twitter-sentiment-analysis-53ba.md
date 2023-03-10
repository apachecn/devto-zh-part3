# 学习如何执行 Twitter 情绪分析

> 原文：<https://dev.to/ugis22/learning-how-to-perform-twitter-sentiment-analysis-53ba>

[![](img/29d4ef1333cc174d0a5aa3e5ac5e32d3.png)](https://cdn-images-1.medium.com/max/1004/1*ex3gjAfvaV3Ub9Me7kYPXQ.png)

#### Keras 挑战复仇者联盟

情感分析，也称为意见挖掘，是自然语言处理中的一个有用工具，它允许我们识别、量化和研究主观信息。由于每天都会产生数十亿字节的数据，这种技术使我们有可能提取这些数据的属性，例如关于某个主题的负面或正面意见，以及关于正在谈论的主题和表达该意见的个人或实体的特征的信息。

Twitter 越来越受欢迎，如今，人们每天都在使用它来表达对不同主题的看法，如产品、电影、音乐、政治家、事件、社会事件等。每年都有很多电影上映，但如果你像我一样是漫威的粉丝，你可能会迫不及待地想最终观看新的《复仇者联盟》电影。就我个人而言，我想知道人们对此的感受。

在之前的中，我们已经讨论了如何使用 Twitter API 来传输推文，并将其存储在关系数据库中。现在，我们将使用这些信息来执行情感分析。但在此之前，我们应该考虑一些事情。首先，我们使用“复仇者”这个词来发布推文，但没有任何额外的考虑。很有可能我们有成千上万条重复的推文。就情感分析而言，处理它们不会增加任何额外的价值，相反，它将是计算上昂贵的。因此，我们需要访问数据库，删除重复的推文，保留第一个事件。第二，我们有一个未标记的数据库。为了让模型在训练期间学习，我们应该声明推文是正面的还是负面的。理想的解决方案是手动标注数据集，这非常准确，但需要大量时间。然而，有几个选择比如使用开源数据集标记工具，比如 [*斯坦福 CoreNLP*](https://stanfordnlp.github.io/CoreNLP/#human-languages-supported) *。*

有很多框架可以用于机器学习任务，但是，我们将使用 [***Keras***](https://keras.io/) ，因为它提供了一致而简单的 API，最大限度地减少了所需的用户操作数量，更重要的是，它易于学习和使用。我们还将使用 [***自然语言工具包***](https://www.nltk.org/) (NLTK)，它提供了许多语料库和词汇资源，这些资源对于标记、解析和语义推理非常有用，我们还将使用[***Scikit-learn***](https://scikit-learn.org/stable/)，它为数据挖掘和数据分析提供了有用的工具。

准备好开始了吗？让我们看看 Keras 能从《复仇者联盟》中学到什么。

首先，我们需要检索之前存储在 PostgreSQL 数据库中的 tweets。为此，我们将利用 [sqlalchemy](https://www.sqlalchemy.org/) ，这是一个 Python SQL 工具包和对象关系映射器，允许我们以一种简单的方式连接和查询数据库。sqlalchemy 的一个特点是包含了最常见数据库的方言(它用来与数据库通信的系统)实现，比如 MySQL、SQLite 和 PostgreSQL 等等。我们将使用 create_engine()函数，该函数基于给定的数据库 URL 生成一个引擎对象，其典型形式如下:方言+驱动程序://用户名:密码**@主机**:端口/数据库。在我们的例子中，方言是 PostgreSQL，而驱动程序是 psycopg2。创建引擎对象后，我们将使用函数 read_sql_query from pandas 模块来查询数据库，以获取存储在我们的 tweet 表中的所有数据(“select * from tweet_table”)并收集在 DataFrame 中检索的信息:
# 使用 F#和 Jupyter 笔记本进行数据分析

> 原文：<https://dev.to/samueleresca/data-analysis-using-f-and-jupyter-notebook-k97>

*最初发布于[https://samueleresca](https://samueleresca.net)T3】*

在最近一次在[@ justeatech](https://medium.com/just-eat-tech)的黑客马拉松中，我用 ML.NET 和[玩了很多机器学习。网芯。此外，一个. NET 开发者能够在不切换语言的情况下实现机器学习的想法很酷。ML.NET](https://dotnet.microsoft.com/apps/machinelearning-ai/ml-dotnet)的[仍然有很大的改进空间，但它可能是一个处理机器学习的强大框架。](https://dotnet.microsoft.com/apps/machinelearning-ai/ml-dotnet)

> ![Samuele Resca profile image](img/88e78b8b359283d25b7f4dfd6684ae34.png)Samuele Resca[@ samueleresca](https://dev.to/samueleresca)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)我在 [@MLdotnet](https://twitter.com/MLdotnet) 周围玩了很多，在 [@justeat_tech](https://twitter.com/justeat_tech) 目前的黑客马拉松期间，一个. NET dev 不用切换语言就能实现机器学习的想法很酷，但是有很大的改进空间，比如[github.com/dotnet/machine…](https://t.co/SbjpU2PdkV)
> [【machine learning】T22】](https://twitter.com/hashtag/MachineLearning)[【MLNET】](https://twitter.com/hashtag/MLNET)13:06PM-15 Mar 2019[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1106542236165132289)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1106542236165132289)2[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1106542236165132289)2

下面这篇文章主要讲述一些关于数据收集和数据分析的常识。此外，它解释了使用 F#和 Jupyter notebook 执行数据分析的一些基本工具。

## 数据基础的重要性

数据集收集是机器学习中更关键的一步。数据是所有后续流程的基础，也是 ML 工作流程的主要步骤。

因此，理解我们将要使用的数据并训练机器学习模型是至关重要的。因此，在开始之前建立原型和探索数据是很重要的。

## 歌词数据分析

下面这个例子的目的是给出一些关于数据分析过程的基本概念。作为一名软件工程师。NET 核心，我将使用围绕。网络生态系统。因此，该示例将使用 F#作为主要语言，并使用一些相关的库来处理数据。这个例子也可以在下面的 Github 资源库中找到:[https://github.com/samueleresca/LyricsClassifier](https://github.com/samueleresca/LyricsClassifier)

重要的是要考虑到以下步骤的大部分概念是独立于我们使用的语言或库的。此外，几乎所有的语言和开发框架都附带了一些用于机器学习和数据分析的开源工具。这里有一个完整的机器学习库和框架:[https://github.com/josephmisiti/awesome-machine-learning](https://github.com/josephmisiti/awesome-machine-learning)

更具体地说，下面的例子将使用下面的库:

*   `XPlot.Plotly` : XPlot 是一个跨平台的数据可视化库，支持使用 Google Charts 和 Plotly 创建图表。该库为构建图表和指定其属性提供了一种可组合的特定于领域的语言；
*   Math.NET 数值旨在为科学、工程和日常生活中的数值计算提供方法和算法。涵盖的主题包括特殊函数，线性代数，概率模型，随机数，插值，积分，回归，优化问题等；
*   F#数据库实现了你在 F#应用程序和脚本中访问数据所需的一切。它包含 F#类型的提供程序，用于处理结构化文件格式(CSV、HTML、JSON 和 XML)和访问 WorldBank 数据。它还包括解析 CSV、HTML 和 JSON 文件以及发送 HTTP 请求的助手；
*   ML.NET 是一个机器学习框架。NET 开发者；

### 数据模式

该示例将使用在 [Kaggle](https://www.kaggle.com/gyani95/380000-lyrics-from-metrolyrics) 上可用的歌词数据集。数据集包含不同流派和几位艺术家的歌曲列表。数据有一个简单的模式，可以用下面的 F#类型来表示:
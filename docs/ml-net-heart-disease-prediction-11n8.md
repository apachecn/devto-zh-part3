# ML。NET:心脏病预测

> 原文：<https://dev.to/dejavo/ml-net-heart-disease-prediction-11n8>

[![[Heart Attack Stroke Disease — Free image on Pixabay](https://pixabay.com/illustrations/heart-attack-stroke-heart-disease-3177360/)](img/4e09d8ab6c832434073bce500c455b86.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L6kC5NKx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2560/1%2ACEwJ9Ai7LEytGq-tPmMafg.jpeg)

 *原载于[中](https://medium.com/@dvirsegal/ml-net-heart-disease-prediction-6fd73a274eec)2019 年<time title="Thursday, April 11, 2019, 12:55:31 PM">4 月 11 日</time>* 

微软去年五月宣布了 ML.NET，并作为。有了机器学习的经验，我知道我必须尝试一下 Python 各种框架(比如 [scikit-learn](https://scikit-learn.org/stable/) )统治这个领域。

ML.NET 是一个免费的，跨平台的，开源的机器学习框架。NET 开发人员。预览版包括处理二元分类、多类分类和回归任务的学习者。添加了额外的 ML 任务，如推荐系统、聚类、异常检测、排名模型和深度学习架构。

在这篇博文中，我的目的是发挥 along 的功能，并最终展示其易用性。我的目标是基于一个开源数据集来预测给定患者的心脏病。

[![ML.NET logo by Wikimedia Commons](img/46f7e29dc408f26ccfae3a1f9b54c76c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OgeCdoAt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/0%2AYWGKpgPBnuFtBqSk.png)

#### 入门

假设您的系统上安装了 Visual Studio 2017，请打开一个新的控制台应用程序(。NET core)项目:

[![](img/72ee575eda402b06ffc7833d6f549a5a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qedaGwa5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Abn1lfUteirJ5NZETRDEVbQ.png)

然后，进入'**工具**菜单，选择'**管理解决方案的 NuGet 包…【T3]':**

[![](img/f9bc65e58ba650d0ca47a5c9384b5959.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hSRzG9Jh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A4kkhpH7IyVTS38j4XjwVyA.png)

在那里，浏览微软。ML’并选择最新的稳定版本(在写这几行的时候，稳定版本是 0.11.0)为当前解决方案安装。顺便说一下，如果您喜欢使用软件包管理器控制台，只需运行以下命令:

> 安装包微软。ML-版本 0.11.0

现在所有的先决条件都已经就绪，我们可以开始做一些机器学习魔术了。

#### 心脏病数据集

对于二元分类任务，我使用了来自 [Kaggle](https://www.kaggle.com/ronitf/heart-disease-uci) 数据集的心脏病 UCI 数据集；它包含 14 列和 303 条记录。

[![Heart Disease Dataset Columns (screenshot is taken from Kaggle)](img/2304a7be8d0cf486a09b531c3276e3f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JCbG0b3q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AnQblVebCZ_IlBm5xzcmAmA.png) 
心脏病数据集栏目(截图取自 Kaggle)

[**心脏病 UCI**T3】](https://www.kaggle.com/ronitf/heart-disease-uci)

我已经手动将 CSV 文件分成两个文件，一个较小的文件用于测试数据，一个较大的文件用于训练数据。下面，以测试数据 CSV 为例。

##### 测试数据 CSV
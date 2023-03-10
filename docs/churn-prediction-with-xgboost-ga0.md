# 使用 XGBoost 进行流失预测

> 原文：<https://dev.to/ivanriverajofre/churn-prediction-with-xgboost-ga0>

# 概述

我们被要求开发一个机器学习模型来预测客户是否会在不久的将来退出电信公司的订阅服务。我们试图实现的是被称为**流失预测**的概念，意思是预测未来哪些客户会停止使用公司的服务。

给定的输入是一个数据集，其中包含过去 12 个月中的活跃客户，每个客户占一行，列中有几个变量，包括人口统计数据和行为数据，最后一个值指示该客户在此期间是否已离开公司。

这是一个典型的**机器学习**问题，最好使用**逻辑回归**作为分类问题来解决，其中 sigmoid 函数(或逻辑函数)输出预测的条件概率。它输出一个介于 0 和 1 之间的数字。

# 技术分析

客户是智利的一家电信公司，拥有数百万客户。该数据集是从智利圣地亚哥所在地区*region Metropolitana*的 10 万名客户中提取的。包括的列有:

*   **Id** :内部客户识别号
*   **名称**:客户名称
*   **Sexo** :性别(女性或男性)
*   FNAC :出生日期
*   **学业**:达到最高教育水平
*   **Actividad** :当前经济活动的分类。
*   **Comuna** :客户住宅所在地(以及有线签约服务所在地)。由于是在智利，所以对应的行政区划叫做*【科穆纳】*。
*   **计划**:与公司签订的认购计划
*   **recl_3** :最近 3 个月收到的投诉数量
*   **recl_12** :过去 12 个月收到的投诉数量
*   **退出**:如果客户在过去 12 个月内退出，则为 1 或 0。

数据显示*流失率*为 18.2%，即。100.000 行中的 18.179 行在列*中有一个‘1’退出*。目标是使用该数据集作为机器学习程序中的训练数据来获得流失预测。

# 特征缩放和归一化

数据集中的每一行都有许多列，代表数据集中每个样本的特征。逻辑回归算法需要数字特征，所以我们必须给每个文本特征分配一个数字。此外，当要素具有相似的比例时，梯度下降等算法的工作速度更快，因此一个好的选择是在[0，1]之间归一化数据。让我们看一个来自数据集的样本(省略真实 ID 和姓氏):

[![](img/a4814ee54825be7f8d16a45b6e95a667.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SCxdOCub--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7k0weeq4rdg061d3wro5.png)

每个样品都有 10 根色谱柱。我们将使用最后一个， **exited** 作为训练过程的标签(也是为任何客户预测的数量)。我们决定在训练模型中去除前两个特征(客户的 ID 和姓名)，认为它们是不相关的。

大多数特性都处于文本模式，我们需要在运行任何模型之前对它们进行更改。这是一个不小的挑战，选择的方式是成功预测客户流失的关键因素。

**LabelEncoder**
一种选择是用从序列中获得的整数来映射文本值。SciKit Learn 预处理包中的 LabelEncoder 类会自动完成这项工作。
使用此代码:

```
from sklearn.preprocessing import LabelEncoder
encoder = LabelEncoder()
encoder.fit(dataset['comuna'])
dataset['comuna'] = encoder.transform(dataset['comuna']) 
```

这种转换会将列 **comuna** 更改为数字。例如，应用于从上面提取的相同数据集，我们得到:

[![](img/27907058a7d3fdb73d0e6f7e2cf67cab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9y228iFF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zh03luzu4zpymtn0vyhk.png)

埃尔·博斯克现在的值是 0，*拉·格兰哈*是 1，*兰帕*是 2，依此类推。如果我们对每个文本特征应用相同的转换(并且我们只考虑出生年份而不是整个日期)，我们得到的每一列都是一个数字:

[![](img/837c8aff4af0f2a38e1998adbcd3ddd4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8zMDtb-w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c19toazi0pypl66ru9nl.png)

但是，这种编码的问题是我们在数据中引入了任意(和随机)的顺序，因此模型将理解例如 *La Granja* 在 *El Bosque* 和 *Lampa* 之间，或者这些位置之间存在任何密切的关系。但这是错误的，因此引入了一种误导性的关系。

**OneHotEncoder**
另一种选择是使用 [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html) ，它将一个有 N 个可能值的列映射成 N 个值为 0 或 1 的向量。
例如，列 **Sexo** 包含 2 个可能的值[' *F* '，' *M* ']，因此 OneHotEncoder 会将其映射到两个二进制向量中。
使用此代码:

```
from sklearn.preprocessing import OneHotEncoder
from sklearn.compose import make_column_transformer
preprocess = make_column_transformer((OneHotEncoder(),['sexo']),remainder='passthrough')
dataset = preprocess.fit_transform(dataset) 
```

我们有:

[![](img/01095b29a836d6ccb82a0eee7cd2778d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--E9KB5UJU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1cfc7ix5ff3uo4wzh1r1.png)

列 **Sexo** 被替换为两列，分别代表两个可能的值。

对于当前的问题，将 OneHotEncoder 应用于每个文本列，最终的训练数据集将包含大约 87 列。

**定制订单**
我们可以应用另一种技术，利用每个特性都有一个来自已知领域的值这一事实，这意味着我们预先知道特定特性的所有可能值。例如， **Comuna** 的每个值都是*region Metropolitana*的 52 个可能的行政区划之一。你可以在维基百科[这里](https://es.wikipedia.org/wiki/Regi%C3%B3n_Metropolitana_de_Santiago#Divisi%C3%B3n_pol%C3%ADtico-administrativa_de_la_Regi%C3%B3n_Metropolitana_de_Santiago)找到它。

依此类推，对于每个特性:

*   **Sexo** : 2 个可能值
*   **研究** : 6 种可能的值
*   **Actividad** : 8 个可能值
*   **Comuna** : 52 个可能值
*   **计划** : 12 个可能值

对于这些特征中的每一个，我们可以对它们进行排序，提供它们之间的有用和真实的关系，这可以用于获得更好的预测。
例如， **Estudios** 栏代表客户所达到的最高教育水平。我们应该方便地从最低级别到最高级别排序，因为更高的级别可能意味着稳定的月收入。

**Actividad** 栏将从失业( *Cesante* )到有工作的职业(*professional Dependiente*)排序。

**计划**列将从最便宜到最贵排序。

Comuna 列可以按地理位置排序，但使用生活质量排名来排序也很有趣，这可能与当生活在具有最好的基础设施和更多工作的位置时，与订阅服务的入住概率更相关。幸运的是，这样的排名是存在的，并公布在 [ICVU 2018](http://www.cchc.cl/comunicaciones/noticias/indice-de-calidad-de-vida-urbana-icvu-2018)

因此，例如应用此代码(其中每个 **Comuna** 根据 ICVU 2018 排序):

```
glo_comuna=["Providencia","Las Condes","Vitacura","Lo Barnechea","San Miguel",
            "La Reina","Ñuñoa","Santiago","Macul","Maipú","La Florida",
            "Estación Central","San Joaquín","Quilicura","Peñalolén","Cerrillos",
            "La Granja","Quinta Normal","Lampa","Huechuraba","Independencia",
            "La Cisterna","Lo Prado","Pudahuel","Padre Hurtado","Puente Alto",
            "Conchalí","Renca","San Bernardo","Recoleta","Colina","Lo Espejo",
            "El Bosque","San Ramón","Melipilla","Cerro Navia","Buin",
            "Pedro Aguirre Cerda","Paine","Peñaflor","Talagante","La Pintana"]

data['comuna'] = data['comuna'].apply(lambda x: glo_columna.index(x)) 
```

我们得到了:
[![](img/867f4b1a3743b479dcc94de0598744b5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--78MAtax5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2um4se71msirt4umui7h.png)

每个 **Comuna** 都被映射到它在 ICVU 排名中的编号。

# 逻辑回归

我们将使用逻辑回归，使用[梯度推进](https://en.wikipedia.org/wiki/Gradient_boosting)算法，在 Python 中，使用 [XGBoost](https://xgboost.ai/)

逻辑回归将尝试将多维空间分为两部分。可能会离开公司的客户和不会离开公司的客户。使用 XGBoost，代码非常简单:

```
gbm = xgb.XGBClassifier(max_depth=16,
                        n_estimators=25,
                        learning_rate=0.01)
         .fit(train_x, train_y.values.ravel()) 
```

其中，train_x 是标准化数据集，train_y 包含**退出列**。

*   **最大深度**是基础学习者的最大树深度
*   n_estimators 是将适合多少棵树
*   **learning_rate** 是梯度下降的速度

你可以在这里了解更多信息

# 初步结果

测试数据集包含另外 1，000 个客户。我们将 XGBoost 分类器应用于该数据集，并得到以下结果:

*   **命中**:正确流失预测的百分比
*   **错过**:客户退出时错过的流失预测的百分比，但系统没有预测到。
*   **错误命中率**:客户没有退出时流失预测的百分比。

分类器结果是:

|  | 打击 | 小姐 | 错误点击 | 用于健身的时间 |
| --- | --- | --- | --- | --- |
| 标签编码器 | 74% (120/162) | 25% (42/162) | 6% (57/838) | 28 秒 |
| 定制订单 | 76% (124/162) | 23% (38/162) | 6% (51/838) | **25 秒** |
| OneHotEncoder | **93% (151/162)** | **6% (11/162)** | **1% (10/838)** | 96 秒 |

使用 OneHotEncoder 给出了 93%的流失预测精度，这是一个非常好的结果，但是有点慢。

# 多项式特征

这种回归尝试将一个线性函数拟合到数据集中，并使用逻辑函数计算其成本。但是对数据集更深入的分析可能会告诉我们，使用更高的多项式阶可能会更好。如果我们要拟合 8 个特征，而不是像这样拟合一个线性函数:

[![](img/d8708674df2369ba6138b6b33fad7fbd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WmXtmy5q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zenoecxm8ixkxhr0iogy.png)

我们可以尝试找到一个不同的公式，使用平方或立方项，间接增加特性的权重，并绑定与客户流失相关的概念。像这样:

[![](img/a3938d280b8d92fca72972b4f331af50.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OdS49S6u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p653vc0dkw4g8pq12uqp.png)

我们会说:

*   增加一个新功能: *Sexo* x *FNAC* 。().
*   增加一个新功能: *recl_3* x *recl_12* (一个很会抱怨的客户更有分量)。
*   添加新功能*Estudios*x*Actividad*()
*   考虑*计划*的平方，给下标的计划更多的权重。

这些新功能允许算法适应更多调整的数据集，这可以生成更好的预测。

# 最终结果

具有这些新特性的预测是:

|  | 打击 | 小姐 | 错误点击 | 用于健身的时间 |
| --- | --- | --- | --- | --- |
| 标签编码器 | 85% (138/162) | 14% (24/162) | 0% (8/838) | 25 秒 |
| 定制订单 | **91% (148/162)** | **8% (14/162)** | **0% (2/838)** | **24 秒** |
| OneHotEncoder | 25% (41/162) | 74% (121/162) | 1% (9/838) | 176 秒 |

现在我们有了一个足够快速和精确的工具来获得有价值的**流失预测**。使用我们的定制订单，该模型比 OneHotEncoder 返回更少的错误命中，并且速度更快。

代码可在[这里](https://github.com/ivanriverajofre/churn_prediction)获得。
谢谢你的时间。
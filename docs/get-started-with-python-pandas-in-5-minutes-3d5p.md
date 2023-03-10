# 5 分钟内开始使用 Python 熊猫

> 原文：<https://dev.to/bhavaniravi/get-started-with-python-pandas-in-5-minutes-3d5p>

### 5 分钟内开始接触熊猫

#### Python 熊猫库教程演练

[![](img/2cd3ec97d162db6834aaa684067d8763.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q8nsPYcX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AXhz01Vby2rg86xZj)

对于那些像我一样开始学习机器的人来说，可能会遇到熊猫，*数据分析库*。在急于理解 ML 的花招时，我们经常没有注意到这个库的重要性。但是很快您就会遇到一个障碍，在将数据输入 ML 模型之前，您需要处理数据、清理和执行数据转换。

已经有很多文档和教程了，为什么我们还需要这个博客？与大多数 python 库不同，Pandas 有一个陡峭的学习曲线。原因是您需要很好地理解您的数据，以便恰当地应用这些函数。从句法上学习熊猫不会让你有任何收获。熊猫的另一个问题是，做事情的方式不止一种。此外，当我开始研究熊猫时，它的广泛和复杂的文档是压倒性的。我查看了[备忘单](https://www.google.com/search?q=pandas+cheetsheets&oq=pandas+cheetsheets&aqs=chrome..69i57.3801j0j7&sourceid=chrome&ie=UTF-8)，这让我更加害怕。

在这篇博客中，我将通过破解特定的用例，带你了解熊猫的功能，你需要用给定的数据来实现这些功能。

### 设置和安装

在我们继续理解 Pandas 特性的代码之前，让我们在您的系统中安装 Pandas。我建议你创建一个虚拟环境，并在虚拟环境中安装熊猫。

#### 创建 virtualenv

```
virtualenv -p python3 venv
source venv/bin/activate 
```

#### 安装熊猫

```
pip install pandas 
```

#### Jupyter 笔记本电脑

如果你正在学习熊猫，我会建议你投入进去，使用 jupyter 笔记本。jupyter 笔记本中数据的可视化使人们更容易理解每一步发生了什么。

```
pip install jupyter
jupyter notebook 
```

默认情况下，Jupyter 运行在 python 的系统级安装中。为了在您的 virtualenv 中运行它，请点击链接并创建一个用户级内核[https://anba sile . github . io/programming/2017/06/25/jupyter-venv/](https://anbasile.github.io/programming/2017/06/25/jupyter-venv/)

### 样本数据

我创建了一个简单的采购订单数据。它包括一个公司的每个销售人员在 10 个国家及其在每个国家不同地区的分支机构的销售数据。这里有一个电子表格的链接供你下载。

[![](img/5b918ba37328add7849e1746f60f7f8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eTpIRAJn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/678/1%2AkA0O7fuDrvoE8K_zfvWaqw.png)

### 加载数据到熊猫

有了熊猫，我们可以从不同的来源加载数据。很少有从 CSV 或远程 URL 或数据库加载的。加载的数据存储在名为 DataFrame 的 Pandas 数据结构中。数据帧通常用变量名 df 来表示。所以，从现在开始，任何时候你看到 df，你都应该把它和 Dataframe 联系起来。

#### 来自 CSV 文件

```
import pandas
df = pandas.read\_csv("path\_to\_csv") 
```

#### 来自远程网址

您可以在 read_csv 中传递 CSV 文件的远程 URL。

```
import pandas
df = pandas.read\_csv("remote/url/path/pointing/to/csv") 
```

#### 来自 DB

为了从数据库中读取数据，将数据库中的数据读入 python 列表，并使用 DataFrame()创建一个

```
db = # Create DB connection object 
cur = db.cursor()
cur.execute("SELECT \* FROM \<TABLE\>")
**df = pd.DataFrame(cur.fetchall())** 
```

上面的每个代码片段都从一个数据源读取数据，并将其加载到 Pandas 的内部数据结构 DataFrame 中

### 了解数据

现在我们已经准备好了数据帧，让我们浏览它并理解它里面的内容

```
**# 1\. shows you a gist of the data**
df.head()

**# 2\. Some statistical information about your data**
df.describe()

**# 3\. List of columns headers**
df.columns.values 
```

[![](img/b4b1c85af4bb4e62a1e80352cfdb7c7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jRT9BJPX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/868/1%2AXJDBjuBY4LK-VyKq2HDNUw.png)

### 挑选&选择你的数据

现在，我们已经将数据加载到数据帧中，并理解了它的结构，让我们挑选并执行数据的可视化。在选择数据时，您可以使用两个索引或基于特定条件来选择。在这一节中，让我们逐一介绍这些方法。

#### 指标

索引是用来引用数据的标签。这些标签通常是您的列标题。例如，国家、地区、数量等。,

#### 选择列

```
**# 1\. Create a list of columns to be selected**
columns\_to\_be\_selected = ["Total", "Quantity", "Country"]

**# 2\. Use it as an index to the DataFrame**
df[columns\_to\_be\_selected]

**# 3\. Using loc method**  
df.loc[columns\_to\_be\_selected] 
```

[![](img/b436056154216855084773a9902546f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j5dO_t08--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/443/1%2AFes_TGhxHtR-4Xktq8-opw.png)

#### 选择行

与列不同，我们当前的数据框架没有可以用来引用行数据的标签。但是像数组一样，DataFrame 默认提供数字索引(0，1，2…)。

```
**# 1\. using numerical indexes - iloc**  
df.iloc[0:3, :]

**# 2\. using labels as index - loc**  
row\_index\_to\_select = [0, 1, 4, 5]
df.loc[row\_index\_to\_select] 
```

[![](img/0bd08ea948d6efc01eb0d8e11dcb21ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mOpnGW9Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/669/1%2AfaazKZaQGHnckMhM8XrG4A.png)

#### 过滤行

现在，在实时场景中，您很可能不希望基于索引选择行。现实生活中的一个实际需求是过滤出满足特定条件的行。对于我们的数据集，我们可以通过以下任意条件进行过滤

```
**1\. Total sales \> 200000**
df[df["Total"] \> 200000]

**2\. Total sales \> 200000 and in UK**
df[(df["Total"] \> 200000) & (df["Country"] == "UK")] 
```

### 玩弄枣子

大多数情况下，当处理日期字段时，我们不会按原样使用它们。熊猫让你很容易从它身上投射日期/月份/年份，并在其上执行操作

在我们的示例数据集中，Date_of_purchase 是 string 类型，因此第一步是将它们转换为 DateTime 类型。

```
\>\>\> type(df['Date of Purchase'].iloc[0])
**str** 
```

#### 将列转换为日期时间对象

```
\>\>\> df['Date of Purchase'] = pd.to\_datetime(df['Date of Purchase'])
\>\>\> type(df['Date of Purchase'].iloc[0])
**pandas.\_libs.tslibs.timestamps.Timestamp** 
```

**提取日期，月&年**

```
df['Date of Purchase'].dt.date **# 11-09-2018**
df['Date of Purchase'].dt.day **# 11**
df['Date of Purchase'].dt.month **# 09**
df['Date of Purchase'].dt.year **# 2018** 
```

### 分组

#### 统计操作

您可以执行统计操作，如最小值、最大值、平均值等。在数据帧的一列或多列上。

```
df["Total"].sum()
df[["Total", "Quantity"]].mean()
df[["Total", "Quantity"]].min()
df[["Total", "Quantity"]].max()
df[["Total", "Quantity"]].median()
df[["Total", "Quantity"]].mode() 
```

现在，在实际应用中，很少使用这些统计函数，通常您可能希望根据特定参数对数据进行分组，并得出数据的要点。

让我们看一个例子，在这个例子中，我们看到了国家、国家和地区的销售情况。

```
**# 1\. Country wise sales and Quantity**
df.groupby("Country").sum()

**# 2\. Quantity of sales over each country & Region**
df.groupby(["Country", "Region"])["Quantity"].sum()

**# 3\. More than one aggregation**
df.groupby(["Country", "Region"]).agg(
 {'Total':['sum', 'max'], 
 'Quantity':'mean'}) 
```

[![](img/f11ef89acca4e9fc80e6fc6e2e70521a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hD0cRJx3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/349/1%2Atib5Q6c4SzDbtGq_XnTt8A.png)

### 透视表

透视表是 groupby 的高级版本，可以在行和列上堆叠维度。也就是说，随着数据的增长，上面的分组将会越来越长，并且将变得难以获得洞察力，因此一种定义良好的查看方式将是数据透视表

```
import numpy as np
df.pivot\_table(index=["Country"], 
 columns=["Region"], 
 values=["Quantity"], 
 aggfunc=[np.sum]) 
```

[![](img/479492356a56c64c0684d444a5b96eb4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zZDZ512g--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/291/1%2AbHLja1_fsEsRUtMjOcg3iQ.png)

数据透视表的另一个优点是，您可以添加任意多的维度和功能。它还会为您计算一个总计值

```
import numpy as np
df.pivot\_table(index=["Country"], 
 columns=["Region","Requester"], 
 values=["Quantity"], 
 aggfunc=[np.sum], 
 **margins=True,  
 margins\_name="Grand Total"** ) 
```

[![](img/73a12958e8ed66180bce03004dc25278.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZpJb3EhJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/719/1%2AiRd4MKEk4hElhE3UXABq3g.png)

好吧，5 分钟内你说了这么多。花些时间尝试以上练习。在下一篇博客中，我将带你了解一些更深层次的概念和神奇的可视化效果，你可以用熊猫来创造它们。

每次你开始学习熊猫，很有可能你会迷失在熊猫术语中，比如索引、函数、数字等等。但是不要让这影响到你。你真正要明白的是，熊猫是一个可视化的工具，是一个深入了解你的数据的工具。

抱着这种心态，从你的电子表格中抽取一个样本数据集，并尝试从中获得一些见解。分享你所学到的。这里是我的 jupyter 笔记本的链接，供你开始使用。

博客有没有稍微推动一下，给熊猫一次机会？

*按住“拍手”图标，在* [*推特*](https://twitter.com/@bhavaniravi) *上对我喊一声。请继续关注未来的博客*

* * *
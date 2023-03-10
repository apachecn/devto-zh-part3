# 可视化股票

> 原文：<https://dev.to/mraza007/visualizing-stocks-28ee>

理解股票可能很难，但在这篇文章中，我们将借助`Plotly`(一个可视化库)来可视化股票。众所周知，Python 在处理数据方面非常强大。因此，我们将使用模块，如`Pandas`来处理我们的 CSV 文件和`Plotly`来可视化我们的数据。

#### 为了开始我们需要安装几个库

*   安装`Anaconda`分配。(这包含所有数据科学相关的包/模块)[安装链接](https://www.anaconda.com/download/#linux)。
*   安装`Plotly`(Anaconda 中没有这个)`pip install plotly`。

如果不想装`Anaconda`。您可以按照这些说明安装您需要的软件包。

*   装熊猫`pip install pandas`。
*   安装 Jupyter 笔记本`python3 -m pip install jupyter`。
*   安装`Plotly` `pip install plotly`。

## 让我们开始吧

*   首先，让我们通过运行下面的命令`jupyter-notebook`来启动 jupyter notebook。
*   现在我们有了运行的`jupyter-notebook`。我们将导入库。

```
import pandas as pd 
# importing Plotly import plotly.plotly
import plotly.graph_objs as go 
```

*   打开`Plotly`的离线模式

```
plotly.offline.init_notebook_mode(connected=True) 
```

*   让我们下载股票数据(可以在[雅虎财经](https://finance.yahoo.com/)搜索你最喜欢的股票上找到)。我会用 AAPL。

[![image](img/2a1cbf271c312acbbdf229a807b38014.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lxm0dbaE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i65.tinypic.com/2ps27md.png)

*   现在我们将使用熊猫来阅读股票 csv。

```
# read_csv allows us to read csv files data = pd.read_csv('AAPL.csv')
# few pandas operations. 
#This will display first few cells of the CSV files we can always specify how many cells we need by passing a number within those parenthesis data.head(10) data.head()

# This will tell how many rows and columns are there in our CSV file. data.shape

# This will describe our data by giving us information like mean, max, min, std and etc data.describe() 
```

*   使用`pandas`选择特定的列来绘制它们

```
# Selecting a specific column from csv file. Note brackets should contain exact name from the csv file. dates = data['Date']
high = data['High'] 
```

*   使用`Plotly`绘制数据

```
plotly.offline.iplot({
    "data": [go.Scatter(x=dates ,y=high)],
    "layout": go.Layout(title="AAPL Stocks")
}) 
```

##### 输出

[![Image](img/3f1adec83a78f812fca27596c60c269f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YNZrzssC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://i63.tinypic.com/2nl7jty.png)

现在你有了用折线图表示的股票数据。

我希望你喜欢这篇文章，如果你认为我错过了什么，欢迎在下面评论。

感觉在你的朋友和同事之间分享它。

## 注

这篇文章发表在我的博客上

##### 重要环节

*   [熊猫小抄](https://s3.amazonaws.com/assets.datacamp.com/blog_assets/PandasPythonForDataScience.pdf)
*   [Plotly Docs](https://plot.ly/python/getting-started/#jupyter-setup)
*   [代码](https://github.com/mraza007/stocks)
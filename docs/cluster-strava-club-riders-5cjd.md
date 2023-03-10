# 聚集斯特拉瓦俱乐部车手

> 原文：<https://dev.to/xbasf/cluster-strava-club-riders-5cjd>

你有没有想过你在你的斯特拉发俱乐部有多优秀？你想知道你朋友的俱乐部的水平吗？您想在您的 Strava 俱乐部中找到与您骑术表现相似的人吗？

我问了自己类似的问题，并决定调查 Strava API，并进行一个小分析，根据骑行表现将您的 Strava 俱乐部成员分成 *n* 组🚴‍♀️🚴‍ 🚴🏿‍🚴🏻‍♀️🚴🏼‍♀️ 🚴🏼‍♂️🚴🏽‍♂️🚴🏾‍♀️🚴🏾‍♂️🚴🏽‍♀️🚴🏿‍♀️🚴‍♂️🚴🏻‍♂️💨💨💨*这是多好的俱乐部之旅啊？*

在这里，我想把重点放在集群过程上，而不是 Strava API 的使用上，因为我认为后面的主题已经被广泛地讨论过了。如果你不熟悉 API，你可能想看看官方文档[这里](https://developers.strava.com/)

我们开始吧，好吗？

# 什么是社团活动？

感觉还得从这里说起:**定义**

对我来说，俱乐部活动，更确切地说是俱乐部骑行是一个典型的周日，你早早醒来，吃一顿美味的早餐，穿上莱卡，然后和俱乐部的伙计们一起出去玩几个小时。

原来这个定义在斯特拉发世界里是不被认同的。*留在这里陪我*。Strava 认为特定俱乐部的俱乐部活动是来自该俱乐部的所有用户的所有活动。说白了，如果你加入了一个俱乐部，你的所有活动都会被列为这个俱乐部的俱乐部活动。

# 计划

计划很简单- *他们说越简单越好* -首先也是最重要的是，我们要确保供应你最喜欢的热饮，在我这里是格雷伯爵茶🍵。依我看，这应该是尝试做任何光荣的事情之前的第一步。好了，继续..

我们的想法是尽可能多地检索感兴趣的俱乐部游乐设施的数据，进行一些数据清理，一旦我们对数据感到满意，我们就会兴奋起来，因为我们将准备好对俱乐部游乐设施进行聚类。

# 开裂

我的水壶已经开了，我的格雷伯爵马上就要准备好了 ☑️

是时候看看一些代码了:

```
import requests

ACCESS_TOKEN = 'your_access_token_here'
n_clubs = 30
endpoint = "https://www.strava.com/api/v3/athlete/clubs?&pagenobody =1&per_page={}&access_token={}"
r = requests.get(endpoint.format(n_clubs,ACCESS_TOKEN))
my_clubs = r.json() 
```

我认为计划中没有详细说明这一步🙃总之，基本上这是得到一份你参加的所有斯特拉瓦俱乐部的名单。在那里你应该能找到你的每一个俱乐部的钥匙 id。一旦你从列表中确定了你感兴趣的俱乐部，记下它的*id*——从现在开始我们将把它叫做*俱乐部 id* 。

请注意，这里需要访问令牌。如果你知道如何得到它，那对你来说是个好消息，如果不是，恐怕我不会在这里报道这个，抱歉，我相信其他比我沟通能力更好的人已经公布了得到你的方法。

现在，正如我们计划的那样，我们将使用 *club_id* 来检索我们允许的尽可能多的数据:

```
import pandas as pd

endpoint = "https://www.strava.com/api/v3/clubs/{}/activities?&page={}&per_page={}&access_token={}"
df = None
for ii in range(2):
    r = requests.get(endpoint.format(str(club_id),str(ii+1),'100',ACCESS_TOKEN))
    club_activity = r.json()
    df = pd.concat([df, pd.DataFrame(club_activity)])
df = df.reset_index(drop=True)
# Unpack the nested athlete dictionary into columns df = pd.concat([df, pd.DataFrame((d for idx, d in df['athlete'].iteritems()))], axis=1)
df.drop(['athlete','resource_state'],axis=1,inplace=True)
df['full_name'] = df.firstname + ' ' + df.lastname 
```

这将导致生成一个数据帧，其中包含感兴趣俱乐部的俱乐部活动的基本信息。

```
In[]: df.head()
Out[]: 
   distance  elapsed_time  moving_time             name 
0   55359.5          6709         6709   Afternoon Ride
1   23363.7          5911         5595   Afternoon Ride
2   28746.8          4961         4823   Afternoon Ride
3   64576.7         13551        10647   Afternoon Ride
4   24094.0          2712         2712     Morning Ride

   total_elevation_gain         type  workout_type        full_name(*)
0                 816.0         Ride          10.0          Sanglier
1                 427.0         Ride           NaN  Julius Pompilius
2                 724.0         Ride           NaN      Moralélastix
3                1343.7         Ride           NaN           Amnésix
4                 146.0  VirtualRide           NaN         Sténograf 
```

(*)出于隐私原因，我将显示[星号字符](https://www.asterix.com/les-personnages/)而不是真实姓名。

这里有一些注释，

*   谢天谢地单位似乎是 SI，*这是一个很好的接触！*🙌🙌
*   我们有两个特征来描述上面数据中的时间:`elapsed_time`应该包括休息，而`moving_time`应该是名称所描述的。如果是这样的话，我希望总有比移动时间更高的运行时间值。正如你所看到的，事实并非如此，是什么让我认为有些游乐设备没有开启自动暂停功能🤦‍♂️ *啊，加油伙计们！*
*   平均速度没有显示，所以我们用`df['speed_kph'] = df.distance/df.moving_time*3.6` *来计算。对不起，那些不自动暂停的人，他们的速度会降低*
*   似乎并不是每个人都要上路，斯特诺格拉夫在家里进行早期训练时非常舒服！

# 重新整理数据

我们希望有运动员的数据索引，我们可以做到这一点的一种方法是使用平均统计分组链的方法。

```
summary = df[df.type=='Ride'].groupby('full_name')['distance','total_elevation_gain','speed_kph'].mean() 
```

```
In[]: summary.head()
Out[]: 
                  distance  total_elevation_gain  speed_kph
full_name                                                                 
Abraracourcix      34325.1                 502.1       15.2
Absolumentexclus   50507.7                 796.8       23.7
Amnésix            48812.6                 981.7       21.5
Amonbofis          54889.8                1014.0       20.5
Aplusbégalix       92074.0                 956.0       27.5 
```

所以你看，这给了我们 3 个特征-距离，总仰角增益和速度-每个骑手。注意，我们通过过滤掉乘坐类型来忽略虚拟乘坐。接下来，我们将精确地使用这些数据来将骑手分组。

# 聚类

我的茶快没了..等一下，这部分不仅仅是茶。我觉得饼干也行😋

在处理机器学习算法时，一个好的做法是重新整理你的数据。在这种情况下，我们将使用最小最大缩放器对数据进行预处理。这将缩放所有要素，使其值落在给定的范围内，通常在 0 和 1 之间。然后，我们将使用这些值来填充 K-means 聚类算法。

```
from sklearn.preprocessing import minmax_scale
from sklearn.cluster import KMeans

X = minmax_scale(np.array(summary))
kmeans = KMeans(n_clusters=3, random_state=0).fit(X)
summary['cluster'] = kmeans.labels_ 
```

很快，不是吗？在匆忙下结论之前，让我们先看看结果。我想通过我们的 3 个特征来描绘运动员的表现，同时展示我们刚刚制作的组。

```
import matplotlib.pyplot as plt
import seaborn as sns

_= plt.figure()
_= plt.subplots_adjust(hspace=0,wspace=0)
_= plt.subplot(221)
_= sns.scatterplot(x=summary.distance/1000,y='total_elevation_gain',data=summary,hue=summary.cluster,legend=False)
_= plt.subplot(223)
_= sns.scatterplot(x=summary.distance/1000,y='speed_kph',data=summary,hue=summary.cluster,legend=False)
_= plt.subplot(224)
plt.yticks([])
_= sns.scatterplot(x='total_elevation_gain',y='speed_kph',data=summary,hue=summary.cluster,legend=False) 
```

[![alt text](img/059518da0d86ee2b726a75aecbe566f0.png "Clustering with no tuning")](https://res.cloudinary.com/practicaldev/image/fetch/s--SYIzDToQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hod4cxc6o5b06vawi5j3.png)

情节不错，但我并不完全满意。当然，我们已经成功地将车手分成了 3 组，或者说 3 个队。暂时不要喝香槟，好消息是我们的车手根据他们的距离进行了分组，但是仔细观察，一些车队在速度方面很不平衡😓。看下面的支线剧情——距离 vs 速度和总仰角增益 vs 速度——现在注意蓝队。他们的速度范围很大，记住这个速度是平均速度！！我个人不喜欢在蓝队，如果你是顶级车手，你除了等待其他车手什么也不做，如果你是最慢的车手..这是多么可怕的一场噩梦啊！！

我们需要第二次尝试。

我们希望每组的速度范围小一些，这样所有的车手都可以轻松跟上小组的速度。这意味着特征速度比其他更重要。你如何实现这个概念？关键在于尺度。按照最小最大缩放比例，我们将速度缩放 2 倍，其他功能保持不变。这将达到目的。

```
X_weighted = np.multiply(X, np.tile([1,1,2], (len(X), 1)))
kmeans = KMeans(n_clusters=3, random_state=0).fit(X_weighted)
summary['cluster2'] = kmeans.labels_ 
```

现在我们创建同样的图形:

```
_= plt.figure()
_= plt.subplots_adjust(hspace=0,wspace=0)
_= plt.subplot(221)
_= sns.scatterplot(x=summary.distance/1000,y='total_elevation_gain',data=summary,hue=summary.cluster2,legend=False)
_= plt.subplot(223)
_= sns.scatterplot(x=summary.distance/1000,y='speed_kph',data=summary,hue=summary.cluster2,legend=False)
_= plt.subplot(224)
plt.yticks([])
_= sns.scatterplot(x='total_elevation_gain',y='speed_kph',data=summary,hue=summary.cluster2,legend=False) 
```

[![alt text](img/63299b9023b8fb2d9410782b45fe0a18.png "Clustering with further tuning")](https://res.cloudinary.com/practicaldev/image/fetch/s--pwinLl5n--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xnhqg60uxdqau9n57w20.png)

现在看起来好多了，骑手们根据他们走过的距离，爬多高和骑多快来分组，确保组内平均速度的差距保持在较低水平。

这就是你要做的，如何聚集你的斯特拉发俱乐部骑行。该开香槟了🍾
# Folium:绝对初学者的强大绘图工具

> 原文：<https://dev.to/lberlin/folium-powerful-mapping-tool-for-absolute-beginners-1m5h>

作为一个数据科学的新手，刚刚开始探索我可以用大量数据和一点编程做些什么的可能性，有些部分非常令人满意，有些部分让我觉得有点力不从心。

有很多次，我开始去解决一些事情，尝试一些事情，或者写一些东西，却陷入了困境。可怕的，不可避免的，我甚至不知道从哪里开始。这是不可避免的，当我发现自己处于这种情况时，我正在建立一个坚实的资源工具箱。

[![Turtle spinning helplessly Gif, Image Source: Giphy](img/b929644bc85ce24bf2fb30df90c99019.png)](https://i.giphy.com/media/TITP4JehwSwquf64CW/giphy.gif)

但是另一方面也很棒。任何时候我开始做某件事，而它确实如我所愿，这是非常值得的。即使是一些基本的东西，我也喜欢突破障碍，找到自己创造的解决方案——这是我将自己定位为进入数据科学和技术职业的一个重要原因。

所以！当我想为一个项目绘制一个数据集，对我所知道的工具的笨拙感到沮丧，自学如何使用一个新的映射库，并制作一个传达我想要的东西的华丽地图时，我对自己非常满意。请允许我与其他 python 初学者分享一下 leav，因为它是一种可以随意使用映射技术的方式。它看起来也很光滑，默认是交互式的，让我觉得我毫不费力地提升了我的数据可视化。

* * *

### 开始之前

叶子确实有一些警告。我正在处理的数据集提供了西雅图附近金县的房屋销售数据。该数据集已经有纬度和经度列，这使得数据的映射非常自然。从我从[leav 文档](https://python-visualization.github.io/folium/)中收集的所有信息来看，你需要纬度/经度对来使用他们的地图，所以其他位置数据将在开始时提出数据清理挑战。

此外，leav 仅在某一点上工作良好——在我的系统上，它不会处理和映射我试图通过它传递的所有 21，000 行熊猫数据帧(公平)。在使用它之后，大约 1000 行似乎是我的最佳点，超过这个数就不会工作得那么好了。在我的数据集中，我将重点放在 1000 套最昂贵的房子上，以此缩小我的数据子集(并回答我正在进行的项目中的其他问题)，所以我将展示这个子集。

因此，通过瞄准具有可访问的位置数据的较小数据集，然后进入其中并四处玩耍，为自己的成功做好准备！

跟随我的代码:[https://github.com/lindseyberlin/Blog_FoliumMaps](https://github.com/lindseyberlin/Blog_FoliumMaps)

* * *

### 一切都在哪里？

也许有一些神奇的人可以看到一双长/宽的鞋，并知道它的确切位置，或者看到两对鞋，并知道它们之间的关系，但我不是这样的人。当我处理这些数据时，我有一些最初的问题。这些房子都在同几个街区内吗？它们真的散开了吗？有什么明显的集群吗？只有一个方法可以找到答案——让那些横/长的鞋为我工作！

[![Comparison map - matplotlib's basemap vs Folium](img/0ee321210481a7ea79086363059c3b2e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nQT7YVUY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dbx9c4ce1smogk42qd2o.jpeg)

我创建的介绍性叶子地图在地图背景上提供了一个散点图。代码中最复杂的部分是 for 循环，它将每一行映射为地图上自己的点。第二个最难的部分是在数据集上使用均值函数来获得纬度和经度列的平均值，这是我的地图的焦点。

这个基本配置回答了上面的问题——我可以清楚地看到每栋房子在哪里出售，以及每栋房子是如何隔开的。万岁！但是我最初的成功让我想知道我还可以用叶子做什么，所以我做了一些深入的研究。

* * *

### 添加层层含义

在基本地图的基础上，我采取了三个额外的步骤来使我的地图更加复杂，并确保它传达了更多的意义，而不仅仅是我的数据集中房屋的位置。

注意！这些只是截图——我用 python 创建的实际地图是交互式的。查看我上面链接的 github 回购协议，看看我的意思。

[![First layer: Folium map with pop-up text](img/6f8955247bd58cc83cfc9c3f64912add.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HOrycbRA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vhpim8869mdc9ec4txhw.jpeg)

首先，我添加了弹出文本，它显示了房子的准确纬度和经度以及售价。添加弹出文本有点复杂，但仍然很简单——我在 for 循环中添加了代码，因此它将创建特定于每行数据的弹出文本。我用。格式，但也可以使用 f 字符串。现在我的地图提供了更多的细节！

[![Second layer: Folium map where the radius size of circles reflects the price](img/bf669390b6b223fe09a13854c00863d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EUNgOCnw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d643kihmgzir8go0k48l.jpeg)

接下来，我改变了每个点的大小，以符合每所房屋的销售价格。这包括根据该行的价格改变每个点的半径。很简单！

[![Third layer: Folium map with circle colors reflecting price buckets](img/12880bfc042dd58363f347c5d63ba4cb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fTM-U4LJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ciy9v7knghos9vp0hu2w.jpeg)

最后，我改变了每个点的颜色，以对应不同的价格，因此最贵的房子显示为明亮、令人讨厌的粉红色，最便宜的房子(原始数据集中 1000 所最贵的房子)为淡绿色。这涉及到在 for 循环中根据那些成本桶集成 if/elif/else 语句，以改变每个点的颜色。到目前为止，比其他任何东西都稍微复杂一点，但是仍然很简单。

* * *

### 其他选项

[![Side quest: Folium heat map to reflect concentration of homes](img/2bcd02efc6dc0afa72aeea4dfa38f9a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gpS9ZuX3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mkv9jyc6jqqdcifvswzd.jpeg)

检查此类数据的另一个简单方法是添加一个热图，而不是在图上添加点。这以不同的方式传达了数据的集中程度，如果您想了解集中程度而不是数据集的每一行的详细信息，这将非常有用。

你可以用很多其他的方式在叶子中绘制地图，可能还有更好的方式来回答我最初提出的问题。玩玩它，看看你会发现什么！如果你有任何有用的提示或技巧，或者最好用地图回答的问题，请分享！

* * *

### 有用教程:

[用于地图、热图和时间分析的树叶](https://www.kaggle.com/daveianhickey/how-to-folium-for-maps-heatmaps-time-analysis)

[用叶子创建交互式犯罪地图](https://blog.dominodatalab.com/creating-interactive-crime-maps-with-folium/)

###### *封面图片来自[用](https://towardsdatascience.com/data-101s-spatial-visualizations-and-analysis-in-python-with-folium-39730da2adf)用 Python 进行空间可视化和分析。旋转乌龟 GIF 来自 [GIPHY](https://gph.is/2xQV4nW) 。所有其他的图片都是我用 lyum 创建的截图——见我的 GitHub 库[这里](https://github.com/lindseyberlin/Blog_FoliumMaps)*
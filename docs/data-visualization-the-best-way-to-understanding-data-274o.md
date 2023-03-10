# 数据可视化:理解数据的最佳方式

> 原文：<https://dev.to/teosoft7/data-visualization-the-best-way-to-understanding-data-274o>

要分析数据，首先需要了解数据的特性。但是读取数据并不是一件容易的事情。电子表格应用程序可以帮助我们有效地读取表格或工作表中的数据，但是如果数据很大，我们可能会遇到一些困难。例如，以下是工资( [Github link](https://github.com/dsnair/ISLR/blob/master/data/csv/Wage.csv) )数据，其中包含美国大西洋中部地区男性的收入调查信息，是《统计学习导论》一书的一部分。它有 3，000 行和 12 列，包含按年份、年龄、地区等分类的工资数据汇总。

我们可以用下面的熊猫来查一下，

`df = pd.read_csv('Wage.csv')
df.head()`

[![DataFrame](img/e91742a209e3240a82074a5b33c10c1f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wVk7TENy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bme2k08qisf8y5n1r0iq.png)

而我们可以从下面的 python 语句中看到数据的基本统计值，

`df.describe()`

[![Describe](img/825851593ab57c1fc49939f0e03ba19a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--48FCt_V8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d8jr39y4n4jjk95zagc3.png)

我们可以用表格从数据中获得一些信息，但是它不能给出对数据的清晰理解。因为表格和文字并没有告诉我们数据的故事。

但是通过下面的一些图片，我们可以一目了然地看到工资随年龄、年龄和教育水平的变化。

[![Chart Example](img/c79ee11f3714f69737fc4d85457a3f3e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5NR_2UZh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/atnaan6z32pqixs6vajs.png)

正如我们在上面看到的，通过图表可视化来阅读数据变得更加容易。我们可以同时通过数据获得大量信息。如今，有很多方便的工具可以从数据中绘制图表。Microsoft Excel 和 Tableau 是一个著名的强大的应用程序，用于组织数据和绘图，最适合大多数商业案例。还有支持各种图表样式的在线工具，如 Google Chart、在线图表工具等等。通过使用这些图表工具，我们可以很容易地得到各种图表。

[![Various Chart](img/b34c57601030122c88142295122ad700.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0qeTO6A_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jvk5hivxhu16fcixj2qp.png)

所有的工具都非常方便，但是仍然有一些限制。我们需要为他们遵循他们的规则和方法。

但是通过用 python 写代码，我们可以更自由地绘制图表。Python 的最大优势之一是，它有大量的图表库，我们可以获得各种可视化形式的数据。我们可以用 matplot，plotly，seaborn 制作一个通用的图表，我们还可以用 folium，basemap 在地图上绘制一些地块，还有一堆更多的图表库让我们的生活变得更加轻松。

[![Sample-Chart-01](img/7c1d69b47f62f3975d078ca7c444f404.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sIgKmuym--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/20kbim4alows1qr2tm3d.jpg)

[![Sample-Chart-02](img/85f27ebbf61782c12547b9cfb4bfe6a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dP1sBGnl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xbluhyl990aoszhn70z4.jpg)

大多数数据都可以用图表来表示，但是文本数据呢？有几种方法可以将文本和图表可视化。但是最有趣的方法叫做*【字云】*，用文字表达图表的那种。单词云是由单词组合而成的图像，看起来像一个云状。一个单词的大小显示了它的重要性，例如，它在文本中出现的频率——它的频率。这里举一个*字云*的例子。
[![Word Cloud Sample - 1](img/44117f07e39fd27fab159d53389b691c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ve-L7qgk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x8k16u0fly11yelq6bw1.png) 
*文字云*通常用于轻松制作大型文档(报告、演讲)的摘要，创建主题艺术(礼物、展示)或可视化数据(表格、调查)。但是生成单词云有点困难，你应该统计一些文本中的单词并用美术工具如 Photoshop 或 Illustrator 绘制出来。但是有了 *wordcloud* Python 库，超级方便。你可以在几行代码内创建一个单词云。【T14
`texts = 'Dog Cat Animal Carmel Snake'
wordcloud = WordCloud()
wordcloud = wordcloud.generate_from_text(texts)
plt.figure(figsize = (8, 8), facecolor = None)
plt.imshow(wordcloud, interpolation="bilinear")
plt.axis("off")
plt.tight_layout(pad = 4)
plt.show()`

[![image](img/1d1f33152fc0e27d65ca347f1d1ac25b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--smUTYdby--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gjc1n47936rbkz0jkvep.png)

*尝试用 Python 代码做一个有创意的词云！！！*
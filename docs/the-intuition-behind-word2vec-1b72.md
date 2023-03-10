# Word2Vec 背后的直觉

> 原文：<https://dev.to/pmuens/the-intuition-behind-word2vec-1b72>

[![The intuition behind Word2Vec](img/855f57a703d94c29128d3f8439e304f5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JaLGDF3h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://philippmuens.com/conteimg/2020/01/antoine-dautry-_zsL306fDck-unsplash.jpg)

你有没有想过 YouTube 是如何知道推荐哪些视频的，谷歌翻译是如何将整篇文章翻译成像样的目标语言版本的，或者你的智能手机键盘是如何在你键入文本时知道建议哪些单词和文本片段的？

所谓的[嵌入](https://en.wikipedia.org/wiki/Word_embedding)很有可能是在幕后使用的。嵌入是现代自然语言处理模型背后的中心思想之一。

在下面的文章中，我们将发现嵌入背后的主要构件和基本直觉。我们将了解它们如何工作以及为什么工作，以及如何使用一种将单词转化为向量的方法来展示:

[国王-男人+女人=王后]

我们将在这里编写的所有代码都可以在 GitHub 上我的[“Lab”](https://github.com/pmuens/lab)存储库中找到。阅读本教程时，请随意编码。

## 基本设置

在直接进入代码之前，我们需要确保我们将使用的所有 Python 包都安装在我们的机器上。

我们安装了 [Seaborn](https://seaborn.pydata.org/) ，这是一个可视化工具，可以帮助我们绘制好看的图表。我们并不直接与 Seaborn 合作，而是将它的风格与 [Matplotlib](https://matplotlib.org/) 结合使用，让我们的剧情看起来更“现代”。

```
!pip install seaborn 
```

接下来，我们需要导入我们将在整个教程中使用的模块(最后几行将 Matplotlib 配置为使用 Seaborn 样式)。

```
import json
from pathlib import Path

import pandas as pd
import seaborn as sns
import numpy as np
from IPython.display import HTML, display

# prettier Matplotlib plots
import matplotlib.pyplot as plt
import matplotlib.style as style
style.use('seaborn') 
```

因为我们要处理不同的数据集，所以我们应该创建一个单独的目录来存储它们。

```
!mkdir -p data
data_dir = Path('data') 
```

## 比较国家

让我们从第一个数据分析任务开始。我们的目标是比较和对比不同国家的表面积和人口。主要的想法是，我们希望根据这两个指标来分析哪些国家非常相似，哪些国家非常不同。

我们将使用的数据集是 [@samayo](https://github.com/samayo) 的 [`country-json`项目](https://github.com/samayo/country-json)的一部分。确保花一些时间浏览不同的 JSON 文件，以了解数据的结构。

在我们的例子中，我们只对 [`country-by-surface-area.json`](https://github.com/samayo/country-json/blob/master/src/country-by-surface-area.json) 和 [`country-by-population.json`](https://github.com/samayo/country-json/blob/master/src/country-by-population.json) 文件感兴趣。让我们继续把文件下载到我们的`data`目录。

之后，我们可以定义两个变量，它们将指向我们文件系统中的文件。

```
SURFACE_AREA_FILE_NAME = 'country-by-surface-area.json'
POPULATION_FILE_NAME = 'country-by-population.json'

!wget -nc https://raw.githubusercontent.com/samayo/country-json/master/src/country-by-surface-area.json -O data/country-by-surface-area.json
!wget -nc https://raw.githubusercontent.com/samayo/country-json/master/src/country-by-population.json -O data/country-by-population.json

surface_area_file_path = str(data_dir / SURFACE_AREA_FILE_NAME)
population_file_path = str(data_dir / POPULATION_FILE_NAME) 
```

在我们的数据分析过程中，我们将利用 [Pandas](https://pandas.pydata.org/) ，这是一个伟大的 Python 库，它使检查和操作数据变得非常简单。

由于我们的数据是 JSON 格式的，我们可以使用 Pandas [`read_json`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_json.html) 函数将数据加载到所谓的 [DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) (可以把它想象成一个 Excel 电子表格)。

[`dropna`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.dropna.html) 函数确保我们删除所有未定义的条目，因此对进一步检查没有用处。

```
df_surface_area = pd.read_json(surface_area_file_path)
df_population = pd.read_json(population_file_path)

df_population.dropna(inplace=True)
df_surface_area.dropna(inplace=True) 
```

你可能已经注意到，如果我们想根据两个指标来比较不同的国家，处理两个不同的文件会很麻烦。

由于两个文件包含相同名称的相同国家，只是在数据`area`和`population`上有所不同，我们可以使用 [`merge`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.merge.html) 创建一个新的数据框架，包含所有国家及其各自的`area`和`population`号。

我们在这里执行的另一个调整是将`index`设置为国家名称。通过这种方式，我们可以轻松地根据国家名称查询国家数据，而不必处理无意义的整数值。

```
df = pd.merge(df_surface_area, df_population, on='country')
df.set_index('country', inplace=True)
df.head() 
```

[![The intuition behind Word2Vec](img/b441a7eddab485781b5f6f6631756bbe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zoa3pW9r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://philippmuens.com/conteimg/2020/01/Bildschirmfoto-2020-01-13-um-19.28.09.png)T3】

```
len(df)

227 
```

如你所见，我们的数据框架中共有 227 个国家。227 个国家对我们来说太多了。尤其是因为我们将在下一步绘制数据。

让我们通过使用`area`和`population`数据执行一些范围查询来减少我们的结果集。

```
df = df[
    (df['area'] > 100000) & (df['area'] < 600000) &
    (df['population'] > 35000000) & (df['population'] < 100000000)
]
len(df)

12 
```

太好了！12 个国家一旦标绘出来就更容易分析了。

说到这里，我们来做一个我们 12 个国家的 2D [散点图](https://en.wikipedia.org/wiki/Scatter_plot)。我们决定在 X 轴上绘制`area`,在 Y 轴上绘制`population`。

```
fig, ax = plt.subplots()
df.plot(x='area', y='population', figsize=(10, 10), kind='scatter', ax=ax)

for k, v in df.iterrows():
    ax.annotate(k, v)

fig.canvas.draw() 
```

[![The intuition behind Word2Vec](img/a50fd8fe1d180532d34d4819e2292fe8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UJwEkx7C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://philippmuens.com/conteimg/2020/01/countries-scatterplot.png)

查看绘制的数据，我们可以立即看到一些关系。与其面积相比，越南的人口似乎很多。另一方面，肯尼亚的面积很大，但与其国土面积相比，人口较少。

像这样绘制数据有助于我们以直观的方式进行推理。除此之外，我们还可以轻松验证数据的完整性。

虽然我们作为人类可以通过查看我们的图表立即说出我们国家数据中的关系，但有必要将我们的视觉推理转化为原始数据，以便我们的计算机也可以理解它们。

再次查看该图，似乎国家数据点之间的距离是确定这些国家“相似”或“不同”程度的一个很好的衡量标准。

有几种算法可以计算两个(或多个)坐标之间的距离。欧几里德距离是一个非常普通的公式。这是数学符号:

[d(x，y) = d(y，x)= \ sqrt { \ sum _ { I = 1 }<sup>N</sup>(x _ I-y _ I)<sup>2 }</sup>]

虽然这个公式一开始看起来有点吓人，但是把它转化成代码还是很简单的。

```
def euclidean_distance(x, y):
    x1, x2 = x
    y1, y2 = y
    result = np.sqrt((x1 - x2) **2 + (y1 - y2)** 2)
    # we'll cast the result into an int which makes it easier to compare
    return int(round(result, 0)) 
```

根据我们的计划，泰国和乌干达似乎是两个非常不同的国家。计算两者之间的欧几里德距离证实了我们的预感。

```
# Uganda <--> Thailand
uganda = df.loc['Uganda']
thailand = df.loc['Thailand']

x = (uganda['area'], thailand['area'])
y = (uganda['population'], thailand['population'])

euclidean_distance(x, y)

26175969 
```

如果我们将这个结果与伊拉克和摩洛哥之间的欧几里德距离进行比较，我们可以看到，这些国家似乎更“相似”。

```
# Iraq <--> Morocco
iraq = df.loc['Iraq']
morocco = df.loc['Morocco']

x = (iraq['area'], morocco['area'])
y = (iraq['population'], morocco['population'])

euclidean_distance(x, y)

2535051 
```

虽然这个练习非常简单和直观(如果你对地理很熟悉的话),但是它也向我们介绍了嵌入的基本概念。通过嵌入，我们将数据(如单词或原始数字)映射到多维空间，并使用数学来处理和计算数据之间的关系。

这听起来可能相当抽象，我同意我们国家的数据分析和嵌入之间的关系仍然有点模糊。

相信我，接下来的例子肯定会带来一个“啊哈时刻”,我们到目前为止学到的东西会突然变得灵光一现！

## 颜色数学

现在我们已经看到了嵌入的一些基本原理，让我们再看一个稍微复杂一点的例子。这一次，我们将使用不同的颜色，并将它们表示为红、绿、蓝值的组合(也称为 RGB)。

在我们开始分析之前，我们将定义一个辅助函数，让我们根据颜色的 RGB 表示来渲染颜色。

下面的代码定义了一个函数，该函数采用红、绿和蓝的整数值(取值范围为 0 - 255 ),并以给定的颜色作为背景呈现一个 HTML 文档。

```
def render_color(r, g, b):
    display(HTML('''
      <div style="background-color: rgba(%d, %d, %d, 1); height: 100px;"></div>
    ''' % (r, g, b)),
    metadata=dict(isolated=True)) 
```

黑色表示为红色、绿色和蓝色。让我们验证一下我们的`render_color`函数是否如预期的那样工作。

```
render_color(0, 0, 0) 
```

[![The intuition behind Word2Vec](img/96b154174245e17c2410ab2de5082c71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8RgHHN5c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://philippmuens.com/conteimg/2020/01/Bildschirmfoto-2020-01-13-um-19.31.47.png)

太好了。有用！

接下来，是时候下载我们将用于颜色分析的数据集了。我们已经决定使用 [@jonasjacek](https://github.com/jonasjacek) 的 [256 色](https://jonasjacek.github.io/colors/)数据集。它列出了广泛使用的终端模拟器 [xterm](https://en.wikipedia.org/wiki/Xterm) 使用的 256 种颜色。确保花几分钟时间熟悉数据及其结构。

下载数据集的步骤与我们在本教程开始时下载国家数据的步骤相同。

```
COLORS_256_FILE_NAME = 'colors-256.json'

!wget -nc https://jonasjacek.github.io/colors/data.json -O data/colors-256.json

colors_256_file_path = str(data_dir / COLORS_256_FILE_NAME) 
```

既然我们可以在编程环境中访问数据，那么是时候检查结构并考虑进一步处理它的方法了。

```
color_data = json.loads(open(colors_256_file_path, 'r').read())
color_data[:5]

[{'colorId': 0,
  'hexString': '#000000',
  'rgb': {'r': 0, 'g': 0, 'b': 0},
  'hsl': {'h': 0, 's': 0, 'l': 0},
  'name': 'Black'},
 {'colorId': 1,
  'hexString': '#800000',
  'rgb': {'r': 128, 'g': 0, 'b': 0},
  'hsl': {'h': 0, 's': 100, 'l': 25},
  'name': 'Maroon'},
 {'colorId': 2,
  'hexString': '#008000',
  'rgb': {'r': 0, 'g': 128, 'b': 0},
  'hsl': {'h': 120, 's': 100, 'l': 25},
  'name': 'Green'},
 {'colorId': 3,
  'hexString': '#808000',
  'rgb': {'r': 128, 'g': 128, 'b': 0},
  'hsl': {'h': 60, 's': 100, 'l': 25},
  'name': 'Olive'},
 {'colorId': 4,
  'hexString': '#000080',
  'rgb': {'r': 0, 'g': 0, 'b': 128},
  'hsl': {'h': 240, 's': 100, 'l': 25},
  'name': 'Navy'}] 
```

正如我们所看到的，该数据集中有 3 种不同的颜色表示。有一个十六进制，一个 HSL(色调，饱和度，亮度)和一个 RGB(红色，绿色，蓝色)表示。此外，我们可以通过`name`属性访问颜色的名称。

在我们的分析中，我们只对每种颜色的名称和 RGB 值感兴趣。假设我们可以创建一个简单的字典，它的关键字是小写的颜色名称，它的值是一个分别包含红色、绿色和蓝色值的元组。

```
colors = dict()

for color in color_data:
    name = color['name'].lower()
    r = color['rgb']['r']
    g = color['rgb']['g']
    b = color['rgb']['b']
    rgb = tuple([r, g, b])
    colors[name] = rgb 
```

为了验证我们的数据结构以我们上面描述的方式工作，我们可以打印出一些带有 RGB 值的样本颜色。

```
print('Black: %s' % (colors['black'],))
print('White: %s' % (colors['white'],))

print()

print('Red: %s' % (colors['red'],))
print('Lime: %s' % (colors['lime'],))
print('Blue: %s' % (colors['blue'],))

Black: (0, 0, 0)
White: (255, 255, 255)

Red: (255, 0, 0)
Lime: (0, 255, 0)
Blue: (0, 0, 255) 
```

虽然我们的字典是一个很好的起点，但如果数据存储在熊猫[数据框架](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html)中，对数据进行计算通常会更容易，有时会更快。 [`from_dict`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.from_dict.html) 函数帮助我们将一个简单的 Python 字典变成一个数据帧。

```
df = pd.DataFrame.from_dict(colors, orient='index', columns=['r', 'g', 'b'])
df.head() 
```

[![The intuition behind Word2Vec](img/a31dcd1359859d97e4d229c1a9932c89.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9p5qAgkI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://philippmuens.com/conteimg/2020/01/Bildschirmfoto-2020-01-13-um-19.33.34.png)

看到以这种方式格式化的数据，我们可以认为它的表示是红色、绿色和蓝色值到三维空间的映射，其中例如红色是 X 轴，绿色是 Y 轴，蓝色是 Z 轴。

您可能还记得，我们在上面的国家示例中使用了[欧几里德距离](https://en.wikipedia.org/wiki/Euclidean_distance)来确定国家之间的“相似”程度。主要思想是，与数据点相距较远的不同国家相比，相似国家的数据点之间的距离较小。

另一个非常有用的计算数据点相似度的公式是所谓的[余弦相似度](https://en.wikipedia.org/wiki/Cosine_similarity)。余弦相似性度量多维空间中两个向量之间的角度。角度越小，底层数据越相似。

将此转化为我们的颜色示例，我们可以认为每种颜色都被表示为具有 3 个值(红色、绿色和蓝色)的向量，这 3 个值(如上所述)可以映射到 3D 坐标系中的 X、Y 和 Z 轴。利用余弦相似性，我们可以选取其中一个向量，计算它与其余向量之间的距离，以确定它们有多相似或不相似。这正是我们在这里要做的。

余弦相似性的数学符号如下所示:

[similarity = \ cos(\ Theta)= \ frac { A \ cdot B }

我们取两个向量 A 和 B 之间的[点积](https://en.wikipedia.org/wiki/Dot_product)，并除以它们的[大小](https://en.wikipedia.org/wiki/Magnitude_(mathematics)#Euclidean_vector_space)的乘积。

下面的代码片段实现了这样的公式。同样，它可能看起来令人生畏并且相当复杂，但是如果你花些时间通读它，你会发现它并不难理解。

事实上，我们在这里的实现不仅仅是计算余弦相似度。除此之外，我们复制包含颜色的数据帧，并向其中添加另一列，该列将包含 0 到 1 之间的距离值。完成后，我们将拷贝的数据帧按距离降序排列。我们这样做是为了在以后查询相似颜色时查看计算出的值。

```
def similar(df, coord, n=10):
    # turning our RGB values (3D coordinates) into a numpy array
    v1 = np.array(coord, dtype=np.float64)

    df_copy = df.copy()

    # looping through our DataFrame to calculate the distance for every color
    for i in df_copy.index:
        item = df_copy.loc[i]
        v2 = np.array([item.r, item.g, item.b], dtype=np.float64)
        # cosine similarty calculation starts here
        theta_sum = np.dot(v1, v2)
        theta_den = np.linalg.norm(v1) * np.linalg.norm(v2)
        # check if we're trying to divide by 0
        if theta_den == 0:
            theta = None
        else:
            theta = theta_sum / theta_den
        # adding the `distance` column with the result of our computation
        df_copy.at[i, 'distance'] = theta
    # sorting the resulting DataFrame by distance
    df_copy.sort_values(by='distance', axis=0, ascending=False, inplace=True)
    return df_copy.head(n) 
```

为了验证我们的`similar`函数是否有效，我们可以用它来寻找与`red`相似的颜色。

```
similar(df, colors['red']) 
```

[![The intuition behind Word2Vec](img/013d09104336400fe04bd82121ebfba6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R54x8KdB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://philippmuens.com/conteimg/2020/01/Bildschirmfoto-2020-01-13-um-19.34.51.png)

我们还可以将颜色作为 RGB 值列表传入。

```
similar(df, [100, 20, 120]) 
```

[![The intuition behind Word2Vec](img/ca79a80fa212940d15f847f7f3a7cfcd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HQ10hzLr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://philippmuens.com/conteimg/2020/01/Bildschirmfoto-2020-01-13-um-19.35.27.png)

既然很难想象`100`、`20`、`120`代表什么颜色，那就值得用我们的`render_color`函数来看看。

```
render_color(100, 20, 120) 
```

[![The intuition behind Word2Vec](img/1ffafe9280352af63989d3d08daf75e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jgovqecY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://philippmuens.com/conteimg/2020/01/Bildschirmfoto-2020-01-13-um-19.36.05.png)

从上面看最相似颜色的列表，似乎`darkviolet`与`100`、`20`、`120`非常相似。让我们看看这个颜色是什么样的。

```
darkviolet = df.loc['darkviolet']
render_color(darkviolet.r, darkviolet.g, darkviolet.b) 
```

[![The intuition behind Word2Vec](img/91c34b31f9d737cedfe1801241c6ff2a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZH-7oTlf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://philippmuens.com/conteimg/2020/01/Bildschirmfoto-2020-01-13-um-19.36.44.png)

我们可以验证`darkviolet`实际上看起来与`100`、`20`、`120`非常相似！

但这并没有结束。我们的 3 个颜色值是 0 - 255 范围内的数字。鉴于此，应该可以对它们进行一些基本的数学计算，如加法或减法。

由于我们只能访问 256 种不同的颜色，所以我们得到的红色、绿色和蓝色的颜色值不太可能与 256 种颜色中的一种完全匹配。这就是我们的`similar`函数派上用场的地方！`similar`函数应该可以计算一种新的颜色，并在我们的 256 色数据集中找到它最相似的表示。

我们可以查看一个[色轮](https://www.sessions.edu/color-calculator/)来看到从`purple`中减去`red`颜色应该会产生一个更蓝的颜色。让我们算一算，看看这是不是真的。

```
blueish = df.loc['purple'] - df.loc['red']

similar(df, blueish) 
```

[![The intuition behind Word2Vec](img/71e89129b534d04810c10168656be2d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--meokZ1nr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://philippmuens.com/conteimg/2020/01/Bildschirmfoto-2020-01-13-um-19.37.20.png)

果然在我们的数据集中最相似的颜色是蓝色的。我们可以通过呈现最佳匹配之一的`darkblue`来验证这一点。

```
darkblue = df.loc['darkblue']
render_color(darkblue.r, darkblue.g, darkblue.b) 
```

[![The intuition behind Word2Vec](img/536432c2b7286731cfeb43a3da0c868c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z2nxdI1R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://philippmuens.com/conteimg/2020/01/Bildschirmfoto-2020-01-13-um-19.38.45.png)

这里有一个简单的。如果我们有黑色和一些白色混合，我们应该得到一些灰色的东西，对不对？

```
greyish = df.loc['black'] + df.loc['white']

similar(df, greyish) 
```

[![The intuition behind Word2Vec](img/44a9324fb6b4e8aea6264cc30c09476f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4RrETAGb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://philippmuens.com/conteimg/2020/01/Bildschirmfoto-2020-01-13-um-19.39.21.png)

果然我们做到了。渲染图`grey93`显示浅灰色。

```
grey93 = df.loc['grey93']
render_color(grey93.r, grey93.g, grey93.b) 
```

[![The intuition behind Word2Vec](img/ad1920d23ecc0e5911fe10b39edd62a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yjXpFw0r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://philippmuens.com/conteimg/2020/01/Bildschirmfoto-2020-01-13-um-19.40.07.png)

让我们用一个更复杂的公式来结束我们对颜色的探索。到目前为止，我们只做了一些非常简单的数学运算，比如颜色的加减。但是我们还能做更多。我们也可以将对颜色的搜索表达为“求解 x”问题。

将黄色和红色混合会产生橙色。我们也可以将这种行为转化为其他颜色。这里我们问“黄色对红色，就像 X 对蓝色一样”，并用数学符号表示，以得到 X 的结果

```
# yellow is to red as X is to blue
yellow_to_red = df.loc['yellow'] - df.loc['red']
X = yellow_to_red + df.loc['blue']

similar(df, X) 
```

[![The intuition behind Word2Vec](img/9ce813234d162d4804c561d409c3beac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jyXcwlyY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://philippmuens.com/conteimg/2020/01/Bildschirmfoto-2020-01-13-um-19.40.43.png)

我们的计算告诉我们,`lightseargreen`对于蓝色就像黄色对于红色一样。如果你仔细想想，直觉上这是有道理的。

```
lightseagreen = df.loc['lightseagreen']
render_color(lightseagreen.r, lightseagreen.g, lightseagreen.b) 
```

[![The intuition behind Word2Vec](img/9841f0934fd35e1b106072ff6edc9099.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--okJJvppX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://philippmuens.com/conteimg/2020/01/Bildschirmfoto-2020-01-13-um-19.41.25.png)

## Word2Vec

在本教程的开始，我承诺一旦完成，我们应该理解 [Word2Vec](https://en.wikipedia.org/wiki/Word2vec) 背后的直觉，这是现代自然语言处理模型的关键组成部分。

`Word2Vec`模型对文字做的和我们用 RGB 值表示的颜色做的一样。它将单词映射到多维空间(我们的颜色被映射到三维空间)。一旦这些单词被映射到那个空间，我们就可以对它们的向量进行数学计算，就像我们计算颜色向量之间的相似度一样。

将单词映射到这样的向量空间使得进行计算成为可能，从而导致:

[国王-男人+女人=王后]

## 结论

在本教程中，我们深入探讨了嵌入背后的主要构件和直觉，这是一个在现代自然语言处理模型中大量使用的强大概念。

主要思想是将数据映射到多维空间，以便可以在其上执行来自线性代数领域的数学计算。

我们从一个简单的例子开始我们的旅程，在这个例子中，我们将不同国家的表面积和人口映射到一个 2D 向量空间中。然后，我们使用[欧几里德距离](https://en.wikipedia.org/wiki/Euclidean_distance)来验证某些国家是相似的，而其他国家是不相似的。

另一个更高级的示例将颜色及其 RGB 表示映射到 3D 矢量空间。然后我们使用[余弦相似度](https://en.wikipedia.org/wiki/Cosine_similarity)和一些基本的数学来加减颜色。

有了这些知识，我们现在能够理解更高级的模型，如 [Word2Vec](https://en.wikipedia.org/wiki/Word2vec) 或 [Doc2Vec](https://cs.stanford.edu/~quocle/paragraph_vector.pdf) 如何使对单词和文本进行计算成为可能。

## 实验室

你可以在我的 GitHub [Lab](https://github.com/pmuens/lab) 资源库中找到更多的代码示例、实验和教程。

## 附加资源

渴望了解更多信息？下面是我写这篇文章所用的所有资源的清单。

*   [艾莉森·帕里什——理解单词向量](https://gist.github.com/aparrish/2f562e3737544cf29aaf1af30362f469)
*   [杰伊·阿拉玛——图文并茂的 Word2Vec](https://jalammar.github.io/illustrated-word2vec/)
*   Derek Chia-word 2 vec 的逐行实现
*   [sky mind . ai-word 2 vec 初学者指南](https://skymind.ai/wiki/word2vec)
*   [Word2Vec 论文](https://papers.nips.cc/paper/5021-distributed-representations-of-words-and-phrases-and-their-compositionality.pdf)
*   [Doc2Vec 论文](https://cs.stanford.edu/~quocle/paragraph_vector.pdf)
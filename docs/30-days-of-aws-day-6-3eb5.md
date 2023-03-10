# 30 天 AWS(第 6 天)

> 原文：<https://dev.to/gameoverwill/30-days-of-aws-day-6-3eb5>

欢迎回来，伙计们，第六天到了。所以这个挑战比我想象的要困难，但我还是来了。

好了，今天我学习了著名的简单存储服务 S3，但是我在 Linux 学院没有学分，我不能在那里做任何测验。我所做的练习是在我的 AWS 帐户中完成的。

## S3

来自文档“亚马逊简单存储服务(亚马逊 S3)是一种对象存储服务，提供行业领先的可扩展性、数据可用性、安全性和性能。这意味着各种规模和行业的客户都可以使用它来存储和保护一系列使用案例中的任意数量的数据，例如网站、移动应用程序、备份和恢复、归档、企业应用程序、物联网设备和大数据分析”。

S3 是 AWS 提供的主要存储服务。在那里你可以存储任何类型的文件。

您可以在任何 S3 中看到以下组件:

1.  桶:这是你用来保存文件的地方，就像一个文件夹，但亚马逊称之为桶。这个名字在整个 AWS 世界中是独一无二的。默认情况下，它是私人的，但你可以设置一个公共的，唯一的问题是，每次你创建一个公共桶，所有的互联网将能够访问。

[![graphic2](img/d270bb6324c055ebffa48edcf5b3610e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7qk9MfFx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/pgt4bxgvjyoyihwm8f6k.png)

上图中你可以看到 *wilfredo3testcourse* 就像一个文件夹，里面有另一个对象，比如文件夹或文件。

1.  对象:这些是存储在桶中的文件/文件夹。

[![image3](img/08d61c6b6a8e248120758f8c45a6c965.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dGAzKVQd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nyzdmbgs6fhli9wr3axx.png)

在图像中，你可以看到一个文件夹是空的，一个图像存储在我的 S3 桶中。

在这里，您可以看到 S3 组件如何工作的完整图表。

[![image4](img/2773836fe1245ed0e2a8585a2f6b678f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--315w3BSd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5zfemxuy1v9vo7hmo31j.png)

## S3 存储类

存储类表示 S3 每个对象的分类，可以是标准、标准 _AI、OneZone_IA、冰川。每个存储类别都有其成本和可用性。

1.  标准存储:这是默认选项，设计用于一般用途。它具有 99.999999999%的物体耐久性。这是 99.99%的对象可用性。这是最昂贵的选择，因为资源总是可用的。

2.  Standard_AI:它是为你不经常访问的对象而设计的，但是你需要它在你需要的时候会立即可用。它比标准存储更便宜。这是 99.5%的对象可用性。并且它具有 99.999999999%的物体耐久性。

3.  OneZone_IA:它是为非关键对象设计的。它比其他选择便宜。这是 99.5%的对象可用性。

4.  Glacier:它是为长期档案存储而设计的。要取回储存在冰川中的物品需要几个小时。它具有 99.999999999%的物体耐久性。成本很低。

## S3 版本化。

这个特性可以让你跟踪和存储你存储在任何桶中的对象的所有版本。你可以随意检查旧版本。它可以随时打开/关闭。

好了，伙计们，今天就到这里。我希望明天继续更新这个冒险。

感谢阅读。
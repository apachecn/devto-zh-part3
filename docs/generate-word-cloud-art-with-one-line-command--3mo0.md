# 用一行命令生成文字云图

> 原文：<https://dev.to/0xkoji/generate-word-cloud-art-with-one-line-command--3mo0>

最近，我试图创建一个 python 包，昨晚我发布了一个新的包，wcartist。

wcartist 能做的就是用一行命令生成一个字云艺术形象。

你需要的步骤真的很简单。

1.  安装 wcartist
2.  获取 word cloud 的图像。有一件事你必须遵循的是，现在 wcartist 只处理黑白图像，如下所示。

[![sample](img/be3e6d699ddd4fcade215af666bf8a11.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JRiJy_j_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://img0103.pipa.jp/tegaki/user_img103/12988/1298805407613452.png)

1.  创建一个文本文件。当然，我们需要一些文本来创建一个单词云

2.  运行命令

这个案例使用了维基百科中关于 facebook 的文本。
[![test](img/2c5259315e4af327dbbffb88300a4ec4.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--D_fBK96F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lkja9j4hyuu0df31q22a.png)

详情
[https://github.com/koji/wcartist](https://github.com/koji/wcartist)

正如我提到的，现在`wcartist`只支持黑色的&白色的图像，但是在未来，`wcartist`将支持任何类型的图像。
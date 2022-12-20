# 罗马不是一天建成的，但威德卡是！

> 原文：<https://dev.to/hrbrmstr/rome-was-not-built-in-a-day-but-widgetcard-was-3d30>

我看到了第二个关于将 htmlwidgets 变成交互式 Twitter 播放器卡的帖子，并觉得有必要让创建所述实体变得更容易一些，因此提出了以下观点:

> 这个打包有用吗？https://t.co/sfqlWnEeJVhttps://t.co/troKzmzTNv[T4](https://t.co/sfqlWnEeJV)
> 
> (TLDR/V:将一个 HTML 小部件变成可部署的交互式 Twitter 卡的单一函数)[pic.twitter.com/uahB52YfE2](https://t.co/uahB52YfE2)
> 
> —鲍勃·鲁迪斯([@ hrbrmstr](http://twitter.com/hrbrmstr))[2019 年 3 月 26 日](https://twitter.com/hrbrmstr/status/1110510174958223362?ref_src=twsrc%5Etfw)

我估计有 40 多岁💙不会错，所以就有了`widgetcard`:

*   [来源帽子](https://git.sr.ht/~hrbrmstr/widgetcard)
*   [GitLab](https://gitlab.com/hrbrmstr/widgetcard)
*   [CINC](https://cinc.rud.is/web/packages/widgetcard/)
*   [到](https://github.com/hrbrmstr/widgetcard)

为了使这篇文章尽可能简短，TLDR 就是你只需要传入一个`htmlwidget`和一些必需的参数，然后你就可以得到一个可部署的交互式 Twitter 播放器卡，作为一个归档文件和本地目录。示例代码几乎一样短，因为我们正在作弊，并使用非常有用的`plotly`包将 ggplot2 vis 转换为交互式的东西。

首先，制作 vis:

```
library(ssh)
library(plotly)
library(ggplot2)
library(widgetcard)

ggplot(mtcars, aes(wt, mpg)) +
  geom_point() -> gg 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们为刚刚绘制的图创建一个局部预览图像，因为我们需要为卡片创建一个图像:

```
preview <- gg_preview(gg) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，您可以使用任何想要的图像。此功能简化了从 ggplot2 图创建 plotly 图的过程。软件包帮助文件中有映像大小调整指南的链接。

现在，我们将 ggplot2 对象转换为 plotly 对象，并创建 Twitter Player 卡。请注意，Twitter *真的*不喜欢独立的小部件被用作 Twitter 玩家卡链接，因为它们的重量级大小。因此，`card_widget()`创建了一个非独立的小部件，但是将所有东西打包到一个目录和可部署的归档文件中。

```
ggplotly(gg) %>% 
  card_widget(
    output_dir = "~/widgets/tc",
    name_prefix = "tc",
    preview_img = preview,
    html_title = "A way better title",
    card_twitter_handle = "@hrbrmstr",
    card_title = "Basic ggplot2 example",
    card_description = "This is a sample caRd demonstrating card_widget()",
    card_image_url_prefix = "https://rud.is/vis/tc/",
    card_player_url_prefix = "https://rud.is/vis/tc/",
    card_player_width = 480,
    card_player_height = 480
  ) -> arch_fil 
```

Enter fullscreen mode Exit fullscreen mode

下面是结果目录结构的样子:

```
tc
├── tc.html
├── tc.png
└── tc_files
    ├── crosstalk-1.0.0
    │   ├── css
    │   │   └── crosstalk.css
    │   └── js
    │   ├── crosstalk.js
    │   ├── crosstalk.js.map
    │   ├── crosstalk.min.js
    │   └── crosstalk.min.js.map
    ├── htmlwidgets-1.3
    │   └── htmlwidgets.js
    ├── jquery-1.11.3
    │   ├── jquery-AUTHORS.txt
    │   ├── jquery.js
    │   ├── jquery.min.js
    │   └── jquery.min.map
    ├── plotly-binding-4.8.0
    │   └── plotly.js
    ├── plotly-htmlwidgets-css-1.39.2
    │   └── plotly-htmlwidgets.css
    ├── plotly-main-1.39.2
    │   └── plotly-latest.min.js
    ├── pymjs-1.3.2
    │   ├── pym.v1.js
    │   └── pym.v1.min.js
    └── typedarray-0.1
        └── typedarray.min.js 
```

Enter fullscreen mode Exit fullscreen mode

(在`tc`目录的同一层还有一个`tc.tgz`。)

使用`widgetframe`对小部件进行 iframe 处理，然后使用`htmlwidgets::saveWidget()`保存。

现在，为了将它部署到 web 服务器，可以使用这样的方法来`scp`可部署的归档:

```
sess <- ssh_connect(Sys.getenv("SSH_HOST"))

invisible(scp_upload(
  sess, files = arch_fil, Sys.getenv("REMOTE_VIS_DIR"), verbose = FALSE
))

ssh_exec_wait(
  sess,
  command = c(
    sprintf("cd %s", Sys.getenv("REMOTE_VIS_DIR")),
    sprintf("tar -xzf %s", basename(arch_fil))
  )
) 
```

Enter fullscreen mode Exit fullscreen mode

或者，您可以使用其他工作流将归档文件*或*副本输出传输和扩展到您的静态博客主机。

确保在发布之前测试你用 Twitter 的[验证器](https://cards-dev.twitter.com/validator)构建的任何东西。

### 鳍

这是可行的，但还处于初级阶段，可能需要一些严重的 IRL 踢轮胎和残酷的反馈。选择一个你喜欢的最不冒犯的社交编码网站，随意发布问题和公关。
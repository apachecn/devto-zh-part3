# 在开发中:用 wiglr 进入热点

> 原文：<https://dev.to/hrbrmstr/in-dev-wigle-your-way-into-a-hotspot-with-wiglr-165g>

[WiGLE](https://wigle.net) 已经存在*而*是一个探索全球 Wi-Fi(和蜂窝)网络普及或稀疏的伟大网站。虽然交互式使用很有趣，但 WiGLE 也有一个免费的 API(只要你遵守 EULA，不滥用)，如果你注册一个帐户，获得一个密钥，并使用 WIP[`wiglr`](https://github.com/hrbrmstr/wiglr)|[GL](https://gitlab.com/hrbrmstr/wiglr)|[GH](https://github.com/hrbrmstr/wiglr)包，它可以让你探索得更深入一些。

从攻击性最小的社交编码网站安装:

```
devtools::install_git("https://sr.ht.com/~hrbrmstr/wiglr")
# or
devtools::install_gitlab("hrbrmstr/wiglr")
# or (if you must)
devtools::install_github("hrbrmstr/wiglr") 
```

Enter fullscreen mode Exit fullscreen mode

阅读代码(以确保 R 包开发人员正在 pwning 或跟踪您)，并且…在将您的“编码使用”令牌放入`WIGLE_API_KEY`环境变量中之后…开始探索！

让我们找出所有国家的统计数据，并绘制出前 20 名:

```
library(wiglr)
library(hrbrthemes)
library(tidyverse) # for show

cc <- wigle_country_stats()

top_n(cc, 20) %>% # show top 20 
  mutate(country = factor(country, levels = rev(country))) %>% 
  ggplot(aes(count, country)) +
  geom_segment(aes(xend=0, yend=country), size = 6, color = ft_cols$blue) +
  scale_x_comma(position = "top") +
  labs(
    x = "# Networks", y = NULL,
    title = "WiGLE Top 20 Countries"
  ) +
  theme_ipsum_rc(grid = "X") 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/73f3d0b316ef93b2b7fc92fe9a9b4161.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--C8xFZU4U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i0.wp.com/rud.is/b/wp-content/uploads/2019/02/country-stats-02-1.png%3Fresize%3D780%252C585%26ssl%3D1)

我们还可以提供一个边界框，找到附近的所有 Wi-Fi 接入点，并使用`leaflet`制作一个方便的交互式地图(您也可以添加其他列来找到完全开放的列):

```
library(leaflet)

wifi_box <- wigle_bbox_search(43.2468, 43.2806, -70.9282, -70.8025)

wifi_box$results %>% 
  mutate(labs = sprintf("SSID: <code>%s</code><br/>Encryption: %s", ssid, encryption)) %>% 
  leaflet() %>% 
  addTiles() %>% 
  addCircleMarkers(~trilong, ~trilat, radius = 1, popup = ~labs) 
```

Enter fullscreen mode Exit fullscreen mode

点击图片查看小工具…

[![](img/d6dc73aa1c83f390622477ee8ac41431.png)](https://rud.is/dl/wigle-01.html)

有很好但不完整的 WiGLE API 报道:

*   `wigle_about_me`:获取当前登录用户的 WiGLE 用户对象
*   `wigle_api_key`:获取或设置 WIGLE_API_KEY 值
*   `wigle_bbox_search`:获取 WiGLE 命名的通用统计地图
*   `wigle_country_stats`:获取按国家组织的 WiGLE 统计数据
*   获取指定国家的 WiGLE 统计数据，按地区组织
*   `wigle_site_stats`:获取 WiGLE 命名的通用统计地图

### 鳍

这个包是一个 WIP，但是 API 非常简单，所以如果你想贡献一个(我敢说“有趣！”)2019 年开源项目现在你的机会来了！只要在你喜欢的任何社交编码网站上发表一个问题(如果你使用 SourceHut、GitLab 或 GitHub 之外的东西，请在评论中 ping 我，我会把包放在那里)并说明你想做什么*或*只需提交一份清晰的 PR。潜在的改进领域包括:

*   支持更多搜索参数
*   自动分页的功能
*   涵盖其余的搜索/检索 API 端点
*   研究如何使用 R 来*提交*读数
*   创建一个内部软件包闪亮的应用程序来探索 WiGLE 直接从 R
*   开发标准基础可视化，并将它们作为包函数添加
*   写一篇短文
*   改进包文档
*   通过对人口普查数据进行分层，开发一个统计模型，用于评估在一个地区找到免费/开放 Wi-Fi 的可能性，或 Wi-Fi/蜂窝沙漠对社区的影响

使用或开发该软件包也可能有助于揭示我们在数字世界中暴露的另一种方式。[![](img/093eeab51d4fc0e90a130652d94e2540.png)](https://rud.is/b/)

和往常一样，你会得到不加评判的帮助/建议。如果克兰能够通过这一过程，那么它的描述、名声和荣耀都值得称赞。这个包现在对我来说已经足够了，所以如果其他人不先加入的话，我还需要一段时间才能完成上面的任务。
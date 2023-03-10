# 家酿 2.0.0 发布==家酿分析包更新

> 原文：<https://dev.to/hrbrmstr/homebrew-200-released--homebrewanalytics-package-updated-4dn0>

一个重要的自制软件新版本[已经登陆](https://brew.sh/2019/02/02/homebrew-2.0.0/)，现在包括对 Linux 和 Windows 的支持！通过 Linux 的 Windows 子系统。还有整体稳定性和速度的改善。前面提到的通知包含了你看到细节所需的所有信息。除非你在更新方面过于松懈，`brew update`会给你最新的版本。

有额外的公式分析端点和 [`homebrewanalytics`](https://git.sr.ht/~hrbrmstr/homebrewanalytics) R 包已经更新，以处理它们。软件包中值得注意的一个变化是所有的 API 调用都被记忆，以避免冲击自制服务器(尽管“API”实际上只是文件端点，它们不是大文件，但带宽就是带宽)。如果您有长时间运行的脚本，使用`memoise`包中的工具使缓存无效。

使用你最喜欢的社交编码网站来安装它(如果我没有在你选择的开放社交编码平台上维护镜像，只需在评论中留言，我也会在那里开始镜像):

```
devtools::install_git("https://git.sr.ht/~hrbrmstr/homebrewanalytics")
# or
devtools::install_gitlab("hrbrmstr/homebrewanalytics")
# or
devtools::install_github("hrbrmstr/homebrewanalytics") 
```

Enter fullscreen mode Exit fullscreen mode

[自述文件](https://gitlab.com/hrbrmstr/homebrewanalytics/blob/master/README.md)和包内手册页提供了检索数据的基本示例。但是我们可以在这里改进，比如找出自制公式的依赖分布:

```
library(hrbrthemes)
library(homebrewanalytics) # git.sr.hr/~hrbrmstr ; git[la|hu]b/hrbrmstr
library(tidyverse)

f <- brew_formulae()

mutate(f, n_dependencies = lengths(build_dependencies)) %>% 
  count(n_dependencies) %>% 
  mutate(n_dependencies = factor(n_dependencies)) %>% 
  ggplot() +
  geom_col(aes(n_dependencies, n), fill = ft_cols$slate, width = 0.65) +
  scale_y_comma("# formulae") +
  labs(
    x = "# Dependencies",
    title = "Dependency distribution for Homebrew formulae"
  ) +
  theme_ft_rc(grid="Y") 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/1d05349b8375bc9c62948c283302f451.png)](https://rud.is/b/2019/02/02/homebrew-2-0-0-released-homebrewanalytics-package-updated/01-dep-dist/)

鉴于升级我的各种自制软件有时需要很长时间，我很惊讶地看到`0`如此普遍，但 2.0.0 中的主要变化之一将是更多的二进制安装(除非你真的需要定制版本)，所以这可能是我经验的一部分，特别是我需要支持网络安全和空间操作的公式。

我们还可以看到哪些公式位于前 50%:

```
unlist(f$dependencies) %>% 
  table(dnn = "library") %>% 
  broom::tidy() %>% 
  arrange(desc(n)) %>% 
  mutate(pct = n/sum(n), cpct = cumsum(pct)) %>% 
  filter(cpct <= 0.5) %>% 
  mutate(pct = scales::percent(pct)) %>% 
  mutate(library = factor(library, levels = rev(library))) %>% 
  ggplot(aes(n, library)) +
  geom_segment(aes(xend=0, yend=library), color = ft_cols$slate, size=3.5) +
  geom_text(
    aes(x = (n+max(n)*0.005), label = sprintf("%s (%s)", n, pct)), 
    hjust = 0, size = 3, family = font_rc, color = ft_cols$gray
  ) +
  scale_x_comma(position = "top", limits=c(0, 500)) +
  labs(
    x = "# package using the library", y = NULL,
    title = "Top 50% of libraries used across Homebrew formulae"
  ) +
  theme_ft_rc(grid="X") +
  theme(axis.text.y = element_text(family = "mono")) 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/1d542d2b0976e3cc88002f04367ece4c.png)](https://rud.is/b/2019/02/02/homebrew-2-0-0-released-homebrewanalytics-package-updated/02-50-libs-01/)

看起来`openssl`很受欢迎(这并不奇怪，但看到网络安全问题出现在好列表的首位总是件好事)！macOS 配备了一个比平常更可怕的默认 Python 设置(*我知道这很难想象*)，所以它排在第二位并不意外。

最后，我们还可以检查公式的安装频率。让我们回顾一下过去的 90 天:

```
ggplot() +
  geom_density(
    aes(x = installs$count, y = stat(count)),
    color = ft_cols$slate, fill = alpha(ft_cols$slate, 1/2)
  ) +
  scale_x_comma("# install events", trans = "log10") +
  scale_y_comma("# formulae") +
  labs(
    title = "Homebrew Formulate 'Install Events' Distribution (Past 90 days)"
  ) +
  theme_ft_rc(grid="XY") 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/3231fcc97245dc35724510cfd339b0e1.png)](https://rud.is/b/2019/02/02/homebrew-2-0-0-released-homebrewanalytics-package-updated/03-install-events/)

我会让你玩这个包，找出谁是重量级人物，并探索更多关于家酿生态系统。

### 鳍

踢轮胎。文件问题&公关和一个衷心的“欢迎！”Linux 和 Windows 用户的家酿生态系统。我的希望是，WSL 的可用性最终将使 windows 系统的开发变得更容易，并避免我们今天所采用的“在软件包安装时从 github 下载有点粗略编译的 Windows 库”的做法。

如果你用这些包做了一些分析，别忘了写博客，并在评论里放一个链接！
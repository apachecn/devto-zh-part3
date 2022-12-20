# 随机 Youtube 歌曲播放器

> 原文：<https://dev.to/harithmohamad97/random-youtube-song-player-9ah>

# 随机 Youtube 歌曲播放器

([https://rahmanify . harith . XYZ](https://rahmanify.harith.xyz))

嗨，

我刚刚开始网络开发，已经建立了这个有趣的项目。这是一个随机的歌曲生成器，来自著名的印度作曲家 A.R. Rahman 的专辑列表。

我已经使用了来自 https://musicbrainz.org 的 API 来获取 JSON 格式的作曲家的专辑数据库。然后，我从列表中随机选择一首歌曲，并将其传递给 Youtube 搜索 API，它将返回一个搜索结果列表。然而，我只返回总是准确的最高结果。然后，web 应用程序使用返回的视频 ID 将页面重定向到 youtube。最后，web 应用程序托管在 Firebase 上。

此应用程序的限制:

1.  由于 API 的限制，无法过滤专辑的年份。
2.  还不能在 Safari 和 Firefox 上运行。
3.  我想 Youtube API 每个 IP 只接受大约 200 个请求。

非常感谢任何关于如何改进这个简单应用程序的建议，
谢谢。
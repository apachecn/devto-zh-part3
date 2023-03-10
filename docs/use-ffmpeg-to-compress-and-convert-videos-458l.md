# 使用 ffmpeg 压缩、转换和调整视频大小

> 原文：<https://dev.to/benjaminblack/use-ffmpeg-to-compress-and-convert-videos-458l>

我的帖子通常是我自己的笔记和参考资料，我在这里发布，希望其他人会觉得有用。

给定一个源视频`source.mp4`:

以大约 1.5Mbps 视频/ 128kbps 音频的合理宽带比特率压缩网页:

```
ffmpeg -i source.mp4 -c:v libx264 -b:v 1.5M -c:a aac -b:a 128k target.mp4 
```

Enter fullscreen mode Exit fullscreen mode

要压缩并转换为 WebM:

```
ffmpeg -i source.mp4 -c:v libvpx-vp9 -b:v 1M -c:a libopus -b:a 128k target.webm 
```

Enter fullscreen mode Exit fullscreen mode

(请注意，33%的低比特率 VP9 的 1 兆对 H.264 的 1.5 兆——是故意的；VP9 的编码效率比 H.264 高 20-50%，Opus 和 AAC 的效率差不多。)

为了将高分辨率的源视频缩小到更适合网络的程度(对于蜂窝网络来说， [qHD，对于宽带来说，](https://en.wikipedia.org/wiki/16:9_aspect_ratio#Common_resolutions))，使用了`-filter:v`参数:

```
ffmpeg -i source.mp4 -c:v libvpx-vp9 -b:v 0.33M -c:a libopus -b:a 96k \
-filter:v scale=960x540 target.webm 
```

Enter fullscreen mode Exit fullscreen mode

命令行选项:`-c:v`指定视频编解码器；`-b:v`指定视频比特率；`-c:a`指定音频编解码器；`-b:a`音频比特率；`-filter:v`对视频流应用滤镜(在本例中为缩放)。

要在 WebM 中使用较旧的 VP8 编解码器，请使用`libvpx`而不是`libvpx-vp9`。

如果源视频没有音频轨道，那么省略`-c:a`和`-b:a`参数。

要明确指定容器格式，使用`-f`命令行选项:`-f mp4`或`-f webm`。

对于 MP4 容器，使用的视频编解码器是 H.264，音频编解码器是 AAC，它享有近乎通用的浏览器支持。

针对 Chrome 和相关浏览器。WebM 容器使用 VP8/9 和 Opus。

Chrome 还支持 MP4 (H.264/AAC)，因此除非文件大小和/或质量得到改善，否则提供 WebM 没有任何意义。

比特率显然可以变化；我使用 0.5M 视频和 96k 音频。
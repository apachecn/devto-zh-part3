# ffmpeg:使用命令行剪切媒体文件

> 原文：<https://dev.to/nabbisen/ffmpeg-using-command-line-to-cut-out-video-files-1o3a>

**封面图片由[格雷巴比](https://pixabay.com/en/users/Greyerbaby-2323/)原创，经过精心编辑。*

#### 总结

当我处理简单的任务时，我喜欢使用命令行工具，因为它们:

*   快跑
*   需要更少的内存
*   与目标阵列配合良好
*   使我们能够将订单记录为文本

嗯，最近在网站上保存视频文件并展示它们变得容易多了。有了 [ffmpeg](https://ffmpeg.org/ffmpeg.html) (还有 [ffprobe](https://ffmpeg.org/ffprobe.html) )，就可以在上传之前把它们剪掉并调整它们的时长。

#### 环境

*   ffmpeg / ffprobe: 4.1

<center>✿ ✿ ✿</center>

#### 如何使用

##### 剪切出媒体文件

```
$  ffmpeg -ss %from-seconds% -t %to-seconds% -c copy -i %input.file% %output.file% 
```

例如，`ffmpeg -ss 0 -t 17 -i some-in.mp4 some-out.mp4`制作内容为 some-in.mp4 0-17s 的 some-out.mp4。

此外，`-c`是`-codec`的简称，`-c copy`是指所有流都将被复制而无需重新编码。
它是用来逃避变质的它们。
主选项手册此处为。

对于持续时间的[语法，指定秒和`HH:MM:SS.m`格式都可以。](https://ffmpeg.org/ffmpeg-utils.html#time-duration-syntax) 

```
$  ffmpeg -ss %from-HH:MM:SS.m% -t %to-HH:MM:SS.m% -c copy -i %input.file% %output.file% 
```

例如，`ffmpeg -ss 00:00:00.0 -t 00:00:17.0 -i some-in.mp4 some-out.mp4`与前一个例子相同，只是使用了`HH:MM:SS.m`格式。

##### 获取持续时间

`ffprobe`提供获取媒体文件时长的方法。

为了得到秒数，命令是这样的:

```
$  ffprobe -v error -show_entries format=duration -of default=noprint_wrappers=1:nokey=1 %input.file%
17.017000 
```

为了得到`HH:MM:SS.m`，在上面的命令中添加`sexagesimal`选项:

```
$  ffprobe -v error -show_entries format=duration -of default=noprint_wrappers=1:nokey=1 -sexagesimal %input.file%
0:00:17.017000 
```

通用选项的手册在这里是。

<center>✿ ✿ ✿</center>

快乐终结
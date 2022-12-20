# 如何将一批 wav 文件转换成 mp3 文件

> 原文：<https://dev.to/wincentbalin/how-to-convert-a-batch-of-wav-file-to-mp3-files-2lei>

如果你不想手动将`.wav`文件转换成 MP3 格式，并且你手上只有 Windows 提示符和一个`ffmpeg.exe`(看这里的)，运行的命令是

```
for %f in (*.wav) do ( ffmpeg -i "%f" -q:a 2 "%~nf.mp3" ) 
```

Enter fullscreen mode Exit fullscreen mode

参数`-q:a 2`代表*可变比特率*；这是一个标准的 FFMPEG 参数。您也可以使用其他 FFMPEG 参数。

查看[此处](https://stackoverflow.com/questions/39615/how-to-loop-through-files-matching-wildcard-in-batch-file)了解 Windows 命令行的进一步改进。
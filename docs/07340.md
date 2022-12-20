# 从 ASP.NET MVC 动作中返回一个 Zip 文件。网

> 原文：<https://dev.to/alexjitbit/returning-a-zip-file-from-asp-net-mvc-action-in-pure-net-44ni>

如果你搜索“在 ASP.NET/C#"生成 zip ”,你会得到大量的博客文章和 StackOverflow 答案，推荐 DotNetZip、SharpZipLib 和其他第三方库。到目前为止，这是唯一的解决方案。

酪 NET Framework 4.5 和更高版本(最终)包含了用于处理来自`System.IO.Compressions`名称空间的 Zip 存档- `ZipArchive`类的本机工具。

下面是一个如何在 ASP.NET MVC 动作方法中动态生成一个 zip 文件的例子(例如，如果你想在一个响应中返回多个文件，这很方便):

```
public ActionResult GetFile()
{
    using (var ms = new MemoryStream())
    {
        using (var archive = new Compression.ZipArchive(ms, ZipArchiveMode.Create, true))
        {
            byte[] bytes1 = GetImage1Bytes();
            byte[] bytes2 = GetImage2Bytes();

            var zipArchiveEntry = archive.CreateEntry("image1.png", CompressionLevel.Fastest);
            using (var zipStream = zipArchiveEntry.Open())
            {
                zipStream.Write(bytes1, 0, bytes1.Length);
            }

            var zipArchiveEntry2 = archive.CreateEntry("image2.png", CompressionLevel.Fastest);
            using (var zipStream = zipArchiveEntry2.Open())
            {
                zipStream.Write(bytes2, 0, bytes2.Length);
            }
        }
        return File(ms.ToArray(), "application/zip", "Images.zip");
    }
} 
```

我刚刚将两个图像文件(以字节数组的形式)添加到一个 zip 存档文件中，并将其返回给用户。`GetImage1Bytes`和`GetImage2Bytes`在这里只是你的代码的存根。很好很容易。但最棒的是——你没有使用任何外部依赖，全部是纯. NET。
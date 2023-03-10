# 将 docker 容器日志发送到单独的文件

> 原文：<https://dev.to/flaviabastos/sending-docker-container-logs-to-a-separate-file-5d3l>

在大型 web 应用程序中，后端日志会变得非常冗长:请求的创建、发送、处理、接收等。根据日志的实现方式，列表可能会很快变大。

您可以使用`docker logs`从 Docker 容器中检查日志，无需执行到容器:

```
docker logs <container\_id> 
```

你可以用`docker ps`找到*，不需要使用完整的 ID。前几个字符就足够了。因此，如果容器 id 是`e1215bf8014c`，该命令将类似于:* 

```
docker logs e121 
```

如果你运行`docker logs`，你可能会看到更多的日志屏幕。您可以使用`--tail` :
将日志限制到最后的`n`行

```
docker logs --tail 500 <container\_id> 
```

上面的命令将只显示最后 500 行。好多了。

但是在终端上阅读日志并不有趣，所以您可能还想将日志定向到一个可以在文本编辑器中打开的文件中。要将 docker 容器日志输出到一个文件中，运行:

```
docker logs --tail 500 <container\_id> > mylogs.txt 
```

> _ 帖子[将 docker 容器日志发送到一个单独的文件](https://wp.me/pa0b0y-3Q)最初发表在 _ [flaviabastos.ca](https://flaviabastos.ca/)*
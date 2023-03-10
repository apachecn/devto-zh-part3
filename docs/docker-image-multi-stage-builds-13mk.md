# 用多阶段构建 leen docker 图像

> 原文：<https://dev.to/zarewoft/docker-image-multi-stage-builds-13mk>

> 首次发布于 2018-01-18[媒](https://medium.com/zarewoft/%E0%B8%AA%E0%B8%A3%E0%B9%89%E0%B8%B2%E0%B8%87-docker-image-%E0%B8%A5%E0%B8%B5%E0%B8%99%E0%B9%86-%E0%B8%94%E0%B9%89%E0%B8%A7%E0%B8%A2-multi-stage-builds-e786ba6d5102)。

你好，t0㎡这个博客是 Docker 的第一个博客

* * *

对于 Docker 部署来说，“规模并不重要”，这肯定不是真的，因为随着规模的增大，我们在注册表中为存储 image 付出了更多的存储成本。

缩小 docker image 有着广泛的实用性，我们应该做到，先生，比如不尝试用`RUN`2305 创建不必要的新图层，设置 see。

# 多阶段构建

我总是喜欢在 docker 上运行测试和构建，以便在相同的环境中运行应用程序之前通过测试，先生，但有时 build

例如，构建一个面向前端应用程序的 gulp。如果您想要一个非常棒的应用程序，我们就需要一个 t0㎡然后来`npm install`然后再使用 gulp。

把这一切都放在图像里是非常不必要的，对吧？因为最终，我们只保存静态文件。如果我们使用它，它会很好的。

多阶段构建是一个概念，也就是说，建立一个阶段，然后把它作为一个通道，再把它应用到另一个阶段。

[![](img/9191c9793238479a7f30fa8f7fd5897c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yPYJb-gg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jslgy2rh0bdoep1mpcji.png)

# 使用方法

在 from
行中指定我们在进程中创建的映像的 name

```
FROM <IMAGE> AS <NAME> 
```

时间，我们将`COPY`文件从原始状态输入，输入名为 from 的参数，添加到
中。

```
COPY --from=<NAME> <ORIGIN_PATH> <DEST_PATH> 
```
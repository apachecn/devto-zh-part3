# 从文本描述生成 UML 图

> 原文：<https://dev.to/renatosuero/generate-uml-diagram-from-a-textual-description-4e9i>

大家好，

我一直讨厌 UML，因为我看到的每一个工具，我都需要“画”，我觉得我的效率很低。所以我发现了 PlantUML，我的世界改变了，现在我可以写和生成图表了。我喜欢这个选项的原因之一是保持图表更新，现在它简单快捷(对我来说)。

让我们看看如何使用这个工具。首先，对我来说另一件好事是，我可以使用 docker =)。
我使用 2 个 docker 图像，首先生成给我的图。我使用 https://hub.docker.com/r/think/plantuml/的[和 T4 的](https://hub.docker.com/r/think/plantuml/)在这个例子中，我创建了一个名为 test.uml 的文件:

```
@startuml
autonumber
Bob -> Alice : Authentication Request
Bob <- Alice : Authentication Response

autonumber 15
Bob -> Alice : Another authentication Request
Bob <- Alice : Another authentication Response

autonumber 40 10
Bob -> Alice : Yet another authentication Request
Bob <- Alice : Yet another authentication Response

@enduml 
```

Enter fullscreen mode Exit fullscreen mode

我运行这个命令

```
cat test.uml | docker run --rm -i think/plantuml > test.svg 
cat test.uml | docker run --rm -i think/plantuml > test.png 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，这就是结果。
[![Diagram generated](img/0b58e176b1a63275c78054a2f082f677.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--PCGlOP64--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/o18copjfp9zgmbqxtblf.png)

另一种方式，我运行服务器来编写和查看图表。我用[https://hub.docker.com/r/plantuml/plantuml-server/](https://hub.docker.com/r/plantuml/plantuml-server/)

在这种情况下，我运行命令

```
docker run -d -p 8080:8080 plantuml/plantuml-server:jetty 
```

Enter fullscreen mode Exit fullscreen mode

然后，我打开浏览器，进入 http://localhost:8080 。如你所见，这就是结果。
T3![plantUML as a server](img/57dd06b2b34166c357a1afbe9a264144.png)T5】

如果你现在想玩，你可以使用官方服务器 T1，使用这个服务器的好处是你可以分享这个图表

我建议您访问官方的[文档](http://plantuml.com/)，我的目标是向您展示生成图表的选项，并帮助您提高工作效率。

我将向您展示如何使用 UML =)
[![Activity to read posts at dev.to](img/c2c1b0d8ec6b0bb29fc769be8ad241fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZZgnyNYx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8exx9icxtgms8adqlj9e.png) 来使用 dev.to 的建议

我想邀请你写/画一个图表并在评论中分享，让我们启发其他读者=)
# Kentico 12:设计模式第 2 部分——编写单元测试

> 原文：<https://dev.to/seangwright/kentico-12-design-patterns-part-2-writing-unit-tests-1f8n>

<figure>[![](img/defba4c5a6b4fe518b55c219a9312b6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XDDsaNYZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2AaM7L3FM1cq4rPzuO) 

<figcaption>摄影:joséAlejandro Cuffiaon[Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

## 快速回顾

在本系列的第 1 部分中，我详细说明了为什么我们应该小心对待对静态全局变量的依赖，比如我们的 Kentico 代码中的`SiteContext`。

总的来说，这些静态全局变量为我们提供了对应用程序状态的访问，但是它们对应用程序如何以及在哪里运行做出了假设。

一个活跃的 ASP.NET·肯蒂科 12 MVC 应用程序与一个正在运行的单元测试项目是完全不同的，当它在单元测试中被访问时，`SiteContext`将提供你作为开发人员无法控制的值。

单元测试的目标是通过模拟应用程序状态来安排一个场景，对该场景进行操作，然后断言该操作的结果。如果我们没有能力安排初始状态，就像`SiteContext`的情况一样，我们就不能有效地测试依赖于它的代码单元。

通过对接口而不是静态全局的依赖，比如`ISiteContext`，我们可以在应用程序运行时使用真正的`SiteContext`作为接口的实现，但在测试时使用存根。

如果我们查看我们在第 1 部分中创建的`KenticoSettingConfigProvider`,我们可以看到我们已经准备好了编写测试的所有部分。
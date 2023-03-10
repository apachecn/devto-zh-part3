# 在已经进行的项目上添加单元测试

> 原文：<https://dev.to/anwar_nairi/add-unit-tests-on-a-project-already-in-progress-5539>

## 我的经历

我目前在一个项目中，我们没有使用单元测试。一年后的今天，随着问题越来越多地涉及到我们有信心的东西，我觉得是时候提出单元测试了。

你在我的经历中找到自己了吗？如果是的话，我给你以下的建议，请开始使用单元测试。

## 一个修复=一个单元测试

你的代码库已经很大了。您没有足够的人力来解析所有的方法并创建单元测试。

然而，您可以从为您修复的每个新问题创建一个单元测试开始。

```
// Fixes issue #31
it('should not allow planning a publishing date in the past', function() {
  // ...
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 渐进式单元测试的好处

*   当您创建拉动式请求时，它们会让您更加自信
*   他们通过陈述你的解决方案的预期结果来迫使你跳出框框思考
*   如果问题再次出现，当您搜索您没有想到的边缘案例时，它们会让您赢得一些时间
*   公关审查人员很快就能明白你在解决哪个案子

## 还不服气？

Pavol 还在本文中描述了对正在进行的项目进行单元测试的好处。如果你需要更多的观点，看看它。

[![rapasoft](img/ecbc6e6c686c4998deade8bfea49e278.png)](/rapasoft) [## 为什么为遗留代码编写测试是一件好事

### pavol Raj zak Aug 31 ' 173min read

#unittesting #legacycode #testing](/rapasoft/why-writing-tests-for-legacy-code-is-a-good-thing)

## 结论

在生产环境中，编码可能会令人沮丧，甚至更糟，压力更大。截止日期可以让我们走捷径，避免简单或明显的编码原则，导致可避免的问题。

我认为单元测试是正确的工具，通过确保我们在坚实的基础上构建一些代码，来帮助我们使我们的工作更愉快。

我希望这篇文章让你想开始尝试单元测试你的代码，如果你还没有的话！

来自 [Pexels](https://www.pexels.com/photo/schedule-planning-startup-launching-7376/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 的[创业股票照片](https://www.pexels.com/@startup-stock-photos?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)
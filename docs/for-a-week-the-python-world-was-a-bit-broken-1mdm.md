# 一周以来，Python 世界有点混乱

> 原文：<https://dev.to/fabiorosado/for-a-week-the-python-world-was-a-bit-broken-1mdm>

如果你懂一点 python，你可能知道由 Kenneth Reitz 创建的非常流行的库 [requests](https://github.com/requests/requests) 。

您可能已经注意到，如果您在项目中直接使用请求或者依赖于另一个使用请求的库，您的 CI 工具(Travis 或其他工具)会有所抱怨。

通过打开您的 CI 工具的日志，它们会显示如下内容:

```
Traceback (most recent call last):
  (...)
  raise VersionConflict(dist, req).with_context(dependent_req)
  pkg_resources.ContextualVersionConflict: 
      (idna 2.8 (/home/travis/build/.../.../.tox/py35/lib/python3.5/site-packages),
      Requirement.parse('idna<2.8,>=2.5'), {'requests'})
  (...) 
```

Enter fullscreen mode Exit fullscreen mode

好吧，乍一看，`requests`和`idna`之间有些问题。发生了什么事？

## IDNA 图书馆

这个图书馆是做什么用的？
根据本库自述，本库提供*【s】对 [RFC 5891](https://tools.ietf.org/html/rfc5891)* 中规定的国际化域名应用(IDNA)协议的支持，并且*还提供对 Unicode 技术标准 46 的支持。*

发生了什么事？
12 月 4 日， [IDNA 图书馆](https://github.com/kjd/idna)得到了一个更新，版本提升到了 2.8。因为请求指定一旦库得到更新，这个库就需要 2.5 到 2.7 之间的版本，Travis 很不高兴。

## 请求更新

一周后，12 月 10 日，请求得到更新，其版本升级到 2.21.0。通过此更新，请求接受新版本的 IDNA 库。

Travis 和其他 CI 工具现在再次高兴起来，你可以再次合并你的 PR 了。

## 修罗

解决这个问题很容易。只需在您的项目中指定需要的 idna 版本。这将使 Travis 推出该版本的 idna，请求的要求将不再冲突。

在您的`requirements.txt`或任何其他用于管理您的依赖项的文件上，添加以下内容将修复失败的测试。

```
idna == 2.7 
```

Enter fullscreen mode Exit fullscreen mode

您可以将此应用于任何可能存在冲突的库，直到问题得到解决，并且您能够使用最新的版本。

## 结论

这一切是否意味着 Python 的世界确实被打破了？一点也不，但是如果你在你的项目中使用请求，那么有一个星期你的 CI 工具不高兴(除非你固定 idna 版本)。

在 [opsdroid](https://github.com/opsdroid/opsdroid) 中，我们必须等到请求得到更新，这样我们就可以合并两个 pr，这本身没什么大不了的。

这一事件让我意识到项目中的依赖关系是多么重要，以及一些事情是如何在不直接是你的错或任何人的错的情况下发生的。

我很想知道你是如何跟踪你的依赖关系以及这些依赖关系的更新的。你只是合并新的更新还是预先做一些测试？

*我要感谢[@韵](https://dev.to/rhymes)向我展示了家庭助手是如何解决这个问题的。*
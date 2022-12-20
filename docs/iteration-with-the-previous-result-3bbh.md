# 用以前的结果迭代

> 原文：<https://dev.to/jessekphillips/iteration-with-the-previous-result-3bbh>

我正在使用 zip 存档，并想为我的单元测试创建一个存档。这个创建不涉及文件系统，所以我需要指定结构。

为了简化目录结构，我需要归档目录树。实际上，对我来说，看起来我没有，但这不是重点。

我把这个范围放在一起，它允许创建目录每个阶段，[和前面的](https://run.dlang.io/gist/JesseKPhillips/9fe592976261db05c7a83d4d631f74ef?compiler=dmd&args=-unittest%20-MAIN)。

```
 import std.path;
    import std.algorithm;
    static assert(r"foo/bar/foobar".pathSplitter
         .withPrevious!buildPath
         .equal([
                buildPath("foo"),
                buildPath("foo","bar"),
                buildPath("foo","bar","foobar")])); 
```

Enter fullscreen mode Exit fullscreen mode

我将路径指定为字符串，并使用 pathSplitter 来提供路径的每个部分。

我将它提供给我的 withPrevious 系列，并利用 buildPath 从组件创建一个特定于 OS 的路径。

结果等于建立在前一个零件上的每个零件。我不认为我会在我的单元测试中使用它，但是我很喜欢它所提供的东西，它并不特定于路径或字符串。
# 风暴脚本:回顾一年

> 原文：<https://dev.to/stormprograms/stormscript-year-in-review-2n21>

从我创建 [StormScript 编程语言](https://stormscript.dev)到现在已经一年多了。从那以后，我不仅修复了该语言的许多问题，还为该语言引入了许多新特性。在这篇文章中，我将详细介绍它们。

# 语法变化

## 关键字修改

最初，关键字`out`用于打印。这是一个很好的关键字，但是我觉得在这个设置中`print`会做得更多。

```
do {
    out "Hello, World!";
}end; 
```

被改为

```
do {
    printl "Hello, World";
}end; 
```

这个变化也允许有两种形式的`print`、`print`和`printl`。`printl`在打印值的末尾添加一个新行。

`@args`最初用于添加函数参数。这样做的问题是，它要求参数与函数在不同的行上。

## 的免职`end;`

最初，StormScript 使用两种方式来结束代码块。许多人在我的第一篇[帖子](https://dev.to/stormprograms/stormscript-my-thoughts-on-how-syntax-should-be-5ei0)中对此发表了评论。用户不仅要用花括号结束一个作用域，他们还必须使用关键字`end;`。这被证明是多余的，已被删除。

代码已从
更改

```
do {
    printl "Hello, World!";
}end; 
```

至

```
do {
    printl "Hello, World!";
} 
```

这不仅使长文件看起来更好，而且还节省了时间。

## 静态转动态型

StormScript 原计划是静态类型的，但在我意识到这会大大降低语言的可读性后，这种情况发生了变化。因此，

```
do {
    str name: "Ethan";
    printl "Hello, ", name;
} 
```

会变成

```
do {
    name: "Ethan";
    printl "Hello, ", name;
} 
```

## 比较

与任何编程语言一样，StormScript 有许多不同的比较运算符。虽然 StormScript 使用关键字而不是符号，但这些并没有随着时间的推移而发生太大的变化。这些关键词是`is`、`not`、`less`、`greater`、`lesseq`和`greatereq`。

# 网站

三月初，我决定 StormScript 应该迁移到自己的网站上。
T3![](img/637685f00f32677310cfce6cfce35621.png)T5】

# 未来

展望未来，我希望在年底发布 1.0.0 版本。目前来看，[0 . 7 . 0 版本](https://github.com/stormprograms/StormScript/releases/tag/v0.7.0)刚刚出来，目前正在做 0.8.0 版本。这里有一些新的东西。

## 字符串串联

对于字符串连接，我使用了符号`$`,后面是字符串中的变量名。

```
do {
    int i: randomrange => min: 0, max: 100;
    printl i, " is my favorite number";
} 
```

现在是

```
do {
    int i: randomrange => min: 0, max: 100;
    printl "$i is my favorite number";
} 
```

## 现场解读

实时解释允许运行代码并实时反映变化。这将保存变量状态以及位置。在当前的开发分支中，实时解释只在发生更改时重新运行程序。

```
$ stormscript test FILENAME 
```

我很高兴地说[运行 live interpretion](https://github.com/stormprograms/StormScript/blob/dev/src/core/live.sts)的文件完全是用 StormScript 编写的。

# 后记

请务必访问网站 [stormscript.dev](https://stormscript.dev) 并向[资源库](https://github.com/stormprograms/StormScript)投稿。

我很乐意回答你可能有的任何问题，所以请随意提问。
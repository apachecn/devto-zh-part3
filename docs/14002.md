# 搜索第一个公共版本

> 原文：<https://dev.to/gillchristian/tsearch-first-public-version-50a>

上周在这个 devlog 的第一篇文章中，我分享了我是如何想出为 TypeScript 函数编写一个搜索引擎的想法的。

从那以后，我一直致力于发布 PoC 的第一个版本。我很高兴地告诉大家，tsearch.io 从本周一开始就已经上线了。

在这篇文章中，我想分享我是如何构建这个 PoC 的，以及它的局限性。

有三个主要部分:

1.  从目录中的 ts 文件(`.ts` & `.tsx`)的导出函数中提取类型信息的 cli。
2.  对提取的函数执行搜索的节点服务器。
3.  与服务器交互的 web 客户端。

## 提取类型信息

为了提取我使用的类型 [ts-simple-ast](https://github.com/dsherret/ts-simple-ast) :

> TypeScript 编译器 API 包装。提供一种导航和操作 TypeScript 和 JavaScript 代码的简单方法。

想法很简单，用`ts-simple-ast`加载一个目录，输出所有导出函数的数组。

假设我们有模块`strs`，只有一个导出的函数:

```
// ~/code/strs/src/index.ts

export function replace(search: RegExp, replace: string, str: string) {
  return str.replace(searchValue, replaceValue)
} 
```

Enter fullscreen mode Exit fullscreen mode

输出将类似于:

```
[
  {
    name: 'replace',
    parameters: [
      {
        name: 'search',
        type: 'RegExp',
      },
      {
        name: 'replace',
        type: 'string',
      },
      {
        name: 'str',
        type: 'string',
      },
    ],
    returnType: 'string',
    module: 'some-module',
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

我没有包括任何类型的额外信息，如泛型或符号。对于更“复杂”的类型(如数组、函数、联合、泛型等)，这很重要，因为匹配不能通过比较类型名称的字符串来完成。例如，`string | number`和`number | string`是同一个类型，但是如果用名字来比较，就像我现在做的，它们就不匹配。

提取一个项目的类型的平均时间大约是 10s。这对于一个目录来说很好，但是在写这篇文章的时候，我想在所有可用的包中运行它🎉

为此，我编写了一个 [Go 程序](https://github.com/gillchristian/ts-earch/blob/f2d4b3de75a6997b3fcc3ca755be2f1ecf2673f5/packages/cli/main.go),在每个包上运行提取器，然后将所有输出一起写入一个 JSON 文件。我选择 Go 是因为并行运行和同步一切是多么容易。

在我的机器上(8 核 i7 第 7 代，16GB RAM)，在 100% CPU 使用率下运行了近 3 个小时，才从 DT 中提取出所有类型，产生了一个 5.8mb 的 JSON 文件。一点也不差，但显然在 500 兆内存和 1 核数字海洋液滴上做不到😂

## 搜索

我没有支持实际的 TS 类型签名，也没有解析它，而是想出了这个非常简单的查询语法，解析起来很简单。参数类型的逗号分隔列表、箭头(`=>`)和返回类型。缺点是它不是很强大，甚至不是对所有情况都有用，但嘿，它毕竟是一个概念验证！

```
(search: RegExp, replace: string, str: string) => string
// would be
RegExp, string, string => string 
```

Enter fullscreen mode Exit fullscreen mode

```
<A>(xs: A[]) => A
// would be
A[] => A 
```

Enter fullscreen mode Exit fullscreen mode

我仍然认为某种语法和一些逻辑缺省值(例如，作为泛型的单个大写字母)可以产生比实际有效的 ts 签名更干净和更容易的搜索方式。

```
<A, B>(array: A[], fn: ((a: A) => B)) => B[]
// vs
A[], (A => B) => B[] 
```

Enter fullscreen mode Exit fullscreen mode

但是使用有效的 TS 也有它的优点。意味着少学一样东西。粘贴一个签名也可以让™️工作

按照搜索算法，[的第一次迭代](https://github.com/gillchristian/ts-earch/blob/f2d4b3de75a6997b3fcc3ca755be2f1ecf2673f5/packages/search/src/index.ts#L24-L109)非常简单，甚至有些幼稚。我想出了一个加权函数，如果返回类型匹配，就给`compared`赋值，以及有多少类型的参数与`query`匹配。

```
weight = 0
n = query.parameters.length

if n === compared.parameters.length
  weight += m/n // m: matches by position
  if weight > 0
    weight += m/n + 1 // m/n added twice
else
  weight += m/n // m: matches in any position

if query.returnType === compared.returnType
  weight += 2 
```

Enter fullscreen mode Exit fullscreen mode

然后它挑选得分超过 0 的前 100 个。正如你所看到的，这不是一个奇特的搜索算法，但它已经为简单的搜索提供了一些匹配(例如， [`A[] => A`](https://tsearch.io/query?q=A%5B%5D%20%3D%3E%20A) ， [`number => number[]`](https://tsearch.io/query?q=number%20%3D%3E%20number%5B%5D) )。

我没有提到的一件额外的事情是，如果搜索查询是一个单词，它将通过函数和类型名称进行搜索。这个想法直接来自胡戈。

## 接下来是什么？

下面是我接下来要应对的一些挑战:

*   订阅 DT repo 并仅在已更改的包上运行提取器。
*   创建发布自己类型的 TS 项目的管理列表，并更新提取器来运行它们。
*   提取函数类型的更多信息。
*   改进查询语法和/或支持有效的 TS 签名。
*   使 TS 语义成为搜索算法的一部分。

当然还有很大的改进空间，但我很高兴人们已经对这个想法感兴趣并开始尝试。这正是在早期阶段将项目公之于众的目标。

对于那些愿意做出贡献的人，我在回购中创建了 [`help wanted`](https://github.com/gillchristian/ts-earch/labels/help%20wanted) 期，并将很快添加更多。

我期待得到您的反馈、意见和建议。

[直到下一次！](https://www.youtube.com/watch?v=-O_cOnGrpY8)
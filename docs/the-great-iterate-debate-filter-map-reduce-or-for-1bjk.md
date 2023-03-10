# 伟大的迭代辩论:过滤/映射，减少，还是为了？

> 原文：<https://dev.to/cmborchert/the-great-iterate-debate-filter-map-reduce-or-for-1bjk>

> 最初发布于[我的博客](https://cborchert.blog/the-great-iterate-debate-filter-map-reduce-or-for)

## [T1】简介](#intro)

想象一下下面这个相对常见的场景:我有一个数组，我需要从中挑选出特定的项并将其转换成新的格式。

我注意到，当需要从一系列对象中生成一系列组件时，这个问题在 React land 中尤其频繁出现。

### 举个例子

例如，想象一下，给定一组用户对象，我们只需要获取已经验证了其帐户的用户，并创建他们的电子邮件列表:

用户列表:

```
const users = [
  { name: 'alex', isVerified: true, email: 'alex@mail.net' },
  { name: 'bobby', isVerified: false, email: 'bobby@mail.net' },
  { name: 'charlie', isVerified: true, email: 'charlie@mail.net' },
  { name: 'dannie', isVerified: false, email: 'dannie@mail.net' },
  { name: 'elliot', isVerified: false, email: 'elliot@mail.net' },
  { name: 'finn', isVerified: true, email: 'finn@mail.net' },
] 
```

我们想要下面的数组作为结果:

```
['alex@mail.net', 'charlie@mail.net', 'finn@mail.net'] 
```

### 一个直观的例子

对于那些喜欢视觉效果的人来说，假设我们有一个带有形状和数值的对象，我们想要一个包含所有方块中的数字的数组:

[![creating an array of numbers from a collection of shapes](img/a461039f788da4b75b33a6ecd0c75b07.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5-PS_Zwg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/w81pbfqfnnarp6piabut.jpg)

## 三种常见的路径

你如何从上面的例子中达到目标？

我遇到了三种处理这种情况的主要模式:使用`for`循环、`filter`使用数组，然后`map` ping 结果，或者使用`reduce` r。让我们仔细看看这些方法中的每一种，使用第一个示例中的`users`对象，目标是得到一个电子邮件地址数组作为结果。请注意，在代码中，我将尽力使代码*易于理解*，因此我将跳过析构参数和挑剔每个字符。毕竟，这不是代码高尔夫😉！

### 为循环

#### 代码

```
const users = [
  { name: 'alex', isVerified: true, email: 'alex@mail.net' },
  { name: 'bobby', isVerified: false, email: 'bobby@mail.net' },
  { name: 'charlie', isVerified: true, email: 'charlie@mail.net' },
  { name: 'dannie', isVerified: false, email: 'dannie@mail.net' },
  { name: 'elliot', isVerified: false, email: 'elliot@mail.net' },
  { name: 'finn', isVerified: true, email: 'finn@mail.net' },
]

// initialize array
const userEmails = []
// loop through each item, pushing their email to the list if the user isVerified
for (let i = 0; i < users.length; i++) {
  if (users[i].isVerified) {
    userEmails.push(users[i].email)
  }
}

console.log(userEmails)
// [ 'alex@mail.net', 'charlie@mail.net', 'finn@mail.net' ] 
```

啊，`for`循环:大多数编程语言的基石之一。简单百搭！这里没有太多要解释的，如果你已经用任何语言做过一些编程，你应该能明白这里发生了什么。我们正在初始化一个新的数组，并将元素放入其中。

#### 优点

*   到目前为止最快的计算选项(参见下面关于性能的部分)。
*   相对可读性(我给它打 3/5 的分:如果你读了代码，你会看到发生了什么，因为我们没有做任何花哨的事情)。

#### 弊

*   详细(96 个字符的精简长度)
*   容易出错:尽管代码本身很简单，但这里有更多的活动部分。你很容易犯一个错误，比如写`for (let i = 0; i <= users.length; i++)`，但不一定意识到这一点。
*   这不时髦。我的意思是，我们已经有了 ES6 数组方法，就像，永远！我们为什么要改变任何变量的值呢？(虽然这是真的，但这意味着讽刺。)

### 滤镜+贴图

```
const users = [
  { name: 'alex', isVerified: true, email: 'alex@mail.net' },
  { name: 'bobby', isVerified: false, email: 'bobby@mail.net' },
  { name: 'charlie', isVerified: true, email: 'charlie@mail.net' },
  { name: 'dannie', isVerified: false, email: 'dannie@mail.net' },
  { name: 'elliot', isVerified: false, email: 'elliot@mail.net' },
  { name: 'finn', isVerified: true, email: 'finn@mail.net' },
]

const userEmails = users
  .filter(user => user.isVerified) // keep if user.isVerified is truthy
  .map(user => user.email) // for each filtered user, return the email

console.log(userEmails)
// [ 'alex@mail.net', 'charlie@mail.net', 'finn@mail.net' ] 
```

我不打算在这里隐藏什么。这是我心目中的赢家(除非你真的想通过减少微秒来提高应用程序的性能。

#### 优点

*   计算速度第二快的选项(参见下面关于性能的部分)。
*   *可读性很强(我给它打 5/5 的分:一眼看去，我们知道我们正在从一个数组中`filter`移除一些项，然后`map` ping(转换)剩余的项。我们知道，在操作执行之后，我们将得到一个数组。).*
*   最不冗长(63 个字符的精简长度)
*   很难搞砸。出于同样的原因，使这些代码易于阅读，这些代码很难搞砸。为胜利进行小规模连锁操作！

#### 弊

*   我会等待有人来纠正我，但我会说“没有”，除非你支持 IE10 或其他东西。

### 减少

```
const users = [
  { name: 'alex', isVerified: true, email: 'alex@mail.net' },
  { name: 'bobby', isVerified: false, email: 'bobby@mail.net' },
  { name: 'charlie', isVerified: true, email: 'charlie@mail.net' },
  { name: 'dannie', isVerified: false, email: 'dannie@mail.net' },
  { name: 'elliot', isVerified: false, email: 'elliot@mail.net' },
  { name: 'finn', isVerified: true, email: 'finn@mail.net' },
]

const userEmails = users.reduce((accumulator, user) => {
  if (user.isVerified) {
    return [...accumulator, user.email] // return the previous array plus the new user email
  }
  return accumulator // otherwise return the array without changes
}, []) // initialize accumulator as an empty array

console.log(userEmails)
// [ 'alex@mail.net', 'charlie@mail.net', 'finn@mail.net' ] 
```

如果你的直觉是去拿减压器，你对我来说太酷了。虽然`reduce`功能多样，但我发现它不直观，对初级开发人员和代码审查人员不友好，而且通常不是这种操作的最佳工具。

#### 优点

*   第二不详细(71 个字符的最小长度)
*   超级潮。我不知道为什么，但是当你使用一个`reducer`的时候，你突然达到了一个新的水平。

#### 弊

*   三个选项中计算速度最慢的一个(参见下面关于性能的部分)。
*   可读性不强(我给它打 2/5 的分:每次我遇到 reduce，就像 for 循环一样:我需要阅读每一行来理解操作；返回值不一定是数组(您可以使用 reducer 来获得字符串或布尔值或任何您想要的东西:它的灵活性使它看起来更难理解)。此外，我只是不喜欢 reduce 的初始值出现在 reducer 函数之后。我觉得这很不直观。).

## 表现

这篇文章实际上来自于我在工作中参与的一次代码审查和随后的辩论，当时有人写了一些技术上不正确的代码(他们只使用了一个`map`，并且没有`filter`删除不需要的项目)。一般来说，我们倾向于使用 reduce 来处理任何事情，我主张使用`.filter().map()`。我最终编写了一个 JSPerf 测试[,概述了这里讨论的操作和其他一些操作之间的速度差异。结果让我大吃一惊:`.filter().map()`的表现是`.reduce`的两倍，`for`是`.filter().map()`的三倍！](https://jsperf.com/filtermap-vs-reduce-vs-for-vs-function-vs-method)

[![Performance comparison as a bargraph](img/36e51abda04f9ebdebc219b286fb379e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ipGmLSGc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2o5ge17mv6w2yzs3hpwd.png)

我对结果感到惊讶，特别是，正如我的一个同事指出的，“我们用过滤图对数据循环了两次，而用 reducer 只循环了一次。”结果可能会因你编写函数的方式而异，reduce 最终会比 filter-map 更快，但不会更快。至于`for`循环，我并不意外它是赢家，只是没想到它赢了这么多。事实证明，反复调用 reduce 函数是相当耗时的，正如本文中的[所简要描述的那样，但是有些细节我已经搞不懂了。如果有人能解释为什么会这样，我很想听听。](https://hackernoon.com/3-javascript-performance-mistakes-you-should-stop-doing-ebf84b9de951?gi=fc581d91f3a0)

## 裁决

虽然性能结果很有趣，但我们讨论的是最终节省的微秒数，而且，尽管在某些情况下坚持这些节省可能是有意义的，但我不认为节省的时间值得编写更多迟钝的代码。出于这个原因，我建议使用友好的、自我记录的、难以出错的`.filter().map()`来完成这个特定的操作。

郑重声明，我认为 reduce 可能仍然有它的位置，但它不会取代`.filter().map()`。实际上，我很难说它应该取代任何给定的`for`循环，因为它和 for 循环一样具有描述性，而且更慢。对我来说，`reduce`在你可以创建应用程序中需要的可重用的 reducer 函数时大放异彩，你只需要加入`myArray.reduce(myReducer, [])`。如果有人能指出为什么我们应该更多地使用 reduce，我会很高兴！

*封面图片致谢:*
照片由[哈维尔·阿莱格·巴罗斯](https://unsplash.com/photos/C7B-ExXpOIE?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/choice?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄
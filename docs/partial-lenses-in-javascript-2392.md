# Javascript 中的部分镜头

> 原文：<https://dev.to/vageez/partial-lenses-in-javascript-2392>

> [从 partial . lens](https://github.com/calmm-js/partial.lenses)
> 镜头基本上是一种抽象，用于同时指定更新和查询不可变数据结构的操作。镜头是高度可组合的，并且可以是高效的。部分透镜可以查看可选数据、插入新数据、更新现有数据和移除现有数据，并且可以例如提供默认值和维护所需的数据结构部分。

用 javascript 处理大型嵌套数据集可能很麻烦。

使用 Lenses 可以以干净、高效和不可变的方式获取和设置一些深层嵌套数据。只需一行代码，我们就可以安全地获取、更新或设置一个深度嵌套的项目，并通过更改创建一个新的 JSON 对象。

这篇文章的目的是展示一些使用镜头和普通 JS 的例子。这是我们将要使用的数据集。

```
const user = {
 name: 'Roger',
 phone: {
   cell: '212-334-1212'
 },
 address: {
   street: '123 First Ave',
   zip: '90210'
 },
 car: {
   daily: [
     {
       make: 'Jeep',
       year: 2013
     },
     {
       make: 'Mazda',
       year: 2018
     }
   ],
   weekend: [
     {
       make: 'Corvette',
       year: 1993
     }
   ]
 }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看如何从这个数据集中提取一些数据。这个例子将显示普通的 js 和镜头。
虽然这两个例子看起来都很简洁，但普通的例子容易出现运行时错误。对于普通的例子，如果没有找到任何元素，我们将得到一个运行时错误。

不过在这个特殊的例子中，两个实例都将返回`Jeep`。

### 得到

```
import * as L from 'partial.lenses'

L.get(['car', 'daily', 0, 'make'], user) // Jeep

user.car.daily[0].make // Jeep 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果我们试图访问一个不存在的记录或字段，会发生什么呢？

```
L.get(['car', 'daily', 3, 'make'], user) // undefined

user.car.daily[3].make // Cannot read property 'make' of undefined 
```

Enter fullscreen mode Exit fullscreen mode

我们的普通例子将触发一个运行时错误。为了达到和镜头一样的安全水平，我们需要写一些更强大的东西。

见下文。

```
// GROSS
if(user && user.car && user.car.daily && user.car.daily.length > 2) {
   return user.car.daily[3].make
} else {
   return undefined
} 
```

Enter fullscreen mode Exit fullscreen mode

目前为止印象深刻吧。

与我们向下钻取时检查每个字段的命令式代码相比，我想说我们在使用镜头时获得了相当大的净收益。它简单明了，也更容易推理。

### 默认值

默认值为我们提供了为缺失的元素设置默认值的功能。

```
L.get(['phone', 'home', L.defaults('Number not found')], user) // Number not found 
```

Enter fullscreen mode Exit fullscreen mode

如果该值不缺失，`L.defaults`将被忽略。很可爱。

### 设定

好吧，让我们设置一些数据。假设我们想在周末车手阵容中加入一款新车。

```
L.set(['car', 'weekend', L.appendTo], {make: 'Ford', year: 1979}, user)
// Returns the entire User object with the new car record 
```

Enter fullscreen mode Exit fullscreen mode

注意`L.appendTo`。我要说这是不言自明的。简而言之，这将保留在`car.weekend`中找到的任何现有记录，并简单地追加我们的新记录。

一些你可能会感兴趣的东西。

假设我们的对象没有`car`和`weekend`元素。我们上面的镜头将创建和设置`car.weekend`以及将新车记录添加到周末数组中。什么？！？！

没错，伙计们。你得试着去相信它。

我应该指出，我们实际上只是触及了表面。当我第一次接触到镜片时，我很难理解为什么这么少人，至少在我的圈子里，没有听说过镜片。

我想再补充一个例子！让我们结帐`collect`

### 收藏

安全地遍历数组。

```
L.collect(['car', 'daily', L.elems, 'make'], user) // [ 'Jeep', 'Mazda' ] 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们深入到`car.daily`。
`L.elems`代表我们遍历过的元素。
和最后的`make`是我们正在寻找的元素。

顾名思义，collect 收集结果并以数组的形式返回给我们。

如果 collect 中的任何元素不存在，我们将简单地得到一个空数组。

怎么样？

:]

你得检查一下[部分镜头](https://github.com/calmm-js/partial.lenses)
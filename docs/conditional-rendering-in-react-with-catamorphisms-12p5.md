# 与变形反应中的条件渲染。

> 原文：<https://dev.to/vageez/conditional-rendering-in-react-with-catamorphisms-12p5>

你是否曾经看着你的 React 组件，对自己说，一定有更好的方法来处理我的组件中的这些条件。

当我第一次接触函数式编程时。强加的“规则”之一是永远不要使用`if / else / else if`。

这给我提出了一个大问题。我如何能管理这个。此外，我如何在我的 React 组件中管理这一点？

让我们首先看一个例子来说明我所说的。下面是一个检查变量然后返回正确分量的例子。

使用 switch 语句也可以获得相同的结果。

```
 import React from 'react'
import Doberman from './Doberman'
import Chihuahua from './Chihuahua'
import BullDog from './BullDog'

const Dog = breed => {

    if(breed === 'Doberman') {
        return <Doberman />
    } else if (breed === 'Chihuahua')
        return <Chihuahua />
    } else {
        return <BullDog />
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

那么，这有什么错呢？

在我看来，它很丑。

这不安全。可以是不确定的或者更好的其他品种的狗。

此外，在这个例子中，我们将逻辑添加到我们的 UI 组件中，这对于测试来说是一个挑战。

所以让我们来谈谈变形是如何帮助我们以不同的方式处理这些情况的。

## 变形

> 摘自维基百科
> 在函数式编程中，变形提供了列表折叠到任意代数数据类型的一般化

对于函数式编程新手来说，fold 也可以称为 reduce 或 aggregate。

假设我们的应用程序需要确定狗的品种，然后将相应的组件呈现给用户。为了实现一个变形，我们需要识别所有我们期望的狗的品种。

这是我们列表中的一个例子，它将支持变形。

```
 import daggy from 'daggy'

const DogBreed = daggy.taggedSum('DogBreed', {
    Doberman : [],
    Chihuahua : [],
    BullDog : [],
    Unknown : []
}) 
```

Enter fullscreen mode Exit fullscreen mode

[点击此处查看 daggy】](https://github.com/fantasyland/daggy)

我们的应用程序需要定义一个初始状态。在初始状态中，我们会指定我们的狗的品种。让我们看一看...

```
 const INITIAL_STATE = {
    dog : {
        breed : DogBreed.Unknown
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我们的应用程序还没有加载，而且我们不知道我们的狗是什么品种，所以我们创建了一个`Unknown`品种。

在应用程序生命周期的某个时候，我们会设置狗的品种。

让我们来看看这个使用超级酷的 FP 库 Pratica 设置我们的狗的品种的例子。

```
 import { Ok, Err } from 'pratica'
import daggy from 'daggy'

const DogBreed = daggy.taggedSum('DogBreed', {
    Doberman : [],
    Chihuahua : [],
    BullDog : [],
    Unknown : []
})

// DogBreed.is 
// A useful built in type check when using daggy.
const isValidBreed = breed => DogBreed.is(breed) ? Ok(breed) : Err()

// Safe function with no side effects. 
export const getBreed = dog => Ok(dog)
    .chain(dog => Ok(dog.breed))
    .map(breed => breed === 'Doberman' ? DogBreed.Doberman : breed)
    .map(breed => breed === 'Chihuahua' ? DogBreed.Chihuahua : breed)
    .map(breed => breed === 'BullDog' ? DogBreed.BullDog : breed)
    .chain(isValidBreed)
    .cata({
        Ok: breed => breed,
        Err: () => DogBreed.Unknown
    }) 
```

Enter fullscreen mode Exit fullscreen mode

让我花一点时间来谈谈这里正在发生的事情。
我使用`Ok`单子来检查我们的狗对象。

1.  我们把我们的狗放进我们的`Ok monad`

2.  下一步，我们`chain`链允许我们打开 Ok 单子。
    a .然后设置另一个 Ok 单子检查`dog.breed`。如果`dog.breed`未定义，我们的 Ok 单子将返回 Err，并将直接传递给我们的`cata.Err`，在那里我们设置了`DogBreed.Unknown`。

3.  然后我们将它传递给一个`map`。Map 接受我们的`chain`、我们的`OK Monad`的输出。
    a. Map 拿一个`OK monad`打开它，检查它，然后再把它包回我们的`Ok monad`
    b .我们映射每一个可能的品种类型。如果我们找到匹配的，我们就设定我们的品种。如果不是，我们返回到下一张地图。

4.  我们最后一次检查，`.chain(isValidBreed)`。
    答:我们为什么需要这个？如果品种不是我们期望的品种，我们需要处理这种情况并遵从`Err()`，它将默认为`DogBreed.Unknown`
    b。我们将上述映射的结果链接到一个函数`isValidBreed`
    c。is valid breed 对品种进行检查。如果是 DogBreed 类型的，我们会返回一个 Ok 单子。如果没有，我们返回 Err()

太好了。

这里，我们展示了调用实现了`pratica`的安全`getBreed`函数。

```
 // a contrived example of updating our state...
const dog = { breed: 'Chihuahua' }

const state = {
    dog : {
        breed : getBreed(dog) // DogBreed.Chihuahua
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在已经准备好看到这一点。让我们记住，我们的目标是在 react 组件中使用 if / else 的替代品。

我们将把`breed`传递给我们的狗组件。`breed`现在是我们`daggy`的一个实例`DogBreed`。这意味着我们可以对它应用一种变形。`cata`将解析为当前类型。

```
 import React from 'react'
import Doberman from './Doberman'
import Chihuahua from './Chihuahua'
import BullDog from './BullDog'

// Lets remember here that breed is the result of `getBreed(dog)` which is a List we can apply a catamorphism on.
const Dog = breed => breed.cata({
    Doberman  : () => <Doberman />,
    Chihuahua : () => <Chihuahua />,
    BullDog   : () => <BullDog />,
    Unknown   : () => <div>{'Unknown breed'}</div>, }) 
```

Enter fullscreen mode Exit fullscreen mode

Daggy 还支持参数的传递，这也是非常有趣的。点击此处查看 daggy。
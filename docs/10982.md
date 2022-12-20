# Go 的地图迭代顺序没那么随机？

> 原文：<https://dev.to/wallyqs/gos-map-iteration-order-is-not-that-random-mag>

我已经使用围棋大约 5 年了，所以现在我已经将地图上的行为`for range`内化为[随机](https://news.ycombinator.com/item?id=7655948)。Go 的`for range`如何在地图上工作的这种可观察到的行为，自然会导致如下代码在我看来是正确的:

```
package main

import (
    "fmt"
)

func pickRandom(entries map[string]int) string {
    var key string
    for k, _ := range entries {
        // Chose the random key
        key = k

        // Mark that it has been used
        entries[k]++
        break
    }
    return key
}

func main() {
    entries := make(map[string]int)
    entries["A"] = 0
    entries["B"] = 0
    entries["C"] = 0

    for i := 0; i < 10000; i++ {
        pickRandom(entries)
    }

    for k, v := range entries {
        fmt.Printf("key=%s hit=%v\n", k, v)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

所以当我看到下面的结果不断被重复时，我感到非常惊讶:

```
key=A hit=7511
key=B hit=1279
key=C hit=1210 
```

Enter fullscreen mode Exit fullscreen mode

看起来不管运行多少次，地图的第一个条目将总是获得至少 7000 个命中😱

解决这个问题的一个方法是用密钥创建一个额外的数组，然后使用 rand 来获得一个随机数组，例如使用`math/rand` :

```
package main

import (
    "fmt"
    "math/rand"
)

func pickRandom(entries map[string]int) string {
    var key string
    choices := []string{}
    for k, _ := range entries {
        choices = append(choices, k)
    }

    // Choose random one and mark that it was hit
    key = choices[rand.Intn(len(choices))]
    entries[key]++

    return key
}

func main() {
    entries := make(map[string]int)
    entries["A"] = 0
    entries["B"] = 0
    entries["C"] = 0

    for i := 0; i < 10000; i++ {
        pickRandom(entries)
    }

    for k, v := range entries {
        fmt.Printf("key=%s hit=%v\n", k, v)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在结果看起来好多了！

```
key=A hit=3254
key=B hit=3387
key=C hit=3359 
```

Enter fullscreen mode Exit fullscreen mode

有人指出，这可能是一个很好的例子，说明了[海勒姆定律](http://www.hyrumslaw.com/) :

```
With a sufficient number of users of an API,
it does not matter what you promise in the contract:
all observable behaviors of your system
will be depended on by somebody. 
```

Enter fullscreen mode Exit fullscreen mode

我们最终意外地依赖于 Go NATS 客户端中的`for range`行为，这导致了一个服务器经常比服务器池中的其他服务器拥有更多连接的不平衡(这个问题已经被[修复](https://github.com/nats-io/go-nats/pull/445/files))。
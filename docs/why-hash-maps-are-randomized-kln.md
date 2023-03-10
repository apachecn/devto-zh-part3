# 为什么哈希表是随机的

> 原文：<https://dev.to/aghost7/why-hash-maps-are-randomized-kln>

我最近在我的一个业余项目中实现了一个散列表(hash table ),只是为了好玩，并了解了一个我没有意识到的关于安全性的有趣的花絮。我想我应该分享一下！

## 哈希映射如何工作

其核心是，哈希映射背后的思想是使用[哈希函数](https://en.wikipedia.org/wiki/Hash_function)将给定的键(例如，字符串)转换成索引值。然后，索引将确定数据在数组中的位置。插入、删除和读取有一个[常数时间](https://en.wikipedia.org/wiki/Time_complexity#Constant_time)复杂性(最好的情况😉).这意味着无论你的 hash 图中有多少元素，你的查找时间都不会改变。

但是，由于我们是将信息压缩到一个整数中，因此可能会发生冲突。解决方案是使用另一个数组(一个桶),在我们的 hash map 的数组中，来保存所有的冲突。

我将跳过一些细节，但是为了得到一个概念，这是一个哈希映射实现的大概样子:

```
class HashMap
    constructor() {
        this.buckets = new Array(1000) // arbitrary
    }
    set(key, value) {
        const index = hash(key)
        const bucket = this.buckets[index]
        if(!bucket) {
            this.buckets[index] = [{
                key: key,
                value: value
            }]
        } else {
            const item = bucket.find(item => item.key === key)
            if(item) {
                item.value = value
            } else {
                bucket.push({
                    key: key,
                    value: value
                })
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 拒绝服务

拒绝服务泛指服务被暂时或永久关闭的攻击。

通常，实现这一点的方法是在服务器端重复触发一个非常昂贵的操作。

## 攻击

如上所述，当发生碰撞时，更多的物品将被放入同一个桶中。如果这种情况发生的次数足够多，那么散列图的性能将开始看起来更像是一个键和值的顺序数组。换句话说，复杂性将变成线性的。

根据哈希映射的性能有多重要，您的服务器资源可能很容易被占用。

## 解

对于攻击者来说，要在我们的散列图中引起冲突，他需要提前知道什么会引起冲突。我们的解决方案使攻击者几乎不可能猜出正确的组合键，从而导致足够多的冲突，影响服务器的性能。

一些散列函数允许为散列指定种子。创建散列图时，我们生成随机种子。这意味着每个哈希映射实例将把相同的键转换成不同的索引。如果它是一个密码安全的随机数生成器，我们知道攻击者将无法猜出它。

解决方案的大致思路如下:

```
class HashMap
    constructor() {
        this.seed = random()
        this.buckets = new Array(1000) // arbitrary
    }
    set(key, value) {
        const index = hash(this.seed, key)
        const bucket = this.buckets[index]
        if(!bucket) {
            this.buckets[index] = [{
                key: key,
                value: value
            }]
        } else {
            const item = bucket.find(item => item.key === key)
            if(item) {
                item.value = value
            } else {
                bucket.push({
                    key: key,
                    value: value
                })
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode
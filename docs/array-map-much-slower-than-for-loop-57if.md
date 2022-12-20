# Array.map()比 for 循环慢得多

> 原文：<https://dev.to/henryjw/array-map-much-slower-than-for-loop-57if>

当我突然想测试哪种方法更快时，我正在处理一些编码挑战。对于 100 万个数字的输入量，`Array.map()`大约需要 2000 毫秒，而`for`循环大约需要 250 毫秒。知道为什么吗？

```
const INPUT_SIZE = 10 ** 7
const input = []
for(let i = 1; i <= INPUT_SIZE; i++) {
    input.push(i)
}

const pow2 = value => value ** 2

console.time('map')
const mapResult = input.map(pow2)
console.timeEnd('map') // 1800-2000+ ms

console.time('loop')
const arrayResult = []
for(let i = 0; i < input.length; i++) {
    arrayResult.push(pow2(input[i]))
}
console.timeEnd('loop') // 200-300ms 
```

Enter fullscreen mode Exit fullscreen mode

我一直认为`Array.map()`会更快，因为它是一个内置函数，但看起来我错了。

## 测试 2

所以想了一会儿，我决定进行一个更公平的比较:`Array.forEach()` vs `for`循环。结果是，`Array.forEach()`仍然较慢，但没有`.map()`慢那么多(550-700 毫秒)。
我猜`.map()`执行了一些额外的逻辑，与原始的`for`循环相比，它明显慢了下来。

编辑:我知道这不是一个实际的场景，因为我们不应该使用 Javascript 处理这么多数据。这个场景是为了理论上的理解和讨论。
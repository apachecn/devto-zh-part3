# 代码日 1 的到来——Javascript while 循环滥用

> 原文：<https://dev.to/thomasjunkos/advent-of-code-day-1---javascript-while-loop-abuse-lbc>

昨天我找到一点时间，开始一年一度的 AOC。

是的，我知道，我参加聚会要迟到了。但是嘿:迟到总比不到好。

所以，第一部分是一个简单的热身，只是一个阵列。第 2 部分有点棘手。我想出了这个——不可否认——有点“不干净”的解决方案。

哦，不管怎样，我觉得它还是值得一读的。

```
const findDoubleFrequency = array => {
  const doubles = new Set();
  doubles.add(0);
  let acc = 0;
  while (
    !array.find(x => {
      acc += x;
      if (doubles.has(acc)) {
        return true;
      }
      doubles.add(acc);
      return false;
    })
  ) {}
  return acc;
}; 
```

Enter fullscreen mode Exit fullscreen mode
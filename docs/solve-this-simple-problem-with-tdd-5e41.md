# 用 TDD 解决这个简单的问题

> 原文：<https://dev.to/delbetu/solve-this-simple-problem-with-tdd-5e41>

几天前我试图用 TDD 解决下一个问题，发现真的很难。我无法通过一系列小的增量测试用例找到解决方案。
这就是问题:

```
flatten an array of arbitrarily nested arrays of integers into a flat array of integers.
e.g. [[1,2,[3]],4] -> [1,2,3,4]. 
```

Enter fullscreen mode Exit fullscreen mode

当然没有使用像 ruby 中的`flatten`这样的语言实现

### 有人能尝试解决这个问题并在这里评论她的/他的经历吗？

### 澄清

我遵循的流程。
1-写一组测试(输入- >期望-输出)
2-挑选最简单的测试用例求解。(红色)(写一个失败的规范)
3-写最简单的代码变更来解决当前的一组测试。(绿色)
4-检查是否需要干燥(重构)
5-回到 2

我的期望是，在某一点上，我得到了一般情况下的解，但是按照这些规则，我不能得到解。在某些时候，没有简单的改变来解决测试，我需要重写整个解决方案。
我找到了这个问题的两个解决方案，但没有一个遵循这个过程。
T3】https://gist . github . com/del betu/ff 67 be 230 f 83542 AC 1924 e 766 b 591803

# 更新

我找到了解决这个问题的方法，看看这个[https://dev.to/delbetu/a-tdd-example-e4o](https://dev.to/delbetu/a-tdd-example-e4o)
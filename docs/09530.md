# 使用 Ruby 求解自然数之和。

> 原文：<https://dev.to/yongliang24/use-dynamic-program-to-solve-the-sum-of-natural-numbers-using-ruby-5ee1>

给定一个自然数范围，例如从 1 到 100，求所有给定数字的和。下面，我们将实现一些解决方案，然后在一小组有序的数字上解决这个问题。

### 任务:

求从 5 到 100000000 (1 亿)的所有自然数的和

### 解决方案 1

这个解决方案将使用一个循环来迭代并将每个数字相加，以获得总和。

[https://repl.it/@YongLiang/IncompatibleWholeGoal?lite=true](https://repl.it/@YongLiang/IncompatibleWholeGoal?lite=true)

## 卡尔·弗里德里希·高斯

在 18 世纪，有一位著名的数学家名叫高斯，他发现了一种模式来计算一系列数字的总和，比如 1 到 100。公式是这样的:

s = n(n+1) /2，其中 s 是总和，n 是最后一个数，1 是第一个数。

### 
解决方案 2

[https://repl.it/@YongLiang/SilverSpanishFact?lite=true](https://repl.it/@YongLiang/SilverSpanishFact?lite=true)

### 此解决方案的运行时间以毫秒为单位！！

##### 这里发生了什么！！，只有一行代码，输出与循环解决方案相同。

# 更有效的解决方案

#### 为了更好地理解这一点，让我们用一小组数字来解决这个问题:1 到 10。

#### 在 1 到 10 的情况下，我们可以将其分成 5 对，如下所示:

# (1+10), (2+9), (3+8), (4+7), (5+6)

### 注意，每一对的和是相等的，如果我们把一对乘以 5，我们得到的和是 1 到 10。

#### 为了解决这个问题，我们可以说:

#### 1.得到一对的和-(最小值+最大值)。

#### 2.得到对数-长度/2。

#### 3.将步骤 1 和步骤 2 相乘得到总和。

[https://repl.it/@YongLiang/UntidySecondhandGlitch?lite=true](https://repl.it/@YongLiang/UntidySecondhandGlitch?lite=true)
Notice: If you are curious and play around with different number sets, please convert int to float to avoid the miscalculation with odd-length sets. Exp:
puts final_solution(5, 100000000).to_f

### 当我们想求所有能被 3 或 5 或两者都整除的数的和时，这种模式也适用。
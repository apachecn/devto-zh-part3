# 用例子解释 Big-O，初学者指南。

> 原文：<https://dev.to/yongliang24/some-big-o-examples-beginners-guide-lk4>

# 概观

Big-O 符号用于描述程序的时间复杂度(性能)和空间复杂度(内存开销)。把每个代码块想象成你的计算机的一个任务。

我们可以用 Big-O 用一个符号来评估每个任务。很像你的代码的分级系统。A++、A、B、C 等。Big-O 使用 O(1)、o(n)、O(n)等符号。

## 恒定时间复杂度 O(1) -等级:A++

当增加或减少输入的值对输出速度没有影响时，Big-O 使用复杂度 O(1)来描述常量运行时，因为这个代码块总是需要一个执行步骤才能完成。O(1)是 A++。示例:

[https://repl.it/@YongLiang/QuietSlateblueIntroductory?lite=true](https://repl.it/@YongLiang/QuietSlateblueIntroductory?lite=true)

## 注意:

尽管第二个例子有多行代码，但是每个块仍然有恒定的运行时间。所以除非复杂度改变，否则 Big-O 会保持简单，把这个方法的复杂度称为 O(1)，因为 Big-O 只关心复杂度，不关心代码行数。

## 线性时间复杂度 O(n) -等级:A

O(n)通常用于描述单回路操作。如果输入的是我需要打印出来的项目。有 10 个项目意味着循环将需要 10 个步骤来打印所有项目；100 个项目需要循环中的 100 个步骤。计算机需要完成一个任务，然后才能进入下一个任务，考虑一个循环是一个单一的任务。这个的复杂度是线性的 O(n)。

[https://repl.it/@YongLiang/StimulatingZigzagQuadrant?lite=true](https://repl.it/@YongLiang/StimulatingZigzagQuadrant?lite=true)

## 注意:

注意，在上面的例子中，有两个循环，但是它们没有嵌套，这里有两个任务。每个循环被认为是 O(n)，因此该方法的复杂度仍然是 O(n)。

## 平方时间- O(n ) -等级:B

[https://repl.it/@YongLiang/WarpedTraumaticTransformation?lite=true](https://repl.it/@YongLiang/WarpedTraumaticTransformation?lite=true)
O(n²) is typically used to describe nested loops. Any nested loops is just one single task. If 3 loops are nested, the complexity would be O(n^3) and so on.

# 最后的想法

Big-O 主要用于描述数据结构和算法。其他 Big-O 符号如 O(2^n)用于描述斐波那契数的递归计算。O(log N)用于描述二分搜索法。

Big-O 也使用相同的符号来描述空间复杂性。空间复杂度不是决定性能，而是关注程序分配内存的次数，比如创建数组、散列、变量等等。

如果一个方法使用一个循环来打印 10 个字符串，时间复杂度是 O(n)，但是空间复杂度是 O(1)常数，因为没有分配内存。
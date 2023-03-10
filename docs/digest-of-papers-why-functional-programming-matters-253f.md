# 论文摘要:为什么函数式编程很重要

> 原文：<https://dev.to/gustavo94/digest-of-papers-why-functional-programming-matters-253f>

[D .特纳，Addison-Wesley，1990](https://www.cs.kent.ac.uk/people/staff/dat/miranda/whyfp90.pdf)
*[用西班牙语阅读此贴](https://dev.to/gustavo94/repasando-artculos-monads-for-functional-programming---c4e)*

为了获得高质量和可维护的代码，模块化和可读性是代码的两个最重要的特性，但是编写模块化代码会产生对“粘合代码”的需求，这有时会产生副作用并降低可读性。函数式编程使用高阶函数和惰性求值，以改善甚至避免这种“胶水代码”，并通过引用透明性来保证可读性。

## 参照透明

FP 的功能与数学概念共享它们的定义如下:“从一组输入到一组可能的输出的关系，其中每个输入恰好与一个输出相关”，因此没有副作用！。此外，这允许我们用返回值替换函数调用，而不必查看函数的整个上下文，并创建更多可重用和易于测试的“模块”。

## 高阶函数

如果一个函数返回另一个函数或者有函数作为输入，它被认为是一个高阶函数，在一个更软件相关的方法中，它也被称为一等公民。

使用高阶函数，我们可以定义一些适用于多种数据类型的抽象行为，并且需要更少的“粘合代码”。

VAVR 中集合的 foldLeft 函数是如何工作的一个简单例子:

这些例子使用了一个名为[VAVR](https://www.vavr.io)T3
的 JAVA 库

```
 public static void main(String[] args) {
      List<Integer> list = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9);
      Integer r = list.foldLeft(0, Integer::sum); // 45
  }

  //source code of VAVR 
  default <U> U foldLeft(U zero, BiFunction<? super U, ? super T, ? extends U> f) {
        Objects.requireNonNull(f, "f is null");
        U xs = zero;
        for (T x : this) {
            xs = f.apply(xs, x);
        }
        return xs;
    } 
```

## 懒评

因为我们在 FP 中没有副作用，所以可以在运行时执行之前编写一个值的定义，并且不要过多考虑副作用产生的无限可能的场景。

这个特性的一个很好的例子是平方根函数，它使用一个“无限”的候选列表，并选择第一个值来达到所需的精度。

```
 public static Function1<Double, Double> nextCandidate(Double n){
      /**
       * Sequence of candidates for the square root according to Newton-Raphson
       */
      return Function1.of((previous) -> (previous + (n / previous)) / 2d);
  }

  public static Double squareRoot(Double n){
      Stream<Double> s = Stream.iterate(1d, nextCandidate(n));
      Double epsilon = 0.0001d; // precision required
      return s.takeUntil(candidate -> Math.abs((candidate * candidate) - n) < epsilon).last();
  }

  public static void main(String[] args) {
      System.out.println(squareRoot(25d)); // 5.000023178253949
  } 
```

前面的例子还示出了高阶函数的另一个例子，由具有作为输出的函数的 nextCandidate 表示。

欢迎在评论中评论你的函数式编程经验。

这是我的第一篇英文帖子，也是一系列论文摘要的第一篇，所以我很感谢关于写作风格、内容、其他要阅读的论文和该系列的推荐标签的反馈:)
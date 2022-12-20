# 论文摘要:函数式编程的单子

> 原文：<https://dev.to/gustavo94/digest-of-papers--monads-for-functional-programming-47fp>

[瓦德勒，菲利普，2001](http://roman-dushkin.narod.ru/files/fp__philip_wadler_001.pdf)
*[用西班牙语阅读此贴](https://dev.to/gustavo94/repasando-artculos-monads-for-functional-programming---c4e)*

单子是数学和函数式编程之间共享的许多概念之一。它来源于范畴理论，用于解决异常控制、计数器或执行跟踪等问题。这个问题在命令式语言中很容易解决，但是在纯命令式语言中就有点复杂了。

> 纯函数式语言有这样的优势:所有的数据流都是显式的。这个缺点是:有时它非常明显。

本文解释了这种显式数据流如何提高模块性以及这一特性在编程中的重要性。

本文解释了这种显式数据流如何提高模块性以及这一特性在编程中的重要性。

monad 也称为标准结构，是一种封装行为的数据结构。这种行为通常作为命令式编程的副作用来实现。理解单子的一个关键例子是封装异常的 Scala 或 VAVR(JAVA 的函数数据结构)中的 Try 值。该值可用于构成函数，而无需对每个可能的错误进行显式管理，直到最终应用。

```
 public static Try<Integer> divide(Integer dividend, Integer divider) {
      return Try.of(() -> dividend/divider);
  }

  public static void main(String[] args) {
      Try<Integer> result = divide(100, 0).flatMap(r1 -> divide(r1, 10));
      System.out.println("RESULT 1 = " + resultado.map(String::valueOf)
       .getOrElseGet(Throwable::getMessage));
       // RESULT 1 = / by zero

      result = divide(100, 10).flatMap(r1 -> divide(r1, 10));
      System.out.println("RESULT 2 = " + resultado.map(String::valueOf)
       .getOrElseGet(Throwable::getMessage));
       // RESULT 2 = 1

  } 
```

## 单子法则

单子的正式定义包括 3 个法则来确定结构是否是单子。这个定律可能理论价值大于实际价值，但是这个博客是关于论文的，所以我会试着解释这些原理。

#### 左单位/左身份

这个定律说，将一个函数应用于一个值和将它应用于具有相同值的 monad 应该是相等的，如果你使用正确的组合(根据函数的返回使用 map 或 flatMap)。

```
 public static void main(String[] args) {
      Integer hundred = 100;
      Try<Integer> tryHundred = Try.of(() -> hundred);
      Boolean r = tryHundred.flatMap(value -> divide(100, value))
      .equals(divide(100, hundred));
      // True
  } 
```

```
 return a >>= f = f 
```

#### 权利单位/权利身份

就像前面的定律一样，如果我们有一个函数，它接收一个值并将结果封装在一个单子中，那么将它应用于该值或与另一个单子函数组合的结果应该是相同的。

```
 public static void main(String[] args) {
      Integer hundred = 100;
      Try<Integer> tryHundred = Try.of(() -> hundred);
      // success return the same value encapsulated in a Try but cannot catch exceptions
      Boolean r = tryHundred.flatMap(value -> Try.success(value))
      .equals(Try.success(hundred));// True
  } 
```

```
 m >>= return = m 
```

JAVA 中的这个例子看起来有相同的实现，但是在 Haskell 中这两个法则之间的差异更加明显。就像我说的，这个定律是理论性的，Haskell 是纯函数性的，更接近数学。

#### 关联性

结合律说，我们应该能够顺序地组合函数，或者调用一个函数作为另一个函数的输入。

```
 public class MonadTest {

  public static Try<Integer> divideByTen(Integer dividend) {
      return Try.of(() -> dividend/10);
  }

  public static Try<Integer> doubleVal(Integer n1) {
      return Try.of(() -> n1 * 2);
  }

  public static void main(String[] args) {

      Try<Integer> tryHundred = Try.of(() -> 100);
      Try<Integer> r1 = tryHundred.flatMap(MonadTest::divideByTen).flatMap(doubleVal::doubleVal);
      Try<Integer> r2 = tryHundred.flatMap(
                        hundred -> divideByTen(hundred).flatMap(MonadTest::doubleVal)
                        );
      Boolean r = r1.equals(r2);// True

  }

} 
```

```
 (m >>= f) >>= g ≡ m >>= (\x -> f x >>= g) 
```
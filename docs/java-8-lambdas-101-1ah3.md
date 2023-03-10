# Java 8 lambdas: 101

> 原文：<https://dev.to/shriyog/java-8-lambdas-101-1ah3>

你所需要的只是 5 分钟来搜索 Java 8 中引入的 Lambda 表达式。这篇文章作为一个开端，快速展示了 lambdas 在取代庞大的匿名类方面是多么的酷。

## 匿名类

您到处都在使用它们——在 GUI 中实现事件处理程序，指定排序的比较器，等等。

```
 button.setOnAction(new EventHandler<ActionEvent>() {
        @Override
        public void handle(ActionEvent event) {
            System.out.println("Hello World!");
        }
    }); 
```

通过这样做，我们只是传递了处理事件的功能。如将代码作为方法参数传递。

最简单的匿名类(只有一个抽象方法)的问题是随之而来的不必要的模板语法。

Lambdas 通过提供简洁紧凑的语法来传达单个方法类的功能，准确地解决了这个问题。

## 极简的例子

*   一个接口- `Operation`
*   一类- `Calculator`

`Operation`接口声明了一个操作 2 个整数的方法。这可以通过多种类型的运算来实现，例如加法和减法。

```
public interface Operation {
    int operate(int a, int b);
} 
```

静态计算方法通过使用提供的操作数调用 operate 方法来执行运算。

```
public class Calculator {
    public static int calculate(int a, int b, Operation operation) {
        return operation.operate(a, b);
    }
} 
```

我们希望调用 calculate 对给定的操作数执行定义的操作(操作接口的实现)。

### 方法 1——使用匿名类

```
 Operation additionOperation = new Operation() {
        @Override
        public int operate(int a, int b) {
            return a + b;
        }
    };

    int result = Calculator.calculate(2, 3, additionOperation); 
```

### 方法 2 -使用 lambda 表达式

```
 int result = Calculator.calculate(2, 3, (x, y) -> x + y); 
```

λ表达式 **`(x, y) -> x + y`** 可以分解成

1.  `(x, y)`
    圆括号内用逗号分隔的一组形式参数(与接口方法签名中的相同)。

2.  `->`
    箭头来区分。

3.  `x + y`
    主体可以有一个被求值并返回的表达式，或者一个用花括号括起来的语句块。

这就是 lambdas 如何轻松替换那些庞大的匿名类。它们似乎也非常适合新的 [Java stream API](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html) ，这是一个函数式转换的集合。

## 资源

[甲骨文 Java 教程- Lambda 表达式](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html)
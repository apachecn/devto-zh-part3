# Java 流 API 简介

> 原文：<https://dev.to/lschultebraucks/introduction-to-java-stream-api-31ie>

*首先发表在我的[博客](https://lasseschultebraucks.com/java/2019/01/20/introduction-to-java-stream-api.html)上*

Java 流 API 从 Java 8 开始就有了。它用来表达对数据的计算是一种简洁而优雅的方式。在接下来的文章中，我将向您介绍最常见的方法，让您了解使用 Java Stream API 可以实现什么。

# 流 vs 集合

在进入代码之前，我想解释一下流和集合之间的区别。很明显，两者都有共同的属性，例如，它们都可以被迭代。您可以使用例如 for each 循环迭代外部集合。取而代之的是，你不在流中显式循环。您以函数的方式表达您的计算，而流 API 在内部处理迭代。此外，流 API 是懒惰的，它的元素是根据需要通过网络计算或获取的。集合是内存中的数据存储，这意味着集合中的每个元素都必须经过计算并存储在 RAM 中，然后才能访问它。但是这并不意味着您不能对已经计算过的数据使用流。在特定情况下，它使流比集合更灵活。您还可以从基于集合的数据结构中创建流，就像我在下面的例子中所做的那样。

# 例子

在接下来的内容中，我将向你展示一些使用流的例子，并且将我的解决方案与经典的编程方法进行比较。

## `filter`

如果你想过滤一个列表，你可以使用`filter`。

让我们假设我们有一个单词列表。

```
List<String> words = Arrays.asList("Abra", "Kadrabra", "Aloha"); 
```

现在我们要过滤所有以大写字母 A 开头的单词并打印出来。

```
words.stream()
        .filter(word -> word.startsWith("A"))
        .forEach(System.out::println); 
```

这里我们实际上使用了两个流方法。首先，我们将列表转换成一个流，过滤以大写字母 a 开头的单词。在产生的流中，我们打印出每个带有`System.out.println`的单词。

你可能以前见过类似的 lambda 表达式，比如在`filter`中的 on，但是马也，你没见过很多我在`forEach`方法中使用的。`System.out::println`只是下面 lambda 表达式的语法糖:`x -> System.out.println(x)`。

我们也可以通过简单地使用 for 循环来编写不使用流的代码:

```
for (String word : words) {
        if (word.startsWith("A")) {
            System.out.println(word);
    }
} 
```

在我看来，这个解决方案不够优雅，可以写得更有表现力，如上面的代码片段所示。

我们甚至可以通过在外部方法中为`startsWith`条件创建一个方法来改进我们的流示例。

```
words.stream()
        .filter(StreamExamples::startsWithA)
        .forEach(System.out::println); 
```

用

```
private static boolean startsWithA(String word) {
    return word.startsWith("A");
} 
```

## `sum`

有了 Stream API，你再也不用像这样写代码了

```
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

int sum = 0;
for (Integer number : numbers) {
    sum += number;
}
System.out.println(sum); 
```

相反，你可以写

```
System.out.println(numbers.stream().mapToInt(Integer::intValue).sum()); 
```

这与上面的代码片段完全相同。

## `map`

将流中的每个元素投射成另一种形式。当我使用`mapToInt`将元素投影到 Int 的时候，我可以使用 map 将流中的每个元素投影到一个新元素中。

```
numbers.stream()
        .map(number -> number * number)
        .forEach(System.out::println); 
```

编写这段代码的传统方法是下面的代码片段:

```
for (Integer number : numbers) {
    int squaredNumber = number * number;
    System.out.println(squaredNumber);
} 
```

## `flatMap`

接下来，假设我们有以下数据源:

```
List<List<Integer>> matrix = Arrays.asList(Arrays.asList(1, 2), Arrays.asList(3, 4), Arrays.asList(5, 6)); 
```

我们现在的任务是总结矩阵中所有的柠檬。

为了简化数据结构，我们可以使用`flatMap`。它连接流并生成单个流作为结果。因此，为了计算总和，我们可以首先使用`flatMap`将多个流连接成一个流，然后使用`mapToInt`和`sum`，如上面的`sum`示例所示。

```
System.out.println(matrix.stream().flatMap(Collection::stream).mapToInt(Integer::intValue).sum()); 
```

允许在 for 循环中放弃 for 循环，在一行中编写优雅的代码。

如果你的同事拉你的代码，他们会感谢你的。

## `collect`

最后但同样重要的是，您可以通过使用`collect`将流转换成传统的数据结构。

```
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

List<Integer> evenNumbers = numbers.stream().filter(x -> x % 2 == 0).collect(Collectors.toList()); 
```

# 结论

我已经上传了所有的例子到我的 Github

Java 8 引入了 Java Streams，它允许我们以一种简洁的功能和优雅的方式表达数据处理查询。还有更多的操作需要探索，这篇文章应该只是给你一个流 API 的介绍。我希望您现在有动力在下次使用 Java 时使用 Stream API。
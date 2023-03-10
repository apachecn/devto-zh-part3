# Java 8 Streams:停止使用并简化代码！

> 原文：<https://dev.to/rinaldodev/java-8-streams-pare-de-usar-for-e-simplifique-seu-codigo-4df8>

Java 8 流使其代码更易于阅读和**简洁**，并在处理列表和集合时能够提高其**生产率**。立即学习，从你的代码中提高到**！**

 **## 传统环路

在 Java 5 之前迭代一个“`Collection`”是一个丑陋的代码，**是一个复杂的**，这总是增加了创建意外的 **bug** 的机会。让我们记住:

```
List<Integer> lista = Arrays.asList(1, 5, 8, 7, 4, 6, 3, 2, 1, 8, 5, 7, 4);
for (Iterator<Integer> numero = lista.iterator(); numero.hasNext();) {
    Integer integer = numero.next();
    System.out.print(integer);
} 
```

幸运的是，从 Java 5 开始，创建了新的语法 *For-Each* ，大大降低了迭代集合的复杂性:

```
List<Integer> lista = Arrays.asList(1, 5, 8, 7, 4, 6, 3, 2, 1, 8, 5, 7, 4);
for (Integer numero : lista) {
    System.out.print(numero);
} 
```

尽管新语法大大简化了 java 开发人员的生活，但它还没有充分利用功能编程，强烈的‘t0’是势在必行的。在 Java 8 中，这完全改变了**。**

 *** * *

## 流做 Java 8

Java 8 带来了*lambda 函数的创建。*如果你还不熟悉它们， [**点击此处**](https://dev.to/rinaldodev/java-8-entenda-facilmente-funes-lambda-a-principal-novidade-59b) 可以了解更多。从而创造了一种**新**的收藏叠代方式，更简单**、**【简明】等**可读**。请看一个简单的例子，使得**甚至**与前两个例子相比:****

```
List<Integer> lista = Arrays.asList(1, 5, 8, 7, 4, 6, 3, 2, 1, 8, 5, 7, 4);
lista.stream().forEach(System.out::print); 
```

此语法会建立一个**串流**。在这种情况下，数据就是数字。对于**每个**其中一个是通过传递数字作为参数而调用`System.out.print(...)`函数的。这里我们还使用了*方法参考*的语法，这就是方法参考方式:`System.out::print`。

* * *

## Java 8 流中的中介操作

我们以上使用的方法是****最终**，或**终端**，因为在此之后就不能再做别的了。但是，您可以使用 Java 8 流执行更多操作。现在让我们来看看有**中间作业**的例子。**

 **### 方法`skip`、`limit`和`distinct`

下面的示例在介绍之前过滤了一些结果。

```
List<Integer> lista = Arrays.asList(1, 5, 8, 7, 4, 2, 3, 2, 1, 8, 5, 7, 4);
lista.stream()
    .skip(2) // ignora os dois primeiros números
    .limit(9) // limita a 9 números
    .distinct() // ignora números iguais
    .forEach(System.out::print); 
```

执行此程式码会在主控台中产生下列输出:

```
8742315 
```

在本例中，在执行`forEach:`之前进行了多次操作

 ***   `skip`用于忽略前 x 项。我们忽略了 1 号和 5 号。
*   `limit`告知您要处理多少对象，从此以后将忽略接下来的对象。在这里，我们得到了 8 到 5。
*   `distinct`与 SQL`DISTINCT`相同，只保留彼此不同的结果。为此，它使用列表对象的`equals`方法。我们忽略了重复的 2 和 8。

### 欧梅托`filter`

上面显示的 3 项功能──我们的数据流，即**忽略了**的一些要素。但是如果还需要一个更个性化的**滤波器呢**？为此采用`filter:`方法

```
List<Integer> lista = Arrays.asList(1, 5, 8, 7, 4, 2, 3, 2, 1, 8, 5, 7, 4);
lista.stream()
    .filter(e -> e % 2 == 0) // mantém apenas números pares
    .forEach(System.out::print); // imprime todos no console 
```

上述程式码片段会在主控台中产生下列输出:

```
842284 
```

注意到`filter`方法，在这种情况下，它正在过滤我们的流以保持**仅**偶数。如果您还不熟悉 lambdas 的功能，**结构**如下:

[![Java 8: estrutura da função lambda. Primeiro temos os parâmetros da função, depois o arrow token, depois do corpo da função.](img/69e35b49a60eb2c34a803ba0a9a4aae1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ISS8s7dU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rinaldo.dev/wp-content/uploads/2019/03/explicacao_lambda-1.png) 

<figcaption>传递给`filter`方法的 lambda 函数结构</figcaption>

### 欧梅托`map`

如果在将数据传递到下一个流方法之前需要对数据进行“T2”转换，则使用`map`方法。尽管有名字，但与接口`Map`无关。看下面。

```
List<Integer> lista = Arrays.asList(1, 5, 8, 7, 4, 2, 3, 2, 1, 8, 5, 7, 4);
lista.stream()
    .map(e -> e * 2) // multiplica cada item por 2
    .forEach(e -> System.out.print(e + " ")); // imprime todos no console 
```

控制台输出如下:

```
2 10 16 14 8 4 6 4 2 16 10 14 8 
```

请注意，所有数字都乘以 2，然后才会显示在控制台中。但是，重要的是要注意到**原来的单子编号没有改变**。也就是说，`map`方法的变换只影响在该流中向前传递的数值。这很棒，因为只要有可能，理想的做法就是与不变的**价值观和实例一起工作。**

 *** * *

## Java 8 流中的最终操作

在迄今为止介绍的所有示例中，流以“`forEach`”结尾。这是 Java 8 流 API 中可用的最终操作之一**。也就是说，它是一种关闭流并收集所有操作结果的方法。现在我们将看到其他**最终**操作，这些操作通常比`forEach`更有用。毕竟，您可能希望存储流操作的结果，并将该结果用于某些用途。让我们看看一些可能性。**

 **### `max``min``count`

如果要取较大的**或较小的**值，在数据流中执行操作后，`max`和`min`方法正是这样做的。让我们来看看‘T2’:****

```
List<Integer> lista = Arrays.asList(1, 5, 8, 7, 4, 2, 3, 2, 1, 8, 5, 7, 4);
Optional<Integer> maiorNumero = lista.stream()
        .map(e -> e * 2) // multiplica cada item por 2
        .max(Comparator.naturalOrder()); // pega o maior item pela ordem natural
System.out.println(maiorNumero.get()); 
```

使用`max`方法时，需要输入数据流将如何**比较**其对象。在这种情况下，由于`Integer`已经实现了界面`Comparable`和方法`compareTo`，他已经有了**自然顺序**可供我们使用，所以我们使用`Comparator.naturalOrder()`。在本例中，数字`16`列印在主控台上，因为它是串流中最大的**数字，在我们都乘以 2 之后。请注意，由于是**最终**运算，所以需要将结果存储在**变量中才能使用。`min`方法也是如此:****

```
List<Integer> lista = Arrays.asList(1, 5, 8, 7, 4, 2, 3, 2, 1, 8, 5, 7, 4);
Optional<Integer> menorNumero = lista.stream()
        .map(e -> e * 2) // multiplica cada item por 2
        .min(Comparator.naturalOrder()); // pega o menor item pela ordem natural
System.out.println(menorNumero.get()); 
```

遵循同样的方法`max`的想法，本例在控制台上输出`2`。

最后，**方法返回流中还剩多少**元素:

```
List<Integer> lista = Arrays.asList(1, 5, 8, 7, 4, 2, 3, 2, 1, 8, 5, 7, 4);
Long quantidade = lista.stream()
        .filter(e -> e % 2 == 0) // mantém apenas números pares
        .count(); // pega quantos itens restam no stream
System.out.println(quantidade); 
```

在此示例中，控制台输出为“`6`”，即流中偶数的数量。

### 欧梅托`collect`

并总结了基本操作的例子，我们有方法`collect`。它是最**可定制的**收集流运行结果的方法。因为有**无数**的使用方式，所以我会在一篇具体的文章中多谈一谈。要知道一旦我**分享**，就跟我来，链接就在本文的**结尾。现在让我们来看一下 collect 的一些基本例子。**

 **如果只想把结果收集到一个新的**列表中，请在`Collectors.toList()`中使用预先实施的**收集器:****

```
List<Integer> lista = Arrays.asList(1, 5, 8, 7, 4, 2, 3, 2, 1, 8, 5, 7, 4);
List<Integer> novaLista = lista.stream()
        .filter(e -> e % 2 == 0) // mantém apenas números pares
        .map(e -> e * 2) // multiplica cada item por 2
        .collect(Collectors.toList()); // coleta todos os itens em uma nova lista
System.out.println(novaLista); 
```

在此例中**我们过滤**只筛选偶数，**我们将**全部乘以 2，**我们收集**的结果列入新的列表。控制台输出为:

```
[16, 8, 4, 4, 16, 8] 
```

如欲**将**清单项目分组为**地图**，请使用`Collectors.groupingBy(...)`收集器:

```
List<Integer> lista = Arrays.asList(1, 5, 8, 7, 4, 2, 3, 2, 1, 8, 5, 7, 4);
Map<Boolean, List<Integer>> mapa = lista.stream()
        .map(e -> e * 2) // multiplica cada item por 2
        .collect(Collectors.groupingBy(e -> e > 8)); // agrupa itens baseado no resultado da comparação
System.out.println(mapa); 
```

在本例中，我们将所有数据流项乘以 2，然后**将**分组为大于的数字**或小于**或等于**。从控制台打印时查看**地图**:**

```
{false=[2, 8, 4, 6, 4, 2, 8], true=[10, 16, 14, 16, 10, 14]} 
```

并且如果要从流中生成一条**单串**，则可以使用**将**多个串合并成**一个**的收集器，只需:

```
List<Integer> lista = Arrays.asList(1, 5, 8, 7, 4, 2, 3, 2, 1, 8, 5, 7, 4);
String stringUnica = lista.stream()
        .map(String::valueOf) // transforma cada item em String
        .collect(Collectors.joining(";")); // junta todas as Strings em uma única separada por ';'
System.out.println(stringUnica); 
```

在此**我们使用 String.valueOf(…)将每一个数字转换成字串。之后**我们将**分组为单一字串，用**点和逗号**分隔元素。控制台输出如下所示:**

```
1;5;8;7;4;2;3;2;1;8;5;7;4 
```

* * *

## Parando de usar ' for ' e ' while '

使用 Java 8 流，很少需要再做一次**显式循环**，如`for`、`while`或`do...while`。流 API 使用**隐式循环**，充分利用**功能编程**，使其代码更加**可读**和**简明**。此外，在接下来的一篇文章中，我将演示如何使**与**并行运行非常**简单的数据流。**！

尽管我们只从列表中呈现流，但可以用**的各种不同方式来创建它们！你看:**

*   从一个**阵列**使用`Arrays.stream(Object[])`；
*   从**静态方法**到**流**类，如`Stream.of(Object[])`、`IntStream.range(int, int)`；
*   从**档案**中取得**行**，可用`BufferedReader.lines()`取得；
*   **文件**的流可以通过`Files`类中的方法获取；
*   **随机数字**的流可以在`Random.ints()`中获取。

恭喜！现在**你**更了解 Java 8 的**流 API！你可以利用这一知识及时交付****高质量**的解决方案。****

 ****想进一步了解 lambda 及其**结构**的功能吗？[参见本文了解**容易**lambda 的功能结构。](https://dev.to/rinaldodev/java-8-entenda-facilmente-funes-lambda-a-principal-novidade-59b)

你想收到我写高质量代码**和按时交付项目的最佳提示？ [**然后进入这里**。](https://rinaldo.dev/contato/)**

 **你想每天学习改进你的代码吗？然后在**Twitter**:[**https://Twitter . com/rinalddev**](https://twitter.com/rinaldodev)上跟我来

**你呢？**是否曾用**流**在任何代码中进行维护？还记得**本来可以使用流的情况吗？留下一个**评语**！**

 **你喜欢你学到的东西吗？与 WhatsApp 及其社交网络上的其他开发者共享！

post [Java 8 Streams:停止使用，简化代码！](https://rinaldo.dev/java-8-streams-pare-de-usar-for-e-simplifique-seu-codigo/)首先出现在 [rinaldo.dev](https://rinaldo.dev) 中。********************************
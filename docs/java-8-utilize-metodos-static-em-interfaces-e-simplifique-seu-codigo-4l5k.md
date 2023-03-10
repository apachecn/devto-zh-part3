# JAVA 8:在接口中使用 STATIC 方法并简化代码！

> 原文：<https://dev.to/rinaldodev/java-8-utilize-metodos-static-em-interfaces-e-simplifique-seu-codigo-4l5k>

方法**静态**在 Java 8 界面**简化**并减少其代码，同时还可以提高**生产率**。立即学习，从你的代码中提高到**！**

 **## 实用类中的`static`方法

创造**`static`方法的必要性很普遍，这样就可以不用创造新对象就能调用。 **JDK** 本身有几个仅包含`static`方法的类，例如`java.lang.Math`、`java.util.Collections`、`java.util.Arrays`、`java.util.stream.Streams`。**

 **并且 JDK 明确表示不是要您创建这些类的**实例。查看直接从 JDK 的“**”源代码中提取的构造函数:****

```
 // Suppresses default constructor, ensuring non-instantiability.
 private Collections() {
 } 
```

```
 // Suppresses default constructor, ensuring non-instantiability.
 private Arrays() {} 
```

```
/**
 * Don't let anyone instantiate this class.
 */
private Math() {} 
```

```
private Streams() {
    throw new Error("no instances");
} 
```

此外，许多**图书馆**提供仅包含`static`方法的实用类，如 _**Apache common**项目的实用类。

### 实用类的问题

这种类通常称为*【实用程序】*，因此在其中许多类中终止* `Util`，主要是 Apache 类。这些类基本上遵循面向对象(OO)的模式**而不是**，因为它只包含结构化代码。

此外，如果 Java 中有**的可能**，这些方法甚至都不在**类**之内。

### 何时使用实用类？

不遵循 OO 的概念并不一定是**坏事。但是，想象一下以下情况:你有一个类`Pessoa`，想做一个方法**`static`。你认为什么能使**更有意义**:用这种方法创建`PessoaUtil`类，还是在自己的类`Pessoa`中创建？我赞成**第二种选择**。也就是说，只有在以下情况下创建实用程序类才有意义:****

 *****   你不是原来的类别的拥有者，或者你不能**修改它**；或者说
*   您有一个**接口结构**，此实用程序方法适用于**全部**。`java.util.Collections`类就是这样。

我是说，我**一直坚持**直到 **Java 8** 。现在，在第二种情况下，自身的**界面**可能有`static`的方法。

* * *

## Java 8 界面中的`static`方法

从 Java 8 开始，可以在自己的**界面**中创建`static`方法:

```
 interface Corredor {
    static double calculeVelocidade(double tempo, double distancia) {
      return distancia/tempo;
    }
  } 
```

也就是说，在这种情况下，无需实例化一个**新**对象即可调用接口方法**:**

```
 double velocidade = Corredor.calculeVelocidade(5.0, 10.0);
    System.out.println(velocidade); 
```

因此，即使有界面的**结构，也可以在相应的**界面内创建自己的`static`方法。****

 ****当然，规则中也有的例外情况，或许还有类似的情况，创造实用类是有意义的。问题是，现在你有了一个新的**选项要考虑。**

 **### 接口中的方法不属于 API

十分重要的是**要记住，创建为`static`**【no】**的方法是界面**API 的一部分。也就是说，它们实际上是**界面元素**。就像一类的`static`方法是**类的元素**。****

 ****因此，下面的代码**不编译**，因为行 3 和行 7。

```
 public static void main(String[] args) {
    // NÃO COMPILA - a classe Pessoa não possui o método calculeVelocidade
    double velocidade = Pessoa.calculeVelocidade(5.0, 10.0);
    System.out.println(velocidade);

    // NÃO COMPILA - o objeto instância de Pessoa não possui o método calculeVelocidade
    double velocidade2 = new Pessoa().calculeVelocidade(5.0, 10.0);
    System.out.println(velocidade2);
  }

  interface Corredor {
    static double calculeVelocidade(double tempo, double distancia) {
      return distancia/tempo;
    }
  }

  class Pessoa implements Corredor {} 
```

即**否**可以直接在实现该接口的**类**中调用接口的`static`方法。

### 界面中的`static`方法是`public`而不是`abstract`

从定义上讲，直到 Java 8，接口中的所有方法都是“`public`”和“`abstract`”，即使您没有这样声明。请参见下面的示例:

```
 interface Corredor {
    public abstract void correr();
    void correrRapido(); // também é public e abstract
  } 
```

`static`方法仍然是强制性的`public`，但**不是`abstract`。**而且说得通，毕竟它们包含了**实施**。

恭喜！现在**您**更熟悉 Java 8 介面！你可以利用这一知识及时交付****优质解决方案。****

 ******你呢？**是否已在接口中使用静态方法？你有没有维护过这样的代码？还记得**你**本来可以从这一新选择中获益的情况吗？留下一个**注释**！

## 也查看 YouTube 上的视频！

[https://www.youtube.com/embed/8W_Xp2BnSVg](https://www.youtube.com/embed/8W_Xp2BnSVg)

<center>***[https://www.youtube.com/watch?v=8W_Xp2BnSVgM](https://www.youtube.com/watch?v=8W_Xp2BnSVgM)***

<center>

post [JAVA 8:在接口中使用静态方法并简化代码！](https://rinaldo.dev/java-8-utilize-metodos-static-em-interfaces-e-simplifique-seu-codigo/)首先出现在 [rinaldo.dev](https://rinaldo.dev) 中。

</center>

</center>**********************
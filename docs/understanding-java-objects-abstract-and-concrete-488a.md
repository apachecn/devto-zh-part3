# 理解抽象和具体的 Java 对象

> 原文：<https://dev.to/martingaston/understanding-java-objects-abstract-and-concrete-488a>

我最近在 Java 方面做了很多工作，但是只有当我的项目要求我采用一些著名的面向对象设计模式时，我才意识到我完全不确定在基础层面上发生了什么，所以我发现跟上所有的术语是一个真正的挑战。我觉得自己撞上了一堵砖墙。

我决定[投入一些时间](https://docs.oracle.com/javase/tutorial/java/TOC.html)来解开其中的一些构件——所以让我们抛开这些 Java 术语。我希望，如果你曾经有过类似的感觉，这些笔记可能有助于加深你对 Java 蔓延的理解。

### Java 术语#1:创建对象

在 Java 中，几乎所有东西都是对象——比如`String`、`ArrayList`和`HashMap`。对象是从类中创建的。把你的类代码想象成 Java 产生对象的指令手册。

创建一个对象需要创建一个类的实例——所以短语*实例化一个类*经常被那些想要听起来特别聪明的人使用。但是你可以交替使用这两个术语，这取决于你在和谁说话，或者你是否想赢得拼字游戏。

要正式创建一个对象/实例化一个类，也就是说我们必须做些什么来阻止编译器在我们的 Mr Resetti 上完全运行，我们遵循一个三步的过程:声明、实例化和初始化。

**声明**是通过指定变量的`type`和`name`来实现的，所以我们可以告诉 Java `Dragon viserion;`，它将为接受`Dragon`类型做好一切准备。但是在这一点上，我们的对象目前更接近于我的支票账户，而不是一条可怕的龙:它是空的。里面什么都没有。Zip，nada，nill 或者，确切地说，`null`。

时刻提防`null`。如果我有孩子，我不希望看到他们和`null`在一起。

声明一下:Java 也有*原语*和*引用*变量，我们现在可以对它们挥挥手，但可能会不时地制造一些额外的麻烦，通常是在赋值和比较的时候。但是，嘿，我们是用 Java 编程的，所以可以很有把握地假设我们时不时都会遇到一些麻烦。

无论如何，我们已经准备好了第 2 步和第 3 步——实例化和初始化，这两个步骤是成对的:

```
viserion = new Dragon(); 
```

`new`操作符**实例化**类，在后台分配所需的内存，然后返回该内存的引用(地址),这样我们的`viserion`变量就知道把敲门的人送到哪里。调用`new`也需要调用一个构造函数，这就是括号进入场景的地方。`()`部分处理**初始化**，它嗅出一个与签名匹配的类构造函数，最终让我们的对象完全设置好并准备好出来。

Java 类中的构造函数与类同名，并且没有返回类型。如果我们看一下我们的`Dragon`类，我们可以看到它有两个构造函数签名:一个可以响应一个零参数的构造函数，这个构造函数又会调用我们的双参数构造函数，这个构造函数需要一个带有默认值的`Stomach`和`Mouth`参数。

```
public class Dragon {
  private Stomach stomach;
  private Mouth mouth;

  public Dragon() {
    this(new BigStomach(), new BigMouth());
  }

  public Dragon(Stomach stomach, Mouth mouth) {
    this.stomach = stomach;
    this.mouth = mouth;
  }

  public eat(Food food) {
    mouth.consume(food);
    stomach.digest(food);
  }

  public dracarys(Target target) {
    return mouth.breatheFire(target);
  }
} 
```

构造函数对于类来说是强制的，但是如果没有显式定义，编译器会将类视为一个零参数的默认构造函数，完全免费。

我们还将`viserion`的声明和实例化/初始化分成两行，这是不必要的——如果愿意，我们也可以在一行中声明所有的赋值。我们甚至不需要将对象赋给变量，而是可以直接在表达式中使用它们。让我们把所有这些结合在一起，创造另一个`Dragon` :

```
Dragon drogon = new Dragon(new GiantStomach(), new GiantMouth()); 
```

我们声明一个引用变量`drogon`，用`new`实例化我们的 Dragon 类，然后用我们的构造函数初始化它，它响应`Stomach`和`Mouth`签名。我们还直接在构造函数调用中实例化了两个新对象，`GiantStomach`和`GiantMouth`。

现在，举个真实的例子怎么样？看看`ArrayList`如何在 Java 源代码中使用三个构造函数签名:

```
// Constructs an empty list with the specified initial capacity.
public ArrayList(int initialCapacity) {
  if (initialCapacity > 0) {
    this.elementData = new Object[initialCapacity];
  } else if (initialCapacity == 0) {
    this.elementData = EMPTY_ELEMENTDATA;
  } else {
    throw new IllegalArgumentException("Illegal Capacity: "+ initialCapacity);
  }
}

// Constructs an empty list with an initial capacity of ten.
public ArrayList() {
  this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}

// Constructs a list containing the elements of the specified collection, in the order they are returned by the collection's iterator.
public ArrayList(Collection<? extends E> c) {
  elementData = c.toArray();
  if ((size = elementData.length) != 0) {
    if (elementData.getClass() != Object[].class)
      elementData = Arrays.copyOf(elementData, size, Object[].class);
  } else {
    this.elementData = EMPTY_ELEMENTDATA;
  }
} 
```

呀，那是相当多的东西。就当是打基础吧。如果你和我一样，你经常*知道*这种东西，实际上，你知道，*不知道*它，所以有一个复习是很好的。

### Java 行话#2:具体类

这个词到处出现，我发现你应该知道它默认的意思。声明一下，我真的不知道这对于**个月**来说意味着什么。

用一句话概括，具体的类是任何可以用关键字`new`创建(实例化)的类。具体类已经实现了它们的所有方法，不管它们有多少接口或者类。

当涉及到做一些具体的事情时，不需要特定的语言关键字，但是您可能会在 UML 图中看到这个词经常出现，并且人们在谈论面向对象编程，所以知道正在发生什么是很好的，这样您就可以参与到对话中来。

我们的`Dragon`类是真正的，100%认证的混凝土，当你不使用任何抽象的东西时，这是完全容易做到的。我发现默认情况下更容易认为类是具体的，只有在使用继承(通过`extends`)和接口(通过`implements`)时才是具体和抽象的。

### Java 行话#3:接口

如果一个具体的类是一个说明书，那么一个接口就是一个蓝图——它是一个未实现的方法签名的列表，通过声明我们将`implement`这个接口，我们的类保证支持它。当一个类选择`implement`一个接口时，这通常被称为**契约**——因为实现的类本质上承诺包含这些方法。

如果你在晚上编译代码，不履行接口契约也会让编译器在深夜造访你。它不会为此高兴的。

一个 Java 类可以实现许多接口，正如你从`ArrayList` :
中看到的

```
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable { 
```

当你在 Java 中工作时，你会发现很多可操作的接口——在这里我们可以看到`Cloneable`和`Serializable`，你会看到它们一次又一次地出现，但是现在我们将集中在`List`上。注意`List`是如何使用`interface`关键字而不是`class` :

```
public interface List<E> {
    int size();
    boolean isEmpty();
    //... there's plenty more empty methods 
```

这个接口中既没有实现`size()`也没有实现`isEmpty()`。我们不能用关键字`new`从这个接口创建一个`List`对象，这就是为什么它不是一个具体的类。但是，通过实现`List` , `ArrayList`保证遵循契约，并确保它知道在发送对任何接口方法签名的请求时如何响应。

对于某些类来说，接口就像一个小领结——它们增加了整洁的正式程度，这是类型检查器所尊重的。您也可以使用接口名称作为引用变量的类型，这意味着任何实现该接口的类都将被允许赋给它。

### Java 行话#4:抽象类

抽象类可以有已实现和未实现的方法，但它们不是具体的，因为它们不能用`new`关键字创建对象。然而，子类可以使用`extends`关键字来继承它们的功能。

许多 Java 的列表集合继承自抽象类`AbstractList` :

```
// AbstractList.java
public abstract class AbstractList<E> {
  //...
  public boolean add(E e) {
    add(size(), e);
    return true;
  }

  public abstract E get(int index); 
```

注意用于表示抽象类和抽象方法的关键字`abstract`:使用后者需要声明前者。还要注意抽象方法`get`是如何缺少括号并以分号为特征的，类似于我们之前声明接口方法的方式。

`ArrayList`是`AbstractList`的子类，因此必须实现一个`get`方法，但将继承`add`方法:

```
// ArrayList.java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable {
  // ...
  public E get(int index) {
    Objects.checkIndex(index, size);
    return elementData(index);
  } 
```

如果`ArrayList`在扩展`AbstractList`时没有实现它自己的`get`方法，它将不得不声明它的类`abstract`以满足编译器，这将意味着它不能使用`new`关键字创建对象，也不能达到非常期望的具体状态。

### 包装完毕

我们已经了解了在 Java 中是什么构成了一个具体和抽象的类，这意味着我们要熟悉类是如何被实例化的。我们从一个简单的示例`Dragon`类转移到查看来自 JDK 12 的一些源代码片段，看看`ArrayList`如何扩展`AbstractList`并实现`ArrayList`。

理解这些构件为掌握 Java 最突出的设计模式中严格的面向对象特性打下了非常有用的基础。

* * *

这篇文章对你有用吗？我真的很感谢任何评论和反馈，是否有任何事情可以做得更清楚或解释得更好。我也非常感谢任何更正！
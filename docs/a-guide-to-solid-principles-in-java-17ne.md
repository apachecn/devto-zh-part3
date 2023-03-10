# java 坚实原理指南

> 原文：<https://dev.to/arpitmandliya/a-guide-to-solid-principles-in-java-17ne>

在这篇文章中，我们将看到 Java 中的 5 个坚实的原则。

Robert C. Martin 给出了五个面向对象的设计原则，首字母缩写“S.O.L.I.D”。这个首字母缩略词的每一个字母都讲述了 [Java](https://java2blog.com/core-java-tutorial-for-beginners-experienced/) 中的原理。当你结合使用 S.O.L.I.D 的所有原则时，开发易于管理的软件就变得更容易了。使用 S.O.L.I.D .的其他功能有:

*   它避免了代码气味
*   快速折射代码
*   能够进行适应性或敏捷的软件开发

当你在编码中使用 S.O.L.I.D 原则时，你就开始编写既高效又有效的代码。

## **S . o . l . I . d 是什么意思？**T3】

如上所述，S.O.L.I.D 代表了 Java 的五个原则，它们是:

1.  单一责任原则
2.  o:开闭原则
3.  李:里斯科夫替代原理
4.  I:界面分离原理
5.  依赖倒置原则

本文深入讨论了每一个原则。我们将首先考虑第一个原则，即单一责任原则。

## **单一责任原则**

 **根据[单一责任原则](https://java2blog.com/single-responsibility-principle-java/)，应该只有一个原因导致一个类必须被改变。这意味着一个类应该有一个任务要做。这个原则通常被称为主观的。

用一个例子就能很好地理解原理。假设有一个执行以下操作的类。

*   连接到数据库
*   从数据库表中读取一些数据
*   最后，将其写入文件。

你想象过这个场景吗？在这里，类有多种改变的原因，其中很少是文件输出的修改、新数据库的采用。当我们谈论单一原则责任时，我们会说，有太多的理由让这个阶级改变；因此，它不适合单一责任原则。

## **开闭原理**

根据[开闭原则](https://java2blog.com/open-closed-principle-java/)，实体或对象应该保持打开以进行扩展，但它们应该保持关闭以进行修改。准确地说，根据这个原则，一个类应该以这样一种方式编写，它可以完美地执行它的工作，而不需要假设未来的人会简单地来改变它。因此，该类应该保持关闭以便修改，但是它应该有扩展的选项。扩展该类的方法包括:

*   从类继承
*   从类中重写所需的行为
*   扩展该类的某些行为

开闭原理的一个绝佳例子，可以借助浏览器来理解。你还记得在你的 chrome 浏览器中安装扩展吗？

chrome 浏览器的基本功能是浏览不同的网站。当你使用 chrome 浏览器写邮件时，你想检查语法吗？如果是的话，你可以简单地使用语法扩展，它提供你对内容的语法检查。

这种增加浏览器功能的机制是一种扩展。因此，浏览器是一个很好的功能性的例子，它对扩展是开放的，但对修改是关闭的。简单来说，你可以通过在浏览器上添加/安装插件来增强功能，但不能构建任何新的东西。

我们再举一个例子。
您正在使用任何 Spring 函数功能。显然你不能改变它的核心逻辑，但是你可以扩展 Spring 框架类并创建你自己的类。

## **里斯科夫替代原理**

利斯科夫替换原则假设 q(x)是一个属性，关于属于类型 t 的 x 的实体是可证明的。现在，根据这个原则，对于属于类型 S 的对象 y，q (y)现在应该是可证明的，并且 S 实际上是 t 的子类型。你现在困惑了，不知道利斯科夫替换原则实际上是什么意思吗？它的定义可能有点复杂，但事实上，它很简单。唯一的事情是，每个子类或派生类应该可以替换它们的父类或基类。

你可以说它是一个独特的面向对象原则。通过理解这个原理，原理可以进一步简化；一个特定父类型的子类型，在不制造任何复杂情况或搞砸事情的情况下，应该有能力代替那个父类型。该原理与利斯科夫替代原理密切相关。

## **界面偏析原理**

根据[接口分离原则](https://java2blog.com/interface-segregation-principle-java/)，一个客户端无论如何都不应该被强制实现一个它不使用的接口，或者客户端不应该被强制依赖任何它们不使用的方法。

因此，基本上，接口分离原则，因为你喜欢的接口，这是小的，但客户特定的，而不是单片和更大的接口。

简而言之，强迫客户依赖他们不需要的某样东西对你来说是不好的。

现在让我们再举一个例子来理解这一点。

我们举个简单的例子。你正在用 java 实现你自己的 ArrayList 和 LinkedList。您创建了一个名为 List 的接口，这两个类都将实现它。

```
package org.arpit.java2blog;

public interface List<T> {
    public T get();
    public void add(T t);
    public T poll();
    public T peek();    
} 
```

Let's create LinkedList class now.

```
package org.arpit.java2blog;

public class LinkedList implements List{

    @Override
    public Integer get() {
        // Implement this method
        return null;
    }

    @Override
    public void add(Integer t) {
        // Implement this method
    }

    @Override
    public Integer poll() {
        // Implement this method
        return null;
    }

    @Override
    public Integer peek() {
        // Implement this method
        return null;
    }
} 
```

现在让我们创建数组列表类。

```
package org.arpit.java2blog;

public class ArrayList implements List{

    @Override
    public Integer get() {
        // Implement this method
        return null;
    }

    @Override
    public void add(Integer t) {
        // Implement this method
    }

    @Override
    public Integer poll() {
        // ArrayList does not require this method
        return null;
    }

    @Override
    public Integer peek() {
        // ArrayList does not require this method
        return null;
    }
} 
```

**你看到问题了吗**，即使你不需要 ArrayList 中的 poll 和 peek 方法，我们也实现了。
**上述问题的正确解决方案是:**
创建另一个名为 Deque 的接口，该接口将具有 peek 和 poll 方法。

```
package org.arpit.java2blog;

public interface Deque<T> {
    public T poll();
    public T peek();    
}

```

并从列表界面中删除 peek 和 poll。

```
package org.arpit.java2blog;

public interface List<T> {
    public T get();
    public void add(T t);   
}

```

现在让我们更改 LinkedList 类。

```
package org.arpit.java2blog;

public class LinkedList implements List,Deque<integer>{

    @Override
    public Integer get() {
        // Implement this method
        return null;
    }

    @Override
    public void add(Integer t) {
        // Implement this method
    }

    @Override
    public Integer poll() {
        // Implement this method
        return null;
    }

    @Override
    public Integer peek() {
        // Implement this method
        return null;
    }
}</integer> 
```

现在让我们改变 ArrayList 类。

```
package org.arpit.java2blog;

public class ArrayList implements List{

    @Override
    public Integer get() {
        // Implement this method
        return null;
    }

    @Override
    public void add(Integer t) {
        // Implement this method
    }
} 
```

如您所见，我们已经分离了两个接口来实现所需功能。

## **依存倒置原理**

根据这一依赖倒置原则，实体应该只依赖抽象而不依赖具体。根据它，高级模块决不能依赖于任何低级模块，而应该依赖于抽象。让我们再通过另一个实例来理解它。

你去当地的商店买东西，你决定用你的借记卡支付。所以，当你把你的卡交给店员付款时，店员根本不会检查你给的是哪种卡。就算你给了 Visa 卡，他也不会拿出 Visa 机给你刷卡。你用哪种类型的信用卡或借记卡付款并不重要；他们会简单地刷一下。所以，在这个例子中，你可以看到你和店员都依赖于信用卡的抽象，你不担心卡的细节。这就是依赖性反转原则。

## **裹起**

我希望现在你已经知道了 S.O.L.I.D 所有五个组成部分的基本定义，它们是单一责任原则、开放、封闭原则、利斯科夫替代原则、接口隔离原则和依赖倒置。所以，基本上，无论何时你写代码，你都必须记住这些核心原则，实际上，说实话，你必须把这些原则作为你写代码时的基本原则，以使它高效和有效。

使用这些原则编写的代码的管理也是一件容易的事情。最初在你的编码中使用这个原则可能看起来很奇怪，但是你需要练习使用这些原则编写你的代码，并且逐渐地它会成为你的一部分。然后，您编写的代码可以根据您满足的需求轻松地修改、扩展、重构或测试，而不会遇到任何问题。因此，只要有可能，就实践这个原则，让你的编码更好。
来源:[Java 中的坚实原理](https://java2blog.com/single-responsibility-principle-java/)

您可能还喜欢:
[java 中的 xms 和 xmx 参数](https://java2blog.com/xms-xmx-parameter-java/)
[Java 在解析时到达文件末尾](https://java2blog.com/java-reached-end-file-parsing/)
[Java 中的 NumberFormatException](https://java2blog.com/java-lang-numberformatexception/)
[Java 中的 FileNotFoundException](https://java2blog.com/filenotfoundexception-java/)
[检查 Java 中的 number 是否为数字](https://java2blog.com/java-isnumeric/)**
# Java 开发人员最应该知道的 21 个面试问题

> 原文：<https://dev.to/codegym_cc/top-21-interview-questions-for-java-developers-you-totally-should-know-4n3l>

[![](img/1e60ea5d066477c485d29f8cda50b00e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OtZiJ3UC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gjlerrkp99rfd3ksq9rw.jpeg)

Java 面试问题数不胜数，我们不可能在一篇文章中收集所有的问题。然而，根据 IT 公司人力资源经理的说法，你可以在这里找到一些非常常见的 Java 面试问题。

### 1.【拆解】`**public static void main(String args[])**`。

大一新生最受欢迎的 java 面试问题之一，也很简单。

*   `public`是访问修饰符。我们用它来指定对这个方法的访问。这里的修饰符是“public”，所以任何类都可以访问这个方法。
*   这个 Java 关键字意味着我们不用创建一个类的新对象就可以使用这个方法。
*   `Void`是方法的返回类型。这意味着该方法不返回任何值。
*   `main`是方法的名称。JVM“知道”它是应用程序的入口点(它应该有特定的签名)。Main 是主执行发生的方法。
*   这是传递给 main 方法的参数。这里我们有字符串类型的参数，当您运行 Java 应用程序时，它接受这些参数。你可以在终端上输入它们。

### 2.`equals()`和`==`有什么区别？

首先，“`==`是一个操作符，而`equals()`是一个方法。我们使用`==`运算符进行引用比较(或地址比较)，使用`equals()`方法进行内容比较。这意味着`==`检查两个对象是否指向同一个内存位置，而`equals()`比较对象中的值。

### 3.不使用`main()`方法可以执行程序吗？

很多 java 基础面试题真的很容易。比如这个。所以简短的回答是:是的，我们可以。例如，我们可以使用静态块来实现。

您可以使用静态块来初始化静态数据成员。在加载类时，它在`main`方法之前执行。

```
class Example{
 Static{ 
System.out.println("static block is invoked"); 
} 
  public static void main(String args[]){ 
   System.out.println("Now main method"); 
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode

**输出为:**

```
**static** block is invoked 
Now main method 
```

Enter fullscreen mode Exit fullscreen mode

总主方法缺失怎么办？如果您试图运行一个没有 main 方法的普通类，您会得到下一个错误:

在类测试中找不到主方法，请将主方法定义为:`public static void main (String [] args)`或者 JavaFX 应用程序类必须扩展`javafx.application.Application`。
错误本身表明，如果这是一个 JavaFX 应用程序，并且该类是从`javafx.application.Application`继承的，那么这是可能的。

### 4.什么是`immutable`对象？你能创建`immutable`对象吗？

创建后不能修改`immutable`类的对象。所以一旦你创建了它们，你就不能改变它们。如果您试图修改`Immutable`对象，您会得到一个新对象(克隆)，并在创建时更改该克隆。

一个很好的例子就是`String`，在 Java 里就是`immutable`。这意味着您不能更改对象本身，但可以更改对该对象的引用。

### 5.下面的代码创建了多少个对象？

替代#4 的 Java 技术面试问题之一。

```
String s1="Hello"; 
String s2="Hello"; 
String s3="Hello"; 
```

Enter fullscreen mode Exit fullscreen mode

答案是“只有一个”，因为 Java 有一个字符串池。当我们使用`new()`操作符创建一个字符串对象时，它会在堆内存中创建一个新对象。如果我们使用字符串语法，就像我们的例子一样，它可能会从字符串池中返回一个现有的对象，如果它已经存在的话。

### 6.下面的代码创建了多少个对象？

```
String s = new String("Hello"); 
```

Enter fullscreen mode Exit fullscreen mode

有两个对象。一个在字符串常量池中，另一个在堆中。

### 7.Java 中的`String`、`StringBuilder`、`StringBuffer`类有什么区别？

有一个顶级 java 面试问题的领军者。

首先`String`是一个不可变的类。这意味着一旦创建，您就不能修改其内容。而`StringBuffer`和`StringBuilder`是可变的类，所以您可以在以后更改它们。如果我们改变`String`对象的内容，它会创建一个新的字符串，因此，它不会修改原来的字符串。这就是为什么`StringBuffer`的表现比`String`好。

`StringBuffer`和`StringBuilder`的主要区别在于`StringBuffer`的方法是同步的，而`StringBuilder`的方法不是。

### 8.使用 literal 和使用`new()`操作符创建的`String`有什么不同吗？

确实是。如果我们用`new()`操作符创建一个字符串，它会出现在堆中，而不会添加到字符串池中。如果使用文本创建`String`,它将在堆的 Perm 区域中的`String`池中创建。

### 9.可以在 Java 中重写`private`或者`static`方法吗？

java 面试新手的棘手问题之一。你真的不能在 Java 中重写`private`或`static`方法。

您不能重写`private`方法，因为私有访问说明符的范围只在类内。当你要覆盖某个东西时，我们应该有一个父类和子类。如果超类的方法是`private`，子类不能使用，子类中的方法会被当作新方法(不被覆盖)。

`Static`方法也不能被覆盖，因为`static`方法是类本身的一部分，而不是类的任何对象的一部分。当然，你可以在子类中用相同的签名声明相同的`static`方法，但是同样，它们将被视为新方法。

### 10.`Abstract Class`和`Interface`的区别

与 OOP 原则有关的一个流行的 java 开发人员面试问题。首先，在 Java 中`interface`定义行为而`abstract class`创建层次结构。

[![](img/143eadd1ef736f0337dc82a86745533a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_qxQE4sW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vdix55tmwjjg62juyvui.png)

### 11.我们可以在一个`abstract`类中声明`static`变量和方法吗？

是的，可以在`abstract`方法中声明`static`变量和方法。不需要创建一个对象来访问静态上下文。所以我们被允许通过使用`abstract`类的名称来访问在`abstract`类中声明的静态上下文。

### 12.JVM 分配了哪些类型的内存区域？

**类区域**存储超类结构，例如，运行时常量池、字段、方法数据和方法的所有代码。

**Heap** 是一个运行时数据区，内存被分配给对象。

**堆栈**存储帧。它包含局部变量和部分结果，并参与方法调用和返回。每个线程都有一个私有的 JVM 栈，与线程同时创建。每次调用一个方法都会创建一个新的框架。当框架的方法调用完成时，框架被销毁。

**程序计数器寄存器**包含当前正在执行的 Java 虚拟机指令的地址。

**本地方法栈**包含应用中使用的所有本地方法。

### 13.为什么 java 中不允许多重继承？

那会非常复杂。想象三个阶层`A`、`B`、`C`、`C`继承`A`、`B`。现在，`A`和`B`类有相同的方法，你可以从子类对象中调用它...哪一个？`A`的还是`B`的？这里我们有歧义。如果你试图继承两个类，Java 会出现编译时错误。

### 14.可以重载`main()`方法吗？

当然，通过使用方法重载，我们可以在一个 Java 程序中拥有许多主方法。试试吧！

### 15.我们可以声明一个构造函数为`final`吗？

没有。构造函数不能被声明为`final`，因为它不能被继承。所以将构造函数声明为`final`是没有意义的。但是，如果您尝试这样做，Java 编译器会向您抛出一个错误。

### 16.我们可以将一个接口声明为`final`吗？

不，我们不能这么做。一个接口不能是`final`，因为根据它的定义，该接口应该由某个类实现。所以做一个接口`final`是没有意义的。但是，如果您尝试这样做，编译器将显示一个错误。

### 17.`static binding`和`dynamic binding`有什么区别？

编译器可以在编译时解析的`binding`称为`static`或早期绑定。所有的`static`、`private`和`final`方法中的`Binding`都是在编译时完成的。

在`Dynamic binding`中，编译器不能选择要调用的方法。Overriding 是`dynamic binding`的完美例子。在重写中，父类和子类具有相同的方法。

```
Static Binding 
class Cat{ 
 private void talk() 
{System.out.println("cat is mewing..."); 
} 
</code>
<code> public static void main(String args[]){ 
  Cat cat=new Cat(); 
  cat.talk(); 
 } 
} </code>
<code>Dynamic Binding 
class Animal{ 
 void talk(){ 
System.out.println("animal is talking..."); 
} 
} </code>
<code>class Cat extends Animal{ 
 void talk(){ 
System.out.println("cat is talking...");
} </code>
<code> public static void main(String args[]){ 
  Animal animal=new Cat(); 
  animal.talk(); 
 } 
} 
```

Enter fullscreen mode Exit fullscreen mode

### 18.如何用 Java 创建一个只读类？

您可以通过将所有的类字段设为私有来做到这一点。只读类只有 getter 方法，这些方法将类的私有属性返回给`main`方法。你不能修改这个属性，原因是缺少 setters 方法。

```
public class HockeyPlayer{ 
private String team ="Maple leaf"; 
public String getTeam(){ 
return team; 
} 
} 
```

Enter fullscreen mode Exit fullscreen mode

### 19.如何用 Java 创建只写类？

同样，您应该使所有的类字段`private`。现在，只写类应该只有 setter 方法，没有 getter。因此，我们无法读取类的属性。

```
public class HockeyPlayer{ 
private String team; 
public void setTeam(String college){ 
this.team = team; 
} 
} 
```

Enter fullscreen mode Exit fullscreen mode

### 20.每个`try`块后面必须跟着一个`catch`块，不是吗？

没有。这不是必须的。每个 try 块可以没有 catch 块。它后面可能跟有一个`catchblock`或一个 finally 块，或者根本没有它们。

```
public class Main{ 
     public static void main(String []args){ 
        try{ 
            int variable = 1; 
            System.out.println(variable/0); 
        } 
        finally 
        { 
            System.out.println("the other part of the program..."); 
        } 
     } 
} 
```

Enter fullscreen mode Exit fullscreen mode

**输出:**

```
class Main {
        public static void main(String[] args) throws IOException {
            try(InputStreamReader inputStreamReader = new InputStreamReader(System.in);
                BufferedReader reader = new BufferedReader(inputStreamReader)){
                System.out.println("test");
            }
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

附注:在 Java 8 之前，接口中的方法只能是抽象的。在 Java 8 或更新的版本中，可以定义默认方法并直接在接口中实现它们。

### 21.`throw`和`throws`关键词有什么区别？

`Throws`用于声明异常，因此它的工作方式类似于`try-catch`块。`Throw`关键字用于从方法或任何其他代码块中显式抛出异常。

`Throw`后面是`Exception`类的实例，throws 后面是异常类名。

在方法体中使用`Throw`来抛出异常。`Throws`在方法签名中用于声明方法中出现的语句中可能出现的异常。

允许一次抛出一个异常，但是您可以通过使用`throw`关键字声明它们来处理多个异常。您可以声明多个例外，例如`public void method()throws IOException`、`SQLException`。

这里我们只有一些 java 面试问题和答案。本文是采访系列的第一篇。下一个(即将推出)是关于数据结构的问题。

第一篇发表在 [CodeGym 博客](https://codegym.cc/groups/posts/78-top-21-java-interview-questions)上。
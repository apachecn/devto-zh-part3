# 101 号锅炉

> 原文：<https://dev.to/golanir/kotlin-101-420a>

Kotlin 是一种由 Jetbrains 开发的(相对)新的静态类型语言，IntelliJ idea 有人知道吗？

2011 年开源，2016 年 2 月 15 日发布 Kotlin 1.0。虽然被 JetBrains 开发人员广泛使用，但 Kotlin 的重大突破是在 Google I/O 2017 上，Kotlin 被宣布为 android 开发的官方语言。

Kotlin 的一些关键特性是:
简洁、安全、可互操作&成熟。

Kotlin 与 Java 是 100%可互操作的，你可以毫无问题地将它用于你当前的项目，你可以从 Kotlin 调用 Java 代码，反之亦然，你所需要做的就是添加一个小插件，一切都准备好了。

Kotlin 增加了许多现代特性，这些特性是坚持使用 Java 7 或 8 的组织或 Android 开发人员肯定会错过的，它的一大优势是它解决了可空性问题。

Kotlin 在当今的任何开发环境中都有一席之地，Kotlin/JVM 用于 Android，后端&运行在 JVM 上的一切，Kotlin/JS 用于前端开发人员，Kotlin/Native 用于 iOS，MacOS，Windows，Linux，WebAssembly & Android(再次)。

让我们开始吧

## 变量

在 Kotlin 中，我们像这样声明变量:

val 是一个只读的(不可变的)变量，我们必须在声明时初始化它，我们不能改变它的值。

```
val name: String = "nir" 
```

编译器足够聪明来推断类型，我们可以这样声明它

```
val name = "nir" 
```

**var** 是可变变量

```
var lastName: String = "golan" 
```

就像在 val 中一样，我们可以移除类型，我们的代码会运行得很好

```
var lastName = "golan" 
```

用 var 关键字声明的变量可以在以后更改

```
lastName = "golanster" 
```

但是我们不能改变类型，所以:

```
lastName = 123 
```

不会编译。

## 类

kotlin 中的类是用**类**关键字
声明的

```
class Person constructor(val firstName: String, var lastName: String) 
```

正如在变量声明中我们可以省略类型一样，这里我们可以省略**构造函数**

```
class Person (val firstName: String, var lastName: String) 
```

当我们在构造函数中使用 **val** 关键字时，我们声明并分配类属性。

另一个重要的类关键字是**数据**，当我们在类前面添加**数据**关键字时，我们得到 getters、setters(用于 **var** 而不是 **val** ) & toString、hashCode、equals &复制覆盖。

## 对象&类实例化

在 Kotlin 中，我们像这样实例化对象和类:

```
val person = Person("nir", "golan") 
```

注意我们没有使用新的关键字

## 功能

Kotlin 中的函数用 **fun** 关键字
声明

```
fun sum(first: Int, second: Int) {} 
```

如果函数有返回值，我们就在后面加上返回类型**，** 

```
fun sum(first: Int, second: Int): Int {} 
```

在{}中，我们定义了函数体

### 如果&当

在 kotlin 中 if & when 是表达式并且有返回值，所以在我们的函数中我们可以写

```
fun sum(first: Int, second: Int): Int {
    if (first > second) {
        first
    } else {
        second
    }
} 
```

在 Kotlin 中，我们在函数
中也有**默认值** & **命名参数**

```
fun sum(first: Int = 5, second: Int = 10): Int {
    if (first > second) {
        first
    } else {
        second
    }
} 
```

我们可以这样调用函数:

```
val number = sum(5,10) //normal call
val number = sum(first = 5, second = 10) //named parameters
val number = sum(second = 5, first = 10) //with named parameters we can
                                         //call them in any order we want
val number = sum(second = 22) //with default values we can omit
                              //any parameter and call just the one we need 
```

因为 Kotlin 是一种简洁的语言，我们可以省略一些{}，得到这个

```
fun sum(first: Int, second: Int) = if (first > second) first else second 
```

这是 Kotlin 中最接近三元算子的东西。

在 Kotlin **中，当**是一个 switch 语句时，它可以作为一个普通的 switch 语句使用，但是因为它也是一个这样使用时返回值的表达式，我们**必须**添加一个 else 子句

```
fun whatAmI(value: Any?) = when(value) {
    3 -> "value is exactly 3"
    is Int -> "double the value = ${value * 2}"
    "What the fuck?" -> "Swich case + if statement!"
    else -> "No value"
} 
```

在 Kotlin 中，一切都继承自任何，就像 Java 中的 Object 一样

在上面的用例中，我们评估**值**，我们声明**值**的值，并且基于**值**的值，我们返回值:-)

## 扩展功能

我们可以用自己的函数扩展任何 Kotlin 类，事实上，Kotlin 本身就是 Java 上的一个扩展函数。

让我们以 **Int** 为例

```
fun Int.maxValue(other: Int) = if (this > other) this else other 
```

这里我们为 Int 定义了一个扩展函数，我们传入另一个值并返回一个更大的值

```
val max = 3.maxValue(4) // returns 4 
```

我们可以在函数前面添加关键字**中缀**，从而使语法更友好一点

```
infix fun Int.maxValue(other: Int) = if (this > other) this else other
val max = 3 maxValue 4 
```

## 空安全

首先，让我们看看 NPE 或空指针异常是如何在 Java 中发生的

```
String luckyNumber = "7";
System.out.println(luckyNumber.length());
luckyNumber = null;
System.out.println(luckyNumber.length()); 
```

这里，当我们的程序到达最后一行时，它会抛出一个`java.lang.NullPointerException`，现在让我们看看 Kotlin 如何帮助我们。在 Kotlin 中，一个变量**不能**为空，如果我们像之前那样声明一个变量

```
var name: String = null 
```

我们的程序不会编译，我们会得到这样的消息: *null 不能是非 null 类型字符串的值*

为了在 Kotlin 中使用 null，我们必须在类型
中声明对 null 的支持

```
var name: String? = null 
```

**？类型末尾的**告诉编译器`name`可以为空，如果在声明中我们省略了类型，编译器会将类型推断为**字符串？**

当使用可空变量时，我们有安全和不安全的方法来处理它们。
首先，我们来看看安全的方式，

```
name?.length() 
```

**？**将检查变量是否为空，只有当变量不为空时，才会执行`length()`方法

```
name!!.length() 
```

用**！！我们告诉编译器:“我知道这可以是 null，相信我，我知道我在做什么”。
不用我告诉你，这是获得 NullPointerException 的最好方法，对吧？**

如果我们需要返回一个值，即使变量为空，我们可以使用 elvis 操作符，如果左边为空，它会返回右边的值

```
val res = name?.length() ?: 0 
```

## 收藏

在 Kotlin 中，我们有所有的基本集合:列表、数组、映射、集合、hashMap、hashSet 等等..让我们来看看可以操纵它们的一些方法。

```
val list = 1..100 // declaration of a list with the numbers 1 to 100

list.map { it * 2 } //map iterates over the list and **it**
                    //is the value currently being evaluated
    .filter { v -> v % 2 == 0 } //filter out the results that are odd 
```

现在，假设我们有一个包含 10000000000000000 个元素的列表，即使在最新的系统上，这也需要时间，为了便于讨论，假设我们需要将列表传递到一个网页上以供显示。我们不能让用户等待几分钟，直到他们在屏幕上看到一些信息，这就是**序列**出现
的地方

```
val list = 1..1999999999999900

list.asSequence
    .map { it * 2 }
    .filter { v -> v % 2 == 0 }

list.forEach{print(it)} //extension function that iterate over the list
                        //and print the current value 
```

`asSequence`将列表评估为惰性，每次添加新值时都会打印出来。

这就是现在的全部，Kotlin 是一种非常令人兴奋的语言，它涵盖了更多的主题，我将在接下来的几天里添加更多内容。
快乐编码。
# Java 构造函数概述

> 原文：<https://dev.to/manojyarramsetti5/a-brief-view-of-java-constructors-3ah2>

介绍

Java 是当前场景中新兴的按需技术之一。由于最安全、最可靠、功能强大，它成为了随选服务之一。

我们都知道对象在 Java 中起着至关重要的作用，它们是用两种方式提供的初始值创建的。以这种方式初始化对象的变量是一种繁琐的方法。一种方法是使用点运算符访问实例变量，然后单独赋值。另一种方法是使用构造函数——这是在 Java 中给变量赋值的最简单也是最受欢迎的方法。

现在，让我们讨论一下构造函数以及它们在 Java 中给变量赋值的重要性。

构造器

构造器是将结构或类转化为面向对象编程的特殊过程，它向我们初始化一个该类型的对象。可用于定义对象成员的值，可以是用户定义的值，也可以是默认值。构造函数是方法和变量的实例，它通常与类同名，

1.  构造器的规则:

a.构造函数名应该与类名相同。b .构造函数不能有任何返回类型，即使是 Void。

1.  构造函数在对象创建期间执行。
2.  一个构造函数可以有 n 个参数。
3.  在构造函数中声明的变量称为局部变量。
4.  施工人员可分为两类:

    a.默认构造函数。
    b .用户定义构造函数。

用户定义的构造函数:

用户生成的构造函数称为用户自定义的
构造函数。它也被称为显式构造函数。
它可以分为:

I)用户定义参数化构造函数:如果[构造函数](https://tekslate.com/reactjs-training/)包含任何参数，或者如果
用户在构造函数内部传递任何值，则称为用户定义参数化构造函数。

例如:

public class Demo {
Demo(String S，int i) //参数化构造函数
{
system . out . println(" I 的值是"+I+" \ n S 的值是"+S)；

}
void display()//方法
{
System.out.println("我在显示方法内部")；
}
public static void main(String[]args)
{
Demo d = new Demo(" Rashda "，10)；
d.display()。
}
}

按类运行您的程序，直到输出窗口显示以下内容:

输出:

I 的值是 10
S 的值是 Rashda
I 在显示方法里面

ii)用户定义非参数化构造函数:如果构造函数没有任何参数，或者如果
用户没有在构造函数内部传递任何值，则称为用户定义非参数化
构造函数。

例如:

public class Demo {
Demo() //非参数化构造函数
{
System.out.println("我在构造函数内部")；
}
void display()//方法
{
System.out.println("我在显示方法内部")；
}
public static void main(String[]args){
Demo d = new Demo()；
d.display()。
}
}

然后，程序将非参数化的构造函数移到下一行代码

输出:

我在构造函数里面
我在显示方法里面

默认构造函数:

1.  编译器生成的构造函数称为默认构造函数。
2.  默认构造函数没有任何实现(语句)。
3.  每个类都有一个默认的构造函数。
4.  默认构造函数也称为隐式构造函数。

例如:

1.  公开课演示{

public static void main(String[]args){
Demo d = new Demo(10)；
}
}
输出:给出编译时错误，因为没有参数化的构造函数，它出现在
类中。

1.  public class Sample { public static void main(String[]args){ Sample s = new Sample()；} }

现在它变成了给出编译时错误，因为没有参数化的构造函数

输出:

上述程序中没有编译时错误
JVM 执行了编译器生成的默认构造函数。

注意:

1.  如果用户在类中定义了任何构造函数，那么编译器不会生成任何默认的构造函数。
2.  如果用户没有在类中定义任何构造函数，那么只有编译器会生成默认的构造函数。
3.  构造函数构造一个对象并初始化状态。Ex:公共类 Sample { Sample(int I){ system . out . println(I)；} public static void main(String[]args){ Sample s = new Sample(10)；样本 s1 =新样本()；} }

Output:
给出编译时错误，因为上面的
类中没有默认的构造函数。

建造者的目的:

1.  对象创建。
2.  初始化状态，即初始化实例变量的局部变量值。

注意:

1.  我们不能在方法和构造函数之外访问局部变量值。
2.  局部变量值在方法范围或构造函数范围内。
3.  构造函数不能返回任何值。

例如:

班级学生
{
字符串名称；

int id
字符串 collegename
Student(String n，int i，String c) //构造函数
{
name = n；
id = I；
college name = c；
}
void display()
{ system . out . println(" Name is "+Name+" \ n Id is "+Id+" \ n 学院名为"college name
}
}
public class Sample {
public static void main(String[]args){
Student S1 = new Student(" John "，1，" KIT ")；
S1 . display()；
学生 S2 =新生(“迈克”，2，“基特”)；
S2 . display()；
学生 S3 =新生(“亚当”，3，“基特”)；
S3 . display()；
}
}

输入
打印“姓名”“ID”“学院名称”

输出:

姓名是约翰
Id 是 1
学院名称是基特
姓名是迈克
Id 是 2
学院名称是基特
姓名是亚当
Id 是 3
学院名称是基特

注意:

每当局部变量和实例变量名称相同时，JVM 就会混淆
来识别局部变量和实例变量。如果我们想克服这个
问题，我们必须使用这个关键字。这是一个关键字，它总是指当前类的实例
变量。

例如:

1.  class Student { String name

int id
字符串 collegename
Student(String name，int id，String college name)//Constructor
{
name = name；
id = id；
college name = college name；
}
void display()
{
system . out . println(" Name is "+Name+" \ n Id is "+Id+" \ n 学院名为"college name
}
}
public class Sample {
public static void main(String[]args){
Student S1 = new Student(" John "，1，" KIT ")；
S1 . display()；
学生 S2 =新生(“迈克”，2，“基特”)；
S2 . display()；
学生 S3 =新生(“亚当”，3，“基特”)；
S3 . display()；
}
}

输入:
打印"名称=姓名；
id = id；
学院名=学院名

输出:
名称为空
Id 为 0
学院名称为空
名称为空
Id 为 0
学院名称为空
名称为空
Id 为 0
学院名称为空

Ex2:
班级学生
{
字符串名称；
int id；
字符串 collegename
Student(String name，int id，String college name)//Constructor
{

this.name = name
this . id = id；
this . college name = college name；
}
void display()
{
system . out . println(" Name is "+Name+" \ n Id is "+Id+" \ n college Name "+college Name)；
}
}
public class Sample {
public static void main(String[]args){
Student S1 = new Student(" John "，1，" KIT ")；
S1 . display()；
学生 S2 =新生(“迈克”，2，“基特”)；
S2 . display()；
学生 S3 =新生(“亚当”，3，“基特”)；
S3 . display()；
}
}

输入:
字符串名称；
int id；
字符串 collegename

打印“this.name = name
this . id = id；
this . college name = college name；"

输出:

姓名是约翰
Id 是 1
学院名称是基特
姓名是迈克
Id 是 2
学院名称是基特
姓名是亚当
Id 是 3
学院名称是基特

# 默认值:

string = Null
int = 0
byte = 0
short = 0
long = 0
float = 0.0
double = 0.0
char = space
Boolean = false

结论:

因此，构造函数增加了 Java 中任何类的可用性和可读性。测试类是很困难的，因为在不知道构造函数及其工作原理的情况下创建对象。

在本文中，我们已经了解了构造函数在 Java 中的工作方式，以及为什么它们是必不可少的。事实上，使用构造函数变得更加容易，因为您可以使用依赖注入进行类初始化。在使用像 Google Guice 和 Spring frameworks 这样的开源软件时，使用合适的构造函数来使用和测试这些类将变得很容易。
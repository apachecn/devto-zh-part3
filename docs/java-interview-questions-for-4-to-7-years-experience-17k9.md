# 4-7 年 Java 面试问题

> 原文：<https://dev.to/arpitmandliya/java-interview-questions-for-4-to-7-years-experience-17k9>

在本帖中，我们将看到[5 到 6 年经验的 Java 面试问题](https://java2blog.com/java-interview-questions/)。
当你拥有 5 年的 [java](https://java2blog.com/core-java-tutorial-for-beginners-experienced/) 开发经验时，你需要很好地理解[集合](https://www.java2blog.com/java-collections-interview-questions/)、[多线程](https://www.java2blog.com/java-multithreading-interview-questions-and-answers/)概念。

[![](img/f224975bfe6f366a2370c2b5a6d5f3e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oBdnjx0X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6cbw1ztv8nunbra4v1ek.png)

如果你正在寻找下面的问题，那么这篇文章也会帮助你。

*   4 年经验的 Java 面试问题
*   6 年经验 Java 面试问题
*   7 年经验 Java 面试问题

这里有一些 5 年 java 程序员最常问的问题。你可能会发现有些问题非常简单，但是相信我，大多数开发人员都没有回答这些问题。

### 1。猜测下面程序的输出。

```
package org.arpit.java2blog;
class A
{
    void m1()
    {
        System.out.println("In m1 A");
    }
}
class B extends A
{
    void m1()
    {
        System.out.println("In m1 B");
    }
    void m2()
    {
        System.out.println("In m2 B");
    }
}
public class Test {

    public static void main(String[] args) {
        A a=new B();
        a.m2();
    }
}

```

**输出会是什么？**
**A .在 m2 B**
**B .编译时出错**
**C .运行时出错**

B.编译时错误
会有编译时错误。即使我们将 B 的对象赋给 A 的引用，我们也只能从 A 的引用中调用 A 中的方法。

### 2。猜测下面程序的输出。

```
package org.arpit.java2blog;

class A
{
    void m1() throws ArrayIndexOutOfBoundsException
    {
        System.out.println("In m1 A");
    }
}
class B extends A
{
    void m1() throws IndexOutOfBoundsException
    {
        System.out.println("In m1 B");
    }
}
public class Test {

    public static void main(String[] args) {
        A a=new B();
        a.m1();
    }
}

```

**输出会是什么？**
**A .在 m1 B**
**B .编译时出错**
**C .运行时出错**

A.在 m1 B
中，这将很好地工作，因为 ArrayIndexOutOfBoundsException 和 IndexOutOfBoundsException 是运行时异常，并且在方法重写时没有运行时异常的规则。

### 3。猜测下面程序的输出。

```
package org.arpit.java2blog;

import java.io.IOException;

class A
{
    void m1() throws IOException
    {
        System.out.println("In m1 A");
    }
}
class B extends A
{
    void m1() throws Exception
    {
        System.out.println("In m1 B");
    }
}
public class Test {

    public static void main(String[] args) {
        A a=new B();
        try {
            a.m1();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```

**输出会是什么？**
**A .在 m1 B**
**B .编译时出错**
**C .运行时出错**

B.编译时错误
由于 IOException 和 Exception 是被检查的异常，所以在方法覆盖时不能扩大异常的范围。

### 4。下面的程序会发生什么？

```
class A
{
    synchronized void m1()
    {
        System.out.println("In m1 A");
    }
    void m2()
    {
        System.out.println("In m2 A");
    }
}

```

有两个线程 T1 和 T2。T1 正在访问 m1 方法。T2 能够同时在同一个实例上访问 m2 方法吗？

是的，T2 将能够访问 m2，因为它不需要锁定来访问 m2 方法。/div >

### 5。下面的程序会发生什么？

```
class A
{
    synchronized void m1()
    {
        System.out.println("In m1 A");
    }
    synchronized void m2()
    {
        System.out.println("In m2 A");
    }
}

```

有两个线程 T1 和 T2。T1 正在访问 m1 方法。T2 能够同时在同一个实例上访问 m2 方法吗？

不，T2 将不能访问 m2，因为它需要锁来访问 m2 方法，该方法已经被 T1 线程占用。

### 6。下面的程序会发生什么？

```
class A
{
    synchronized static void m1()
    {
        System.out.println("In m1 A");
    }
    synchronized void m2()
    {
        System.out.println("In m2 A");
    }
}

```

有两个线程 T1 和 T2。T1 正在访问 m1 方法。T2 能够同时在同一个实例上访问 m2 方法吗？

是的，T2 将能够访问 m2，因为它需要对象级锁来访问 m2 方法，而 T1 线程已经获取了类级锁。你可以阅读更多关于[对象级锁和](https://java2blog.com/object-level-locking-vs-class-level-locking-java/)类级锁的内容。

### 7。猜测下面程序的输出。

```
package org.arpit.java2blog;

import java.util.HashSet;

public class Customer {

    String name;
    int age;

    Customer(String name,int age)
    {
        this.name=name;
        this.age=age;
    }
    public static void main(String[] args) {

        Customer c1= new Customer("John",20);
        Customer c2= new Customer("John",20);

        HashSet<Customer> customerSet=new HashSet<>();
        customerSet.add(c1);
        customerSet.add(c2);
        System.out.println(customerSet.size());
    }

    // getters and setters
}

```

输出将是 2，因为我们没有在 Customer 类中实现 hashcode 和 equals 方法。

### 8。猜测下面程序的输出。

```
package org.arpit.java2blog;

public class Employee {
    String name;
    int age;

    public Employee(String name,int age)
    {
        this.name=name;
        this.age=age;
    }

    @Override
    public int hashCode() {
        final int prime = 31;
        int result = 1;
        result = prime * result + age;
        result = prime * result + ((name == null) ? 0 : name.hashCode());
        return result;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj)
            return true;
        if (obj == null)
            return false;
        if (getClass() != obj.getClass())
            return false;
        Employee other = (Employee) obj;
        if (age != other.age)
            return false;
        if (name == null) {
            if (other.name != null)
                return false;
        } else if (!name.equals(other.name))
            return false;
        return true;
    }

    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }   
}

```

主类

```
package org.arpit.java2blog;

import java.util.HashMap;
import java.util.Map;

public class HashMapMain {

    public static void main(String[] args) {
        Employee emp1=new Employee("Martin",27);
        Map<Employee,String> hm=new HashMap<Employee,String>();
        hm.put(emp1, "Verified");
        emp1.setName("John");
        System.out.println(hm.get(emp1));
    }
}

```

[Show Hide type = " question 8 " more _ text = " Show Answer " less _ text = " Hide Answers "]
**输出将为空。**
我们已经使用姓名和年龄属性实现了 Employee 的 hashcode 和 equals 方法，所以当你把 emp1 作为 hashmap 中的 key 时，它会使用 hashcode 和 equals 方法，会被放入 HashMap 中。
在将 emp1 放入 HashMap 后，我们已经更改了雇员的名字，所以当你试图使用 hm.get(emp1)从 HashMap 中检索元素时，你将无法获得我们之前放入的对象，它将返回 null。
[/showhide]

### 9。如何为您的应用确定年轻一代和老一代的规模？

这取决于应用的性质。如果你有很多临时对象，那么就会有很多小的 gc。您可以提供参数 XX:NewRatio=1，将 50%分配给年轻一代，50%分配给年老一代。默认情况下，NewRatio=2，因此年轻代是总堆的 1/3。
类似地，如果你有太多的长寿命对象，那么你可能需要通过增加 NewRatio 的值来增加使用权空间的大小。

### 10。java 中的垃圾收集是什么？

垃圾收集是识别 java 堆上已用和未用对象并从堆中移除未用对象的过程。活着的对象意味着对象仍然被程序的某个部分引用。未使用的对象意味着对象没有被程序的任何部分引用，可以进行垃圾收集。
程序员不用像 C 或 C++那样做手工垃圾收集。Java 负责

### 11。java 中的垃圾收集器有哪些类型？

你可以在这里看到[详细的回答。](https://java2blog.com/garbage-collection-java/)

### 12。Collection.synchronizedMap(map)和 ConcurrentHashMap 有什么区别？

当您使用 Collection.synchronizedMap(Map)使 map 线程安全时，它会锁定整个 map 对象，但 ConcurrentHashMap 不会锁定整个 map，它只是锁定它的一部分(Segment)。你可以在这里阅读更多关于 [ConcurrentHashMap 的信息。](https://www.java2blog.com/concurrenthashmap-in-java/)

### 13。当你运行下面的代码时会发生什么

```
package org.arpit.java2blog;

import java.util.HashMap;
import java.util.Iterator;
import java.util.Map.Entry;

public class IterateMapMain {

    public static void main(String args[])
    {
        // HashMap with Country as key and capital as value
        HashMap<String,String> map=new HashMap<String,String>();
        map.put("India","Delhi");
        map.put("Japan","Tokyo");
        map.put("France","Paris");
        map.put("Russia","Moscow");

        // Iterating java iterator
        System.out.println("Iterating java Iterator");
        Iterator<String> countryKeySetIterator=map.keySet().iterator();
        while(countryKeySetIterator.hasNext()){
            String countryKey=countryKeySetIterator.next();
            map.put("Nepal", "KathMandu");
            System.out.println(countryKey);

        }
        System.out.println("-----------------------------");
    }

}

```

**你会得到下面的输出**

迭代 java IteratorException 在线程“main”Java . util . concurrentmodificationexception
at Java . base/Java . util . hashmap $ hashiterator . nextnode(hashmap . Java:1489)
at Java . base/Java . util . hashmap $ key iterator . next(hashmap . Java:1512)
at org . arpit . Java 2 blog . itemapmain . main(itemapmain . Java:24)

每当你在迭代过程中试图改变 hashmap 的计数时，它将抛出 Java . util . ConcurrentModificationException，因为我们调用 iterator.next，它检查 modCount 和初始计数，如果不匹配，它将抛出 ConcurrentModificationException。

### 14。写一个程序用线程按顺序打印奇数偶数？

下面是用线程按顺序打印奇数和偶数的程序。

### 15。你在项目中使用过哪种设计模式？

你可以说出一些设计模式，比如单例模式、观察者模式等等。你可能在你的项目中用过。

### 16。什么是单例设计模式中的双层锁定？

单一设计模式中的双级锁定用于确保线程安全。

```
public static Singleton getInstance()
    { 
        if (_instance == null) { // Single Checked 
            synchronized (Singleton.class) 
            { 
                if (_instance == null)// Double checked 

                { 
                    _instance = new Singleton(); 
                }
            } 
        } return _instance; 
    }

```

假设两个线程(T1 和 T2)检查是否为空，并且都到达了 synchronized (Singleton.class)。T1 获取锁并创建 Singleton 实例，然后返回。现在 T2 进入 synchronized 块，因为我们已经再次检查了 null，它不会再创建对象。

### 17。用 BlockingQueue 写个程序实现生产者-消费者问题？

你可以在这里找到[的详细答案](https://www.java2blog.com/blockingqueue-in-java/)

### 18。你做过 Java 8 吗？能否分享一下 Java 8 的主要变化？

如果你使用过 Java 8，你可以分享一些主要的改变，比如流，lambada 表达式，接口中的默认方法等等。

### 19。你做过连载吗？你能区分可序列化和可外部化吗？

你可以在这里找到[的详细答案](https://www.java2blog.com/difference-between-serializable-and-externalizable-in-java/)

### 20。如何在应用程序中检测内存泄漏？

这个问题没有简单的答案。您可以通过 JVisualVM 进行线程转储，并在 [eclipse 内存分析工具](https://www.eclipse.org/mat/)中进行分析。
5-6 年经验 Java 面试问题[就这些了。
您可能还喜欢:](https://java2blog.com/java-interview-questions/)

*   [核心 java 面试问题](http://www.java2blog.com/core-java-interview-questions-and-answers.html)
*   [Java 集合面试问题](http://www.java2blog.com/java-collections-interview-questions.html)
*   [Java 字符串面试问题](http://www.java2blog.com/java-string-interview-questions-and-answers.html)
*   [Java 中的哎呀面试问题](http://www.java2blog.com/oops-interview-questions-answers-java.html)
*   [Java 多线程面试问题](http://www.java2blog.com/java-multithreading-interview-questions-and-answers.html)
*   [Java 异常处理面试问题](http://www.java2blog.com/java-exception-handling-interview-questions-and-answers.html)
*   [Java 序列化面试问题用 java](http://www.java2blog.com/java-serialization-interview-questions-and-answers.html)
*   [web 服务面试问题](http://www.java2blog.com/web-services-interview-questions.html)
*   [restful web 服务面试问题](http://www.java2blog.com/restful-web-services-interview-questions.html)
*   [春季面试问题](http://www.java2blog.com/spring-interview-questions-and-answers.html)
*   [春季新兵面试问题](https://java2blog.com/spring-boot-interview-questions/)
*   [冬眠面试问题](http://www.java2blog.com/hibernate-interview-questions-and-answers.html)
*   此环境中不提供编译器。也许你跑的是 JRE 而不是 JDK？
*   [无法为 java 中的对象堆保留足够的空间](https://java2blog.com/could-not-reserve-enough-space-for-object-heap/)
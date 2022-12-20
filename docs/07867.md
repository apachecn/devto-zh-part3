# 反编译科特林的程序:数据类

> 原文：<https://dev.to/yuyabu/decompiling-kotlin-s-program-data-class-4lc6>

如果你是一个 Java 程序员，你可能厌倦了一次麻烦的 setter、getter、hashcode、toString 的实现。

IDE 不是自动实现了吗，或者用 lombok 之类的工具不去深入思考这个问题？

在 Kotlin 中，有一种机制可以通过在类声明的位置附加限定符“data”来自动生成这些麻烦的声明

这是数据类(人员类)的示例。

```
data class Person (val name: String, val age: Int) 
```

这就是如何使用 person 类。

```
var bob = Person ("Bob", 33)
    var bob 2 = Person ("Bob", 33)
    println (bob === bob2) // false
    println (bob == bob2) // true
    println (bob.equals (bob2)) // true
    println (bob.hashCode ()) // 2075948
    println (bob.toString ()) // Person (name = Bob, age = 33) 
```

## 用 JAD 反编译

反编译 person 类并将自动实现的代码转换成 Java

```
$ jad Person.class
Parsing Person.class ... Generating Person.jad
Could not fully decompile method hashCode 
```

hashcode 的方法无法反编译。
我会在下一篇文章检查`hashcode()`。

java 的反编译结果:Person 类

```
import kotlin.jvm.internal.Intrinsics;

public final class Person
{

    public final String getName ()
    {
        return name;
    }

    public final int getAge ()
    {
        return age;
    }

    public Person (String name, int age)
    {
        Intrinsics.checkParameterIsNotNull (name, "name");
        super ();
        this.name = name;
        this.age = age;
    }

    public final String component 1 ()
    {
        return name;
    }

    public final int component 2 ()
    {
        return age;
    }

    public final Person copy (String name, int age)
    {
        Intrinsics.checkParameterIsNotNull (name, "name");
        return new Person (name, age);
    }

    public static volatile Person copy $ default (Person person, String s, int i, int j, Object obj)
    {
        if ((j & 1)! = 0)
            s = person.name;
        if ((j & 2)! = 0)
            i = person.age;
        return person.copy (s, i);
    }

    public String toString ()
    {
        append ("age ="). append (age). append (")"). toString (); return (new StringBuilder ()). append ("Person (name =
    }

    public int hashCode ()
    {
        name;
        if (name == null) goto _ L 2; else goto _ L 1
_L1:
        hashCode ();
          goto _ L 3
_L2:
        JVM INSTR pop;
        false;
_L3:
        31;
        JVM INSTR imul;
        age;
        JVM INSTR iadd;
        return;
    }

    public boolean equals (Object obj)
    {
label0:
        {
            if (this! = obj)
            {
                if (! (obj instance of Person))
                    break label 0;
                Person person = (Person) obj;
                if (! Intrinsics.areEqual (name, person.name) || (age! = person.age))
                    break label 0;
            }
            return true;
        }
        return false;
    }

    private final String name;
    private final int age;
} 
```

现在，我们检查了 hashCode、equals、toString、copy、# copy 方法的自动生成。下次，我会检查 hashcode 的字节码。

感谢您的阅读。
# 反编译和反汇编从 Kotlin 源代码编译的 JVM 字节码

> 原文：<https://dev.to/yuyabu/decompile-and-dissasemble-jvm-bytecode-compiled-from-kotlin-source-code-2cd9>

这是一个尝试使用 javap(反汇编程序)和 jad(反编译程序)的 Kotlin 代码的实验。

```
fun main (args: Array <String>) {
    println ("Hello, World!")
} 
```

代码来源
[https://kotlinlang.org/docs/tutorials/command-line.html](https://kotlinlang.org/docs/tutorials/command-line.html)

像其他 JVM 语言一样，Kotlin 被编译成一个类文件，可作为 Java 执行。
我们可以验证是因为 javap 可以用 jad 显示 JVM 的助记符并反编译为 java 源代码。

编译这个。

```
kotlinc hello.kt -include-runtime -d hello.jar 
```

提取 jar 并对 hello.class 使用 javap，jad

```
jar xf hello.jar 
```

# 环境

马科斯高塞拉 10

# javap(反汇编器)

## 准备

这是不必要的，因为它从一开始就在 JDK

## 执行

```
javap - c HelloKt.class 
```

结果

```
Compiled from "hello.kt"
public final class HelloKt {
  public static final void main (java.lang.String []);
    Code:
       0: aload_ 0
       1: ldc # 9 // String args
       3: invokestatic # 15 // Method kotlin / jvm / internal / Intrinsics.checkParameterIsNotNull: (Ljava / lang / Object; Ljava / lang / String;) V
       6: ldc # 17 // String Hello, World!
       8: astore_1
       9: getstatic # 23 // Field java / lang / System.out: Ljava / io / PrintStream;
      12: aload_1
      13: invokevirtual # 29 // Method java / io / PrintStream.println: (Ljava / lang / Object;) V
      16: return
} 
```

# jad(反编译器)

## 准备

如何介绍 jad

```
brew tap caskroom / cask
brew install caskroom / cask / jad 
```

## 执行

您可以使用-a 选项将助记符显示为注释。

命令

```
$ jad -a HelloKt.class
$ cat HelloKt.jad 
```

```
import java.io.PrintStream;
import kotlin.jvm.internal.Intrinsics;

public final class HelloKt
{

    public static final void main (String args [])
    {
        Intrinsics.checkParameterIsNotNull (args, "args");
    // 0 0: aload_ 0
    // 1 1: ldc 1 # 9 <String "args">
    // 2 3: invokestatic # 15 <Method void Intrinsics.checkParameterIsNotNull (Object, String)>
        String s = "Hello, World!";
    // 3 6: ldc 1 # 17 <String "Hello, World!">
    // 4 8: astore_ 1
        System.out.println (s);
    // 5 9: getstatic # 23 <Field PrintStream System.out>
    // 6 12: aload - 1
    // 7 13: invokevirtual # 29 <Method void PrintStream.println (Object)>
    // 8 16: return
    }
} 
```

这很容易介绍和分析。
我以后会检查 Kotlin 具体函数是如何表示和转换成 Java 的。
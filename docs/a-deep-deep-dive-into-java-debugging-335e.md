# 对 Java 调试的深入探究

> 原文：<https://dev.to/atlassian/a-deep-deep-dive-into-java-debugging-335e>

这是一个 Java 调试之旅的故事，它始于一个我无法回答的关于 Java 堆栈跟踪的问题。作为一名长期的 Java 程序员，开发人员在语言中遇到不寻常的问题时会向我寻求帮助。潜入并熟悉 Java 的所有黑暗角落是我真正喜欢的事情，主要是因为我对这种语言有了更好的理解，也因为它为我们的团队提供了更好的工具来解决日常问题...以及不寻常的。

我们的旅行将带我们深入了解 Java lambdas 和方法引用，并以快速浏览 JVM 代码结束。我们将使用一些调试工具和技术来解决这一切，并了解一些实现细节和诊断 JVM 选项。这是一个很好的例子，说明有了源，你可以揭开一个普遍现象的神秘面纱，比如丢帧。

## 这一切都始于一个 NullPointerException ...

一位同事给了我以下堆栈跟踪:

```
java.lang.NullPointerException
    at java.util.stream.ReferencePipeline$3$1.accept(ReferencePipeline.java:193)
    at java.util.Collections$2.tryAdvance(Collections.java:4717)
    at java.util.Collections$2.forEachRemaining(Collections.java:4725)
    at java.util.stream.AbstractPipeline.copyInto(AbstractPipeline.java:481)
    at java.util.stream.AbstractPipeline.wrapAndCopyInto(AbstractPipeline.java:471)
    at java.util.stream.ReduceOps$ReduceOp.evaluateSequential(ReduceOps.java:708)
    at java.util.stream.AbstractPipeline.evaluate(AbstractPipeline.java:234)
    at java.util.stream.ReferencePipeline.collect(ReferencePipeline.java:499)
    at com.atlassian.jira.jql.util.ParentEpicFinalTerminalClauseFactory.getFinalTerminalQuery(ParentEpicFinalTerminalClauseFactory.java:47)
```

This was accompanied by another 364 less-interesting frames. What's interesting, and what brought him to me, is the code of the first frame. It's from the JDK (build 1.8.0_172-b11), and it is as follows:

```
ReferencePipeline.java
public final  Stream map(Function super P_OUT, ? extends R> mapper) {
    Objects.requireNonNull(mapper);
    return new StatelessOp(this, StreamShape.REFERENCE,
                                 StreamOpFlag.NOT_SORTED | StreamOpFlag.NOT_DISTINCT) {
        @Override
        Sink opWrapSink(int flags, Sink sink) {
            return new Sink.ChainedReference<p_out r="">(sink) {
                @Override</p_out> public void accept(P_OUT u) {
                    downstream.accept(mapper.apply(u));
                }
            };
        }
    };
}

```

手头的问题是，“第 193 行的空值是什么(上面用红色突出显示的)？”它不可能是制图师因为:

*   它是本地的，所以不能从这个代码之外进行更新
*   在用法之前有一个 requireNonNull ，所以它以 not null 开始，并且
*   它实际上是 final 并在内部类中使用，所以它不可能被更改。关于*的完整细节，你可以阅读 [Java 语言规范，第 8 版，第 4.12.4 节](https://docs.oracle.com/javase/specs/jls/se8/html/jls-4.html#jls-4.12.4)，但是 TL；DR:它可能是 final ，因此被视为 final ，因为内部类引用需要它。*

它不可能是下游的，因为如果你追踪它的声明你会发现:

```
Sink.java
static abstract class ChainedReference<T, E_OUT> implements Sink {
    protected final Sink<? super E_OUT> downstream;

    public ChainedReference(Sink<? super E_OUT> downstream) {
        this.downstream = Objects.requireNonNull(downstream);
    }

```

如你所见:

*   下游的字段是最终的，
*   初始化时有一个 requireNonNull ，并且
*   在构造函数中没有使用 this 或 super ，所以我们不能在部分构造的对象上执行代码。

我的下一个想法是，也许 P_OUT 是一个装箱类型，就像 Integer 或 Long ，并且为了调用 mapper.apply ，编译器已经插入了一个 u. unboxing，如果引用是 null ，那么 Unboxing 会抛出一个 NullPointerException (详见 [JLS 8，5.1.8](https://docs.oracle.com/javase/specs/jls/se8/html/jls-5.html#jls-5.1.8) )。比如像 int value = getValue()这样的代码；T19】

如果 getValue() 被声明为返回整数而实际返回空值，则会抛出异常，因为编译器插入了对 Integer.intValue 的调用，将返回的整数转换为 int 。然而，在这种情况下，查看getfinalmterminalquery的代码会发现 P_OUT 是com . atlassian . jira . issue . issue，它没有被装箱——它是吉拉的主发布接口。

## 空在哪里？

虽然这些问题没有找到 NullPointerException 的来源，但它们确实揭示了映射器是Issue::getId——也就是说——一个*方法引用* ( 按照 [JLS 8，15.13](https://docs.oracle.com/javase/specs/jls/se8/html/jls-15.html#jls-15.13) )，这似乎不同寻常，足够有趣，值得进一步研究。在这一点上，我有些困惑，但也很兴奋，因为我无法解释我所看到的...这意味着我将要学习新的东西！于是我拿出了一点的的测试代码的由上面的沸下到下面的代码:

```
Main.java
public class Main {
    static class Thing {
        Long
        getId() {
            return 163L;
        }
    }

    public static void main(String[] args) {
        Thing thing = null;
        System.out.println(
            Collections.singleton(thing).stream().map(Thing::getId).collect(toList())
        );
    }
}

```

果然，运行这段代码让我得到了一个 NullPointerException ，它的堆栈与上面的足够相似，至少在尖端是这样。顺便说一下，如果你想亲自看看，你可以使用 git@bitbucket.org 的 git 克隆[:atlassian/missing-frame . git](mailto:git@bitbucket.org)来克隆这个库。自述文件中有一些现成的命令行。

换句话说，如果你对一个 null 引用应用一个有效的方法引用(比如例子中的 Thing::getId ，你会得到一个 NullPointerException 。

这并不奇怪。令人惊讶的是，在您了解这一点之前，您不会看到调用该函数的框架。你*当然不能*看到 getId 的帧，因为那将意味着你在一个成员函数中，而这个为空，这个在你看的时候不会发生。这与流的使用无关顺便说一下，你可以从

```
Thing thing = null;
Function<Thing, Long> function = Thing::getId;
function.apply(thing);

```

但是在我写测试代码的时候我并不知道。这是编写测试代码的一个好处，你可以自由地编辑它和摆弄到底是什么导致了你正在研究的效果。

## 但哪里来的例外抛出？

在 IntelliJ 中，你可以使用 *查看/显示字节码* 对这些例子查看 JVM 字节码。其他 ide 也有类似的功能。您也可以从命令行使用 javap 。在任何情况下，你都会看到类似于:

```
INVOKEDYNAMIC apply()Ljava/util/function/Function; [
  // handle kind 0x6 : INVOKESTATIC
  java/lang/invoke/LambdaMetafactory.metafactory(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodHandle;Ljava/lang/invoke/MethodType;)Ljava/lang/invoke/CallSite;
  // arguments:
  (Ljava/lang/Object;)Ljava/lang/Object;, 
  // handle kind 0x5 : INVOKEVIRTUAL
  com/atlassian/rgates/missingframe/Main$Thing.getId()Ljava/lang/Long;, 
  (Lcom/atlassian/rgates/missingframe/Main$Thing;)Ljava/lang/Long;
]
```

What is invokedynamic, you say? In this case, we need to break out the [Java Virtual Machine Specification, version 8, section 6.5](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.invokedynamic), which tells us that some information is looked up and then resolved (as per [JVMS 8, section 5.4.3.6](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-5.html#jvms-5.4.3.6)) to produce a MethodHandle object. This is then invoke()d to get a CallSite which is bound to that instruction, effectively caching the costly lookup. Then, we invokeExact the CallSite.target to get a Function object. Whoah. It's worth noting this is not what is throwing – it hasn't even looked at the null reference yet, and the spec is quite clear that *an "invokedynamic instruction which is bound to a call site object never throws a NullPointerException or …"*.

那么我们得到的函数是什么呢？如果你在 IntelliJ 中查看它，例如通过断开 NullPointerException 的构造函数并检查 mapper ，你会看到一个声称是com . atlassian . rgates . missing frame . main $ $ Lambda $ 1/6738746的对象，但你不会在你的源代码中找到它。这些类是由 INVOKEDYNAMIC 操作码中引用的λmetafactory动态生成的。编写它们的代码位于有趣地命名为的 Java . lang . invoke . innerclasslambdametacfactory # spininner class中。值得注意的是，它们不通过普通的 ClassLoader 机械，而是使用奇妙的sun . misc . unsafe # define anonymous class来装载自己。

不管怎样，现在我已经到了这一步，我必须继续挖掘。因此，我通过评估新的 file output stream(" lambda class . class ")，将由spinner class创建的类转储到一个文件中。在调试器中写(classBytes) 。这是我在调试时常用的技巧——评估一个有意产生副作用的表达式。我们想要检查一些数据—class bytes在这个例子中。为了检查它，我们想把它传递给另一个工具，所以我们需要把它放在一个文件中。该表达式具有将 classBytes 写入文件 lambdaClass.class 的副作用，我可以使用它进行进一步的处理。一旦我在一个文件中有了来自动态构建类的字节，我就可以使用 javap 实用程序来转储它。

```
0: aload_1
1: checkcast     #15                 // class com/atlassian/rgates/missingframe/Main$Thing
4: invokevirtual #19                 // Method com/atlassian/rgates/missingframe/Main$Thing.getId:()Ljava/lang/Long;
7: areturn

```

瞧！抛出 NullPointerException 的是 invokevirtual ，根据 [JVMS 8，6.5 节](https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-6.html#jvms-6.5.invokevirtual) : *如果* objectref *为空，* invokevirtual *指令抛出NullPointerException*。这就是人们可能期望的——一个运行时生成的包装器，它调用由方法引用命名的函数。

## 但是框架还是不见了！

总共应该有一个应用的框架。事实上，如果你在家玩，像我一样突破了 NullPointerException 构造函数，你可以看到栈上的 apply 帧。但是，如果您单步执行代码，直到调用Java . lang . throwable # fillInStackTrace(int)之后，您将不会在 Throwable.getStackTrace 的输出中找到该帧。至此，剩下唯一要考察的地方就是 fillInStackTrace ，当然那是原生。所以你需要跳转到 OpenJdk 源码，在hotspot/src/share/VM/class file/Java classs . CPP中查看void Java _ lang _ Throwable::fill _ in _ stack _ trace(Handle Throwable，methodHandle 方法，TRAPS) 。我没有搜索整个方法，但是我跳过了足够的时间来找到:

```
javaClasses.cpp
if (method->is_hidden()) {
  if (skip_hidden)  continue;
}
```

Methods can be *hidden*? TIL. I wonder who calls set_hidden? As it turns out, void ClassFileParser::MethodAnnotationCollector::apply_to(methodHandle m) says:

```
classFileParser.cpp
if (has_annotation(_method_LambdaForm_Hidden))
  m->set_hidden(true);
```

A bit more digging reveals that the value of _method_LambdaForm_Hidden is the string Ljava/lang/invoke/LambdaForm$Hidden;. To tie this back to the Java code, I again used javap to decompile the class file we dumped above. This time, however, I added an extra flag to javap - the -v flag, which makes javap verbose. In particular, this makes it dump the strings in the class constant pool - our string is at index #13, and we can see the runtime annotation #13 is present on the apply method:

```
#13 = Utf8               Ljava/lang/invoke/LambdaForm$Hidden;

...
public java.lang.Object apply(java.lang.Object);
...
RuntimeVisibleAnnotations:
  0: #13()

```

所以，你看不到框架，因为它是隐藏的。事实上，在 spinInnerClass 中，它调用:

```
mv.visitAnnotation("Ljava/lang/invoke/LambdaForm$Hidden;", true);
```

添加注释，但我在第一次通读时错过了这一点。回过头来理解这个流程:当生成用于调用方法引用的运行时包装器时，生成代码用一个java . lang . invoke . lambda form $ Hidden注释来注释 apply 方法，JVM 代码(填充堆栈跟踪以实现 Java 级别的 fillInStackTrace 函数)检查这个注释，并跳过带有这个注释的方法的帧。

## 还有一件事…

眼尖的会注意到上面的 javaClasses.cpp 中的 JVM 代码也有一个 skip_hidden check，原来是从 ShowHiddenFrames 中设置的，在:

```
diagnostic(bool, ShowHiddenFrames, false,                                 \
        "show method handle implementation frames (usually hidden)")      \

```

阅读这个文件中的一些文档使我想到了Java-XX:+UnlockDiagnosticVMOptions-XX:+print flags final，它展示了大量的 JVM 诊断和内部内容，包括:

```
bool ShowHiddenFrames                          = false                               {diagnostic}
```

因此，最后要:

```
:; java -XX:+UnlockDiagnosticVMOptions -XX:+ShowHiddenFrames -cp build/libs/missing-frame.jar com.atlassian.rgates.missingframe.Main
Exception in thread "main" java.lang.NullPointerException
    at com.atlassian.rgates.missingframe.Main$$Lambda$1/295530567.apply(:1000004)
    at java.util.stream.ReferencePipeline$3$1.accept(ReferencePipeline.java:193)
    at java.util.Collections$2.tryAdvance(Collections.java:4717)
    at java.util.Collections$2.forEachRemaining(Collections.java:4725)
    at java.util.stream.AbstractPipeline.copyInto(AbstractPipeline.java:481)
    at java.util.stream.AbstractPipeline.wrapAndCopyInto(AbstractPipeline.java:471)
    at java.util.stream.ReduceOps$ReduceOp.evaluateSequential(ReduceOps.java:708)
    at java.util.stream.AbstractPipeline.evaluate(AbstractPipeline.java:234)
    at java.util.stream.ReferencePipeline.collect(ReferencePipeline.java:499)
    at com.atlassian.rgates.missingframe.Main.main(Main.java:17)
```

框架在那里！错误在 <未知>:1000004——JVM 不知道源名称(因此 <未知> )，如果没有行号就使用 1000000 + bci 作为行号，其中 bci 是我们上面确定原因的 invokevirtual 的字节码索引。

## 我的同事呢？

我把我的发现告诉了我的同事，他们回应说 *好奇:满意* 。超越仅仅“使用这种变通方法”是很好的，取而代之的是完全理解为什么代码会以我们观察到的方式运行。当我开始学习一些关于 Java 实现细节和 JVM 内部的东西时，感觉特别好。
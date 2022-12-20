# 将 Java 类作为子进程运行

> 原文：<https://dev.to/lankydandev/running-a-java-class-as-a-subprocess-34lm>

将 Java 类(不是 jar)作为子进程运行是我本周需要做的事情。更准确地说，我想从测试中产生一个新的进程，而不是直接在测试中运行它(进程内)。我不认为这是什么奇特或复杂的事情。但是，这不是我以前需要做的事情，也不知道要写的确切代码。

幸运的是，一个快速的谷歌和一些堆栈溢出后。我找到了我需要的[答案](https://stackoverflow.com/questions/636367/executing-a-java-application-in-a-separate-process)。虽然答案就在那里，但为了你我的利益，我在这里重写它。

```
class JavaProcess {

  private JavaProcess() {
  }

  public static int exec(Class clazz, List<String> jvmArgs, List<String> args) throws IOException,
        InterruptedException {
    String javaHome = System.getProperty("java.home");
    String javaBin = javaHome + File.separator + "bin" + File.separator + "java";
    String classpath = System.getProperty("java.class.path");
    String className = clazz.getName();

    List<String> command = new ArrayList<>();
    command.add(javaBin);
    command.addAll(jvmArgs);
    command.add("-cp");
    command.add(classpath);
    command.add(className);
    command.addAll(args);

    ProcessBuilder builder = new ProcessBuilder(command);
    Process process = builder.inheritIO().start();
    process.waitFor();
    return process.exitValue();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个静态函数接受您想要执行的`Class`以及任何 JVM 参数和该类的`main`方法期望的参数。能够访问这两组参数允许完全控制子流程的执行。例如，您可能希望使用较低的堆空间来执行您的类，以查看它如何应对内存压力(这正是我需要它的原因)。

注意，要实现这一点，您想要执行的类需要有一个`main`方法。👈这很重要。

访问 Java 可执行文件的路径(存储在`javaBin`中)允许您使用与主应用程序相同版本的 Java 来执行子流程。如果用`"java"`代替了`javaBin`，那么你就冒着用你机器的默认 Java 版本执行子流程的风险。很多时候这可能是好的。但是，可能会出现不希望这样的情况。

一旦所有命令都被添加到`command`列表中，它们就被传递给`ProcessBuilder`。`ProcessBuilder`获取这个列表，并使用其中包含的每个值来生成命令。`command`列表中的每个值由`ProcessBuilder`用空格分隔。它的构造函数还有其他重载，其中一个重载接受单个字符串，您可以自己手动定义整个命令。这使您无需手动管理向命令字符串添加参数。

子进程从它的 IO 传递到执行它的进程开始。这是查看它产生的任何`stdout`和`stderr`所必需的。`inheritIO`是一种方便的方法，也可以通过调用链接下面的代码来实现(也配置子流程的`stdin`):

```
builder
    .redirectInput(ProcessBuilder.Redirect.INHERIT)
    .redirectOutput(ProcessBuilder.Redirect.INHERIT)
    .redirectError(ProcessBuilder.Redirect.INHERIT); 
```

Enter fullscreen mode Exit fullscreen mode

最后`waitFor`告诉执行线程等待衍生的子进程完成。该过程是成功结束还是出错并不重要。只要子流程以某种方式完成。主执行可以继续进行。进程如何完成由它的`exitValue`详述。例如，`0`通常表示成功执行，`1`详细说明无效的语法错误。还有许多其他退出代码，它们可能因应用程序而异。

调用`exec`方法看起来会像下面这样:

```
JavaProcess.exec(MyProcess.class, List.of("-Xmx200m"), List.of("argument")) 
```

Enter fullscreen mode Exit fullscreen mode

它执行下面的命令(或类似的命令):

```
/Library/Java/JavaVirtualMachines/jdk-12.0.1.jdk/Contents/Home/bin/java -cp /playing-around-for-blogs MyProcess "argument" 
```

Enter fullscreen mode Exit fullscreen mode

为了保持整洁，我删除了包括类路径在内的许多路径。你的可能会比这个长很多。这真的取决于你的应用。上面命令中的路径是让它运行所需的最小路径(显然是为我的机器定制的)。

`exec`方法相当灵活，有助于描述正在发生的事情。尽管如此，如果你想让它更具延展性，适用于更广泛的情况，我建议从方法中返回`ProcessBuilder`本身。允许您在几个地方重用这段代码，同时提供配置 IO 重定向的灵活性，以及决定是在后台运行子进程还是阻塞并等待它完成的能力。这将看起来像:

```
public static ProcessBuilder exec(Class clazz, List<String> jvmArgs, List<String> args) {
  String javaHome = System.getProperty("java.home");
  String javaBin = javaHome + File.separator + "bin" + File.separator + "java";
  String classpath = System.getProperty("java.class.path");
  String className = clazz.getName();

  List<String> command = new ArrayList<>();
  command.add(javaBin);
  command.addAll(jvmArgs);
  command.add("-cp");
  command.add(classpath);
  command.add(className);
  command.addAll(args);

  return new ProcessBuilder(command);
} 
```

Enter fullscreen mode Exit fullscreen mode

通过利用这些函数中的一个(或两个)，您现在将能够运行应用程序的类路径中存在的任何类。在我的情况下，这对于在集成测试中生成子流程非常有帮助，不需要预先构建任何 jar。这允许控制 JVM 参数，比如子进程的内存，如果直接在现有进程中运行，子进程的内存是不可配置的。

如果你喜欢这篇文章或者觉得它有帮助(或者两者兼而有之),那么请随时在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) ,并且记得与其他任何可能发现这篇文章有用的人分享！
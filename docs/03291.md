# 构建一个 Linux CLI 应用程序。网络核心

> 原文：<https://dev.to/animesh/build-a-linux-cli-app-with-net-core-24jp>

作为一名开发人员和极客，从我记事起，命令行工具就一直让我着迷。与 web 应用程序或桌面应用程序相比，它们相对容易创建和使用。

在这篇文章中，我们将探索如何在 Linux 上使用。网芯。

使用`dotnet` cli 创建一个新的控制台应用程序。

```
$ dotnet new console -o netcore-test-cli
$ code netcore-test-cli 
```

Enter fullscreen mode Exit fullscreen mode

大多数命令行工具接受用户输入，并根据这些输入执行操作。

这些输入在运行时通过`string[] args`参数进入`Main`函数。我们可以简单地解析来自字符串数组`args`的参数和它们的值，但是这对我们来说是一件乏味的事情。

幸运的是，有许多开源库可以帮助我们做到这一点。一个这样的库是`CommandLineParser`。我在几个项目中使用过它，并且很喜欢它。

让我们将这个命令行解析库添加到项目中。

```
$ dotnet add package CommandLineParser 
```

Enter fullscreen mode Exit fullscreen mode

要开始使用 CommandLineParser，首先我们需要以`Options`类的形式指定参数的类型和名称。然后我们需要使用一个解析器实例来解析所提供的参数。

```
var result = Parser.Default.ParseArguments<Options>(args) 
```

Enter fullscreen mode Exit fullscreen mode

解析成功后，我们得到一个`Parsed<Options>`类型。否则，我们会得到一个`NotParsed<T>`类型，它是一个`IEnumerable<Error>`类型的错误集合。

规定的方法是使用`WithParsed`和`WithNotParsed`扩展方法，这两个方法接受各自解析的类型，并将解析的选项传递给进一步的处理。

```
static void Main(string[] args)
{
    Parser.Default.ParseArguments<Options>(args)
        .WithParsed<Options>(options => ProcessOptions(options))
        .WithNotParsed<Options>(options => HandleErrors(options));
} 
```

Enter fullscreen mode Exit fullscreen mode

为了简单起见，让我们定义两个选项，`Count`和`Name`，如下所示。

```
class Options
{
    [Option('c', "count", HelpText="The count variable")]
    public int Count { get; set; }

    [Option(HelpText="Name for greeting")]
    public string Name { get; set; }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们需要在我们的两个属性上指定`[Option]`属性。这让`CommandLineParser`知道哪些属性对于解析用户输入是有效的。注意，`Count`选项的`ShortName`有`c`，而`LongName`有`count`。这允许您将计数输入指定为`-c 10`或`--count 10`。

让我们只使用`Count`选项来生成到`Count`为止的所有整数的和，如果指定了`Name`选项，则显示一条退出消息。

```
private static void ProcessOptions(Options options)
{
    int sum = 0;
    for (int i = 1; i <= options.Count; i++)
    {
        sum = sum + i;
    }
    Console.WriteLine($"Sum: {sum}");

    if(!string.IsNullOrEmpty(options.Name))
        Console.WriteLine($"Bye, {options.Name}");
} 
```

Enter fullscreen mode Exit fullscreen mode

要运行程序，使用`dotnet run`命令。

```
$ dotnet run -- --count 10 --name=animesh
Sum: 55
Bye, animesh 
```

Enter fullscreen mode Exit fullscreen mode

如果想直接运行编译后的程序，可以运行`dotnet`命令，传递输出 dll 的名称。

```
$ dotnet bin/Debug/netcoreapp2.2/netcore-test-cli.dll -c 123
Sum: 7626 
```

Enter fullscreen mode Exit fullscreen mode

为了使它成为一个合适的 Linux 可执行文件，我们需要用合适的运行时标识符发布程序。

```
$ dotnet publish -c 'release' -r 'linux-x64'

Microsoft (R) Build Engine version 15.9.20+g88f5fadfbe for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 55.08 ms for /home/animesh/projects/lucubrations/netcore-test-cli/netcore-test-cli.csproj.
  netcore-test-cli -> /home/animesh/projects/lucubrations/netcore-test-cli/bin/release/netcoreapp2.2/linux-x64/netcore-test-cli.dll
  netcore-test-cli -> /home/animesh/projects/lucubrations/netcore-test-cli/bin/release/netcoreapp2.2/linux-x64/publish/ 
```

Enter fullscreen mode Exit fullscreen mode

这将程序发布到文件夹`bin/Release/netcoreapp2.2/linux-x64`。看看这个。

```
$ ll bin/Release/netcoreapp2.2/linux-x64

total 1552
-rwxrw-rw- 1 animesh animesh 692128 Jan 18 03:54 libhostfxr.so
-rwxrw-rw- 1 animesh animesh 712624 Jan 18 03:54 libhostpolicy.so
-rwxrw-rw- 1 animesh animesh 106912 Mar  9 15:29 netcore-test-cli
-rw-r--r-- 1 animesh animesh  37340 Mar  9 15:29 netcore-test-cli.deps.json
-rw-r--r-- 1 animesh animesh   5632 Mar  9 15:29 netcore-test-cli.dll
-rw-r--r-- 1 animesh animesh    928 Mar  9 15:29 netcore-test-cli.pdb
-rw-r--r-- 1 animesh animesh    206 Mar  9 15:29 netcore-test-cli.runtimeconfig.dev.json
-rw-r--r-- 1 animesh animesh     26 Mar  9 15:29 netcore-test-cli.runtimeconfig.json
drwxr-xr-x 2 animesh animesh  12288 Mar  9 15:29 publish 
```

Enter fullscreen mode Exit fullscreen mode

注意上面目录列表中第三个项目`netcore-test-cli`上的文件属性。它有文件属性`-rwxrw-rw-`，这意味着它是一个 Linux 可执行文件。这成为可能，因为我们指定了`linux-x64`运行时标识符。

我们可以像以前一样运行可执行文件。

```
$ cd bin/Release/netcoreapp2.2/linux-x64

$ ./netcore-test-cli 

Sum: 0

$ ./netcore-test-cli -c 100

Sum: 5050 
```

Enter fullscreen mode Exit fullscreen mode

两个参数都指定:

```
$ ./netcore-test-cli -c 1000 --name animesh

Sum: 500500
Bye, animesh 
```

Enter fullscreen mode Exit fullscreen mode

指定`--help`选项显示用户输入的所有可能选项:

```
$ ./netcore-test-cli --help

netcore-test-cli 1.0.0
Copyright (C) 2019 netcore-test-cli

  -c, --count    The count variable

  --name         Name for greeting

  --help         Display this help screen.

  --version      Display version information. 
```

Enter fullscreen mode Exit fullscreen mode

本帖到此结束。我们已经看到了如何用？网芯。

这篇文章的所有代码都可以在我的 [gitlab 库](https://gitlab.com/lucubrations/netcore-test-cli)上找到。

* * *

*原载于我的博客@[https://animesh.blog/build-a-linux-cli-app-with-net-core/](https://animesh.blog/build-a-linux-cli-app-with-net-core/)T3】*
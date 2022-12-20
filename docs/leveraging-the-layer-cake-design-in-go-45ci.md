# 利用 Go 中的分层蛋糕设计

> 原文：<https://dev.to/virepri/leveraging-the-layer-cake-design-in-go-45ci>

我*喜欢* go 的多线程支持已经不是什么秘密了。这是一个新线程的一行程序:

```
go  func(){}() 
```

和一个三行代码(最少)来与线程对话:

```
messenger  :=  make(chan  int,  3)  go  func(){  for  message  :=  <-messenger  {  fmt.Println(<-messenger)  }  }()  messenger  <-  5 
```

线程和通道是天造地设的一对，它们让你可以构建一个面向反应的程序，分治递归，以及很多很多我最终会在 dev.to 上谈到的东西。

但是让我们看看第一个。这让我建立了自己的小设计哲学，我发现自己把它应用到了每个项目中。这可能不是描述它最准确的方式，但是这个名字表达了这个想法。所有东西都是一层，而且都粘在一起。

在我看来，这种方法鼓励我构建我的应用程序、服务器等。鼓励可扩展、模块化和可理解的风格。因此，新的服务、接口等。可以被附加上而不需要集成它们的大量麻烦。话虽如此，我并没有完全按照它的*来调整*，但是，嘿，这就是我写这篇文章的原因。我希望在未来完全保持这种结构，而不是像过去几次那样混乱地实现它。

此外，由于 Go 不允许递归导入，这鼓励了我所称的“底层”的创建。这是应用程序内部通信的核心。你的底层定义了所有可以(内部)命令的东西，你的命令格式，你的参数，等等。

最初，你需要整理几个清单:

*   您的应用程序可以对哪些接口做出反应？
*   您的应用程序提供什么服务？

通常，我的接口表现为

*   硬币指示器 （coin-levelindicator 的缩写）命令行界面（Command Line Interface for batch scripting）
*   RPC(不在我的剪贴板管理器中使用)
*   基于 TCP 的协议

服务会有所不同，但在我的剪贴板管理器中是这样的

*   监控剪贴板
*   (很快)客户端加密管道

所以我们来列举一下。Go 中有几个主要的枚举结构(我默认为第一个，在使用 AzCopy 之后，我明白了为什么它可能是错误的):

*   类型常量枚举(如果包中只有一个枚举，我通常会使用它)

```
type  Location  uint8  const  (  CLI  Location  =  iota  TCP  MONITOR  )  //Why might you want to use this?  /*
- Output as the const name in debug logs
- Easy to reference
*/  //Why might you not want to use this?  /*
- Multiple enums in one package is confusing
- May accidentally collide with function names, etc.
*/ 
```

-type-func 枚举(AzCopy 使用的)

```
type  Location  uint8  const  ELocation  =  Location(0)  func  (l  Location)  CommandLine()  Location  {  return  Location(1)  }  func  (l  Location)  TcpClient()  Location  {  return  Location(2)  }  func  (l  Location)  Monitor()  Location  {  return  Location(3)  }  func  (l  location)  String()  string  {  return  ...  }  //Why might you want to use this?  /*
- Isolated by variable type, not by package
- Can't collide with other functions, enums, etc.
*/  //Why might you not want to use this?  /*
- Requires a Location variable to actually get the enumeration values
- No easy way to print a string of it for debug purposes
- Enumerated by functions, not by any concrete value
*/ 
```

-双映射枚举

```
var  ELocation  =  map[string]uint8{...}  var  LocationToString  =  map[uint8]string{...}  //Why might you want to use this?  /*
- Isolated by variable type, not by package
- Can't collide with other functions, enums, etc.
*/  //Why might you not want to use this?  /*
- Not immutable
- Requires more memory
- Not forced to a single type
- No easy way to print a string of it for debug purposes
*/ 
```

我不太喜欢围棋缺乏结构，但是，你可以利用你所拥有的。所有这些都有各自的问题。所有这些都可以通过混合&匹配来创建“完美的”`enum`实现。

在这一点上，是时候建立你的基板了。你的指挥结构几乎总是最重要的事情。你可以随时添加，但随着时间的推移，越来越难去除。为了简单起见，我通常包括一个命令 ID、一个作为参数的字符串列表和原点位置。

```
type  Command  struct  {  //Could have multiple command structures for each layer  CommandID  uint8  //Could be a enum  Parameters  []string  Origin  Location  //This can be a string or an enumeration. Your choice.  } 
```

所以你已经有了你的指挥结构，现在是时候设置你的通道了。为每个层设置一个通道。

```
var  (  CLIChannel  =  make(chan  Command,  50)  //Reasonable buffer size.  ClientChannel  MonitorChannel  ) 
```

现在，你的衬底准备好了。我通常把它放在“公共”包下(或者一个字面上叫做“衬底”的包)。是时候创建你的层了。为每个图层制作一个包，并为它们创建一个基线。也许他们将来会生自己的孩子。让我们为我取一个样本 TCP 客户端层。

```
package  client  type  extMessage  struct  {  CommandID  uint8  Parameters  []string  }  func  TcpClient()  {  ExternalMessages  :=  make(chan  extMessage,  50)  go  func(){  //TCP listener in here, pipes to ExternalMessages  }()  for  {  select  {  case  extMsg  :=  <-  ExternalMessages:  //Handle external message  case  intMsg  :=  <-  common.TcpChannel:  //Handle internal message  }  }  } 
```

当你把你的骨架放好之后，把那些吸盘放在`main.go`里，因为你已经准备好了。

```
package  main  import  (  "sync"  )  func  main()  {  var  wg  sync.WaitGroup  wg.Add(3)  go  client.TcpClient()  go  commandline.CLI()  go  monitor.Monitor()  wg.Wait()  } 
```

当然，你可以用不同的方式终止程序。也许对你的每一层都有一个自杀命令，让他们用`wg.Done()`回调等待组来安全地终止程序。如果当前活动的数据无关紧要，那么一个`os.Exit(0)`就足够了。

所有这些工作的结果是，你现在有一个很好的分层蛋糕，你可以理解:

*   每个命令都来自哪里
*   谁与什么互动，如何互动

最重要的是，你获得了模块化、可维护性和多线程的好处。
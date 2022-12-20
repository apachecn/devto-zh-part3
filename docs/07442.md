# RaspberryPi 3 上 dotnet core 3.1 上的 gRPC

> 原文：<https://dev.to/erikest/grpc-on-dotnet-core-preview3-on-raspberrypi-3-4nf4>

RaspberryPi 3 上 dotnet core 3.1 上的 gRPC(最初是为 preview3 编写的，nuget 包已更新为最新版本)

### **TL；博士**

它没有得到官方支持，但通过为 ARM 编译本机 **libgrpc_csharp_ext** 库，这是可能的。我用一组脚本帮助你自己编译它，让它变得更容易。我也创建了一个 nuget 包，所以你不需要*用*来编译它，也不需要`dotnet new grpcpi`来连接所有的点，增强你的 grpc pi 生命。另外，如果你的目标是把你的代码放到 github 上分享(请这么做！)，试试 *gitstub* ，一个鼓励 github 优先开发的 dotnet cli 全球工具。

*   自行编译 libgrpc_csharp_ext
    *   [https://github.com/erikest/libgrpc_csharp_ext](https://github.com/erikest/libgrpc_csharp_ext)
*   获得一个已经编译好的`libgrpc_csharp_ext.x86.so`(为 arm 编译的),您现在就可以将它添加到您的项目中
    *   [https://www.nuget.org/packages/libgrpc_csharp_ext.arm7](https://www.nuget.org/packages/libgrpc_csharp_ext.arm7)
*   开始一个新的 grpc 项目，准备发布到 linux-arm 上，用于 pi。
    *   [https://github.com/erikest/grpcpi](https://github.com/erikest/grpcpi)
    *   [https://www.nuget.org/packages/grpcpi/](https://www.nuget.org/packages/grpcpi/)
    *   `dotnet new -i grpcpi`
    *   注:截至。net core 3.1，发布命令选择了错误版本的 libgrpc_csharp_ext.x86.so 发布 linux-arm 时出现了一个[问题](https://github.com/dotnet/sdk/issues/4195)。
*   要获得`gitstub`，这样你就可以毫不费力地把你所有的项目都升级到 GitHub，看看我的[关于它的其他帖子](https://dev.to/erikest/gitstub-a-github-first-development-boostrapper-for-dotnet-core-1i5f)或者只是自己安装和发现
    *   `dotnet tool install --global gitstub`

## [T1】简介](#intro)

这是一个伟大的日子，当你坐下来用一个新的神奇的小玩意做一些事情，你的眼睛里可以看到兴奋，却发现酷还不支持“你的架构/平台/环境”。当我决定用 RaspberryPis 开始一个新的客户机/服务器项目时，情况就是这样。我渴望使用 dotnet core，但又想要比 json 更简洁的东西，于是我发现了高效的客户机/服务器协议 gRPC，它现在可用于我最喜欢的跨平台框架- **dotnet core** 。该工具是新的，闪闪发光，一个规范的“hello world”模板已准备好在 Visual Studio 2019 和 dotnet core 3 preview 6 中从命令行使用(`dotnet new grpc`)。不幸的是，官方支持的处理器架构是 x64，x86 - arm 暂时被排除在外。然而，我们将看到，通过一点努力和黑客技术，我们可以让新的 gRPC 位与 RaspberryPi 一起工作，然后让演示程序运行，并在 Pi 回应时发出满意的笑声。

## 概述

我们将从几个方面来看如何让它工作起来:

1.  首先我们将讨论设置——设备、开发环境、工具等。
2.  然后我们将在 **RaspberryPi** 上为 *arm7* 处理器编译原生 *libgrpc_csharp_ext* 的过程。我们将查看自动化构建/测试脚本和存储库中的一个实验脚本，该脚本用于为 android 编译库，作为我们的灵感。不要担心，事实证明这并不太复杂，如果您想继续学习，我已经为您准备了脚本，让您可以直接学习。
3.  有了本地库，我们将研究固定的 gRPC 模板，以及我们需要做些什么来使它在 pi 上工作
4.  在浏览完之后，我们将引入一个 nuget 包，它将为我们节省一些繁琐的工作
5.  最后，我们将查看新的 grpcpi 模板，安装它并生成一个新的项目，将它连接在一起。我们将在这个例子中通过 gRPC 从服务器向 pi 发送一条消息，然后 pi 将使用这条消息在 led 上产生一系列的闪烁。最后，我们将拥有在这个平台上开发更有意义的应用程序所需的所有东西。
6.  作为奖励，我们将安装 *gitstub* ，这是一个 dotnet cli 全球工具，它将新的或现有的项目连接到 github 资源库，以启动您的开发周期，并在创纪录的时间内打开您的源代码*。*

 *## 我们开始吧！

### 工具、设备、环境，啊呀！

我们需要一些工具。首先说一下我们的主开发机。就我而言，是一台基于 x64 的旧戴尔笔记本电脑。在这个机器上，无论是物理的还是虚拟的，我们都需要 dotnet preview6 sdk 和运行时。这些都可以在这里找到。既然我们要检查 visual studio 中的新模板，我们将需要 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

接下来，对于 RaspberryPi，我们将使用 raspbian 作为 linux 发行版，尽管其他版本也可能工作得很好。我们还需要一些其他的东西来编译这个库。您可以在下面逐个完成这些，或者您可以使用下面的命令下载一个脚本，*应该让*在一个步骤中自动完成并编译库。

像往常一样，不要相信我和我的脚本，直到你读了它，并验证它不是一个邪恶的邪恶的人伪装成一个无辜的尝试，让你编译这个晦涩的库。

上圆周率

```
wget https://github.com/erikest/libgrpc_csharp_ext/setupAndCompile.sh
chmod +x ./setupAndCompile.sh
./setupAndCompile.sh 
```

Enter fullscreen mode Exit fullscreen mode

为了编译这个库，我们将需要构建工具、源代码，正如我们稍后将讨论的，我们将需要确定*如何*构建我们感兴趣的库，因为 grpc 源代码包含许多我们不需要为此任务重新编译的其他位。

1.  获得 git——如果您正在跟踪 raspbian，那么您已经获得了 git，很好。
2.  安装构建工具([脚本](https://github.com/erikest/libgrpc_csharp_ext/installToolchain.sh))。
    *   在 csharp 子模块的 [README.md](https://github.com/grpc/grpc/tree/master/src/csharp) 中，我们被告知要查看 [BUILD.md](https://github.com/grpc/grpc/blob/master/BUILDING.md) 。
    *   BUILD.md 告诉我们，对于 linux 编译，我们需要的工具有:

```
$ [sudo] apt-get install build-essential autoconf libtool pkg-config
$ [sudo] apt-get install libgflags-dev libgtest-dev
$ [sudo] apt-get install clang libc++-dev 
```

Enter fullscreen mode Exit fullscreen mode

*   他们没有告诉你的是，可能因为不支持，我们还需要 **cmake** ，这*是作为构建依赖列在 Windows 下的*，我们将在查看构建过程时了解原因。

```
$ [sudo] apt-get install cmake 
```

Enter fullscreen mode Exit fullscreen mode

1.  获取源代码([脚本](https://github.com/erikest/libgrpc_csharp_ext/gitgRPCsource.sh))

```
 $ mkdir ~/libgrpc_csharp_ext/
 $ cd ~/libgrpc_cshar_ext/
 $ git clone -b $(curl -L https://grpc.io/release) https://github.com/grpc/grpc
 $ cd grpc
 $ git submodule update --init 
```

Enter fullscreen mode Exit fullscreen mode

*   这也是来自 BUILD.md
*   如果您想将您的应用程序作为依赖于框架的部署或依赖于框架的可执行文件部署到 pi，您将需要 dotnet 运行时。否则，针对特定运行时的发布将创建一个自包含的部署)，这意味着所有必需的库都与可执行文件一起发布。你可以在这里阅读更多关于这三种风格的内容。对于本演练，我们将做一个依赖于框架的可执行文件，所以我们需要运行时。
    *   在撰写本文时，最新版本 3.1 可以这样获得( [script](https://github.com/erikest/libgrpc_csharp_ext/installDotNet3.1.sh) ):

```
#Dependencies
sudo apt-get install curl libunwind8 gettext

#SDK needed to compile
wget https://download.visualstudio.microsoft.com/download/pr/d52fa156-1555-41d5-a5eb-234305fbd470/173cddb039d613c8f007c9f74371f8bb/dotnet-sdk-3.1.101-linux-arm.tar.gz

#Runtime needed to run apps
wget https://download.visualstudio.microsoft.com/download/pr/da60c9fc-c329-42d6-afaf-b8ef2bbadcf3/14655b5928319349e78da3327874592a/aspnetcore-runtime-3.1.1-linux-arm.tar.gz

#Make a home and extract the tars
mkdir -p $HOME/dotnet
tar zxf dotnet-sdk-3.1.101-linux-arm.tar.gz -C $HOME/dotnet
tar zxf aspnetcore-runtime-3.1.1-linux-arm.tar.gz -C $HOME/dotnet

#Set environment variables, including the path so the dotnet cli can be found
export DOTNET_ROOT=$HOME/dotnet
export PATH=$PATH:$HOME/dotnet

#push the path to the profile so it 'sticks' after restarts
sudo echo "export DOTNET_ROOT=$HOME/dotnet" >> /etc/profile
sudo echo "export PATH=$PATH:/$HOME/dotnet" >> /etc/profile 
```

Enter fullscreen mode Exit fullscreen mode

好吧！我们有工具，有资源，现在我们只需要让它们发挥作用

### 编译 libgrpc_csharp_ext

如果我们再次检查 csharp 子模块[的 README](https://github.com/grpc/grpc/tree/master/src/csharp) ，我们会看到得到编译的推荐方式是:

```
# from the gRPC repository root
$ python tools/run_tests/run_tests.py -l csharp -c dbg --build_only 
```

Enter fullscreen mode Exit fullscreen mode

如果您现在尝试运行它，您会得到相当多的错误。问题是这个 run_tests.py 试图编译 csharp 子模块中的所有内容——我们只需要本地库。我们需要做的是检查 run_tests.py，看看它在做什么，以及我们是否可以剥离其他构建步骤，只取出本机库。

在 run_tests.py 中，每种语言都有一个使用`-l`标志切换的类，就像上面的命令`-l csharp`一样。当我们为 csharp 做查找时，我们看到一些[好东西](https://github.com/grpc/grpc/blob/31843787ccdb8446e3be3b62d6688dc1de22c7b4/tools/run_tests/run_tests.py#L923)。具体来说，让我们注意:

*   一种方法叫做 [`pre_build_steps`](https://github.com/grpc/grpc/blob/31843787ccdb8446e3be3b62d6688dc1de22c7b4/tools/run_tests/run_tests.py#L1000) 。嗯，听起来很有趣，它指向另一个[剧本](https://github.com/grpc/grpc/blob/master/tools/run_tests/helper_scripts/pre_build_csharp.sh)
*   一种方法叫做 [`make_targets`](https://github.com/grpc/grpc/blob/31843787ccdb8446e3be3b62d6688dc1de22c7b4/tools/run_tests/run_tests.py#L1009) 。由于`make`通常编排 gcc/g++版本，而我们试图编译一个本地 c++库，这也很有趣。*和*它引用了一个名为‘grpc _ cs harp _ ext’的目标——这听起来正是我们所需要的！

作为一点确认，让我们看看 csharp/experimental 文件夹，其中有用于编译 android、ios 和 unity 的原生库的脚本。在 android [脚本](https://github.com/grpc/grpc/blob/31843787ccdb8446e3be3b62d6688dc1de22c7b4/src/csharp/experimental/build_native_ext_for_android.sh#L52)中，我们看到，在调用 cmake 来设置构建环境之后:`make -j4 grpc_csharp_ext`啊哈！`-j4`开关告诉编译器要产生多少线程，然后我们就有了我们的`grpc_csharp_ext`目标。好的，我们现在感觉很好，因为我们在正确的轨道上。但是我们实际上还没有 makefile，所以我们需要对它进行排序。

在我们创建 makefile 之前，我们必须先创建一个 makefile，所以让我们回到那个 [pre_build_script](https://github.com/grpc/grpc/blob/master/tools/run_tests/helper_scripts/pre_build_csharp.sh) 来看一看。

```
set -ex

# cd to repository root
cd "$(dirname "$0")/../../.."

mkdir -p cmake/build
cd cmake/build

cmake -DgRPC_BUILD_TESTS=OFF -DCMAKE_BUILD_TYPE="${MSBUILD_CONFIG}" ../..

cd ../../src/csharp

dotnet restore Grpc.sln 
```

Enter fullscreen mode Exit fullscreen mode

看起来很有希望，我们已经进行了一点目录调整，然后调用 cmake(现在应该*让*明白为什么我们也需要安装这个工具)，最后恢复 grpc 解决方案，为构建的 dotnet 部分做好准备。我们不需要 dotnet 部分，我们只需要 cmake 调用来建立构建环境，然后构建我们的本地目标。

通过将我们从 make 目标(`grpc_csharp_ext`)中学到的内容与这里的预构建步骤结合起来，我们可以得到执行编译的命令。这些都是相对于我们工作的根来执行的，如果你跟着做的话，应该是在`~/libgrpc_csharp_ext/`。

这是我们的脚本，也位于[这里](https://github.com/erikest/libgrpc_csharp_ext/compile_csharp_ext.sh)

```
set -ex

#intended to be run from the repository root
cd grpc/

#create location for build files
mkdir -p cmake/build
cd cmake/build

#setup the build files
cmake -DgRPC_BUILD_TESTS=OFF -DCMAKE_BUILD_TYPE="${MSBUILD_CONFIG}" ../..

#compile libgrpc_csharp_ext.so
make -j4 grpc_csharp_ext

cp libgrpc_csharp_ext.so ../../../libgrpc_csharp_ext.x86.so 
```

Enter fullscreen mode Exit fullscreen mode

那里的一切看起来都很坚固，但是，为什么我们要将`libgrpc_csharp_ext.so`复制到`libgrpc_csharp_ext.x86.so`？嗯，这是一个*完美的*进入下一个话题！

### gRPC template

在我们的 dev box 上，我们可以使用附带的模板
创建一个新的 grpc 项目

```
mkdir grpcDemo
cd grpcDemo
dotnet new grpc 
```

Enter fullscreen mode Exit fullscreen mode

如果我们发布 linux-arm 运行时标识符(RID)的客户端

`dotnet publish grpcDemo/grpcDemo.Client/grpcDemo.Client.csproj -r linux-arm --self-contained false`

*如果我们不包括`--self-contained false`，那么我们也会得到一大堆框架库。这对可移植性有好处，因为您可以将整个发布文件夹推送到任何 pi 并开始运行，但是对于迭代开发，一次性设置框架以避免额外的文件拷贝似乎是可行的方法。*

我们将在/bin/debug/netcoreapp3.0/Linux-arm/publish 文件夹中找到 libgrpc_csharp_ext 的两个副本，一个是. x86.so，一个是. x64.so

如果我们将它部署到 pi 上的现有目录，例如通过使用 scp

`scp ./grpcDemo.Client/bin/debug/netcoreapp3.0/linux-arm/publish pi@<PI IP>:~/grpc.Client`

*正如这里的[所提到的](https://superuser.com/questions/1403473/scp-error-unexpected-filename)，scp 命令不再支持方便的`/.`，它会复制文件而不包括它们所在的目录。所以当我们不想要的时候，我们在 pi 上得到一个“发布”文件夹。事情是这样的。*

并从 pi
运行它

```
cd ~/grpc.Client/publish
chmod +x grpcDemo.Client
./grpcDemo.Client 
```

Enter fullscreen mode Exit fullscreen mode

我们会得到一个错误，就像这个[人做的那样](https://stackoverflow.com/questions/54568281/how-to-deploy-a-sample-grpc-client-server-solution-in-raspberri-pi-in-dotnet-c/55578459#55578459)

也就是说它不能加载`libgrpc_csharp_ext.x86.so`库。这就是我们的问题和所有这些工作开始的地方——这个库不是为 arm 处理器编译的，但它仍然包含在 publish 输出中，并被 NativeExtension 类引用。当我们深入存储库并查看源代码时，特别是这里的我们注意到它寻找的文件名取决于操作系统，在这种情况下是 linux，它需要一个静态库。所以，还有建筑。尽管 pi 拥有 64 位处理器，但实际上操作系统是 ARM32，因此架构字符串被设置为 x86。也就是说，在一个运行 raspbian 的 raspberrypi 上，它试图加载的文件名是**libgrpc _ cs harp _ ext . x86 . so**——所以，让我们给它它想要的吧！

圆周率上

```
cp ~/libgrpc_csharp_ext/libgrpc_csharp_ext.x86.so ~/grpc.Client/publish
./grpcDemo.Client 
```

Enter fullscreen mode Exit fullscreen mode

这一次，在我们看到
之前，它应该暂停大约 15-20 秒

```
Unhandled Exception: Grpc.Core.RpcException: Status(StatusCode=Unavailable, Detail="Connect Failed")
   at gRPCpi.Program.Main(String[] args) in C:\Users\Geocoder\Source\Repos\gRPC Template\content\gRPCpi\src\gRPCpi.Clien
t\Program.cs:line 26
 at gRPCpi.Program.<Main>(String[] args)
Aborted 
```

Enter fullscreen mode Exit fullscreen mode

但是，嘿，这意味着它正在运行！它就是不能*连接*到服务器。我们离剃光牦牛毛越来越近了！嗯，默认情况下，它试图连接到本地主机上的服务器。所以我们需要做一些修改，因为我们的服务器将在我们的开发机器上通过网络运行。

*   在客户端的 program.cs 中，我们需要将 grpc 客户端对准服务器(dev box)的 ip 地址。如果你不知道自己的，windows 上的`ipconfig`或 linux 上的`ifconfig`会带你去那里。

```
 //CHANGE THIS TO YOUR SERVER ADDRESS
var serverIpAddress = "YOUR IP";

var channel = new Channel($"{serverIpAddress}:" + port, ChannelCredentials.Insecure); 
```

Enter fullscreen mode Exit fullscreen mode

*   我们还需要服务器监听那个 ip 地址和端口，默认情况下它没有，它只监听本地主机。为了向我们的网络 IP 开放，我们需要对 kestrel 服务器配置进行更改:

```
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
        webBuilder.UseUrls("http://*:50051");
    }); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们说监听所有绑定到网络接口的(*) IP 地址。

现在，让我们将客户机重新发布到 pi。所以在 dev 框上，在项目根

```
dotnet publish grpcDemo/grpcDemo.Client/grpcDemo.Client.csproj -r linux-arm scp ./grpcDemo.Client/bin/debug/netcoreapp3.0/linux-arm/publish pi@<PI IP>:~/grpc.Client 
```

Enter fullscreen mode Exit fullscreen mode

在我们再次运行客户机之前，让我们启动并准备好服务器。在 Visual Studio 中，您可以运行一个新的调试实例，或者从命令行运行

`dotnet run --project grpcDemo/grpcDemo.Server/grpcDemo.Server.csproj`

然后回到 pi，让我们运行客户端，在我们再次覆盖`libgrpc_csharp_ext`库
之后

```
cp ~/libgrpc_csharp_ext/libgrpc_csharp_ext.x86.so ~/grpc.Client/publish
cd ~/grpc.Client/publish
chmod +x grpcDemo.Client
./grpcDemo.Client 
```

Enter fullscreen mode Exit fullscreen mode

如果一切按计划进行，我们应该得到这个

```
Greeting: Hello GreeterClient 
```

Enter fullscreen mode Exit fullscreen mode

咯咯笑！呜！我们做到了——是时候挂帽子了，任务完成了...不完全是，虽然这表明我们可以“让它工作”，但我们真正想要的是让正确的库与所有其他已发布的工件相遇，这样我们就可以不用这么多步骤继续开发。当然，我们可以编写更多的脚本来减少这种痛苦，但是没有更好的方法吗？

### Libgrpc _ cs harp _ ext . arm 7 nu get 包

到目前为止，我们已经用我们的 arm 编译版本手动覆盖了“错误的”原生库。我们真正想要的是在发布过程中引入正确的库——所以无论我们发布哪个运行时，它都有正确的版本。如果您为 windows 或 linux 或 osx 发布，您会注意到 publish 命令正确地复制了每个运行时所需的版本。

这是怎么回事？

在模板中引用的`Grpc.Core` nuget 包中，是 win、linux 和 osx 运行时的本地库的副本。您可以通过检查您的 username . nu get \ packages \ grpc . tools<version>\ runtimes 文件夹中的包缓存来验证这一点。publish 命令使用这种约定来查找附加的运行时特定组件，以复制到生成和发布输出中。同样的机制可以用来为 linux-arm 运行时提供库。让我们继续添加对这个包的引用

`dotnet add grpcDemo\grpcDemo.Client package libgrpc_csharp_ext.arm7`

然后再次发布

`dotnet publish grpcDemo/grpcDemo.Client/grpcDemo.Client.csproj -r linux-arm --self-contained false`

我们可以(通过文件大小)验证我们的库版本已经被复制，并且可以部署了。嘿，现在这个项目有了腿，快速迭代就在眼前！

### 接下来

在下一篇文章中，我们将通过添加一个新的项目模板来完成所有这些工作，从而使开始变得更加容易。然后，我们将使用 gitstub 启动一个新的开源客户端/服务器闪烁 led 项目，这是每个人都肯定想要的！*
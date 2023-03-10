# 阿帕奇节俭 0.12

> 原文：<https://dev.to/jeikabu/thrift-012-1mi9>

自从在以前的项目中使用了 [protobuf](https://github.com/protocolbuffers/protobuf) 之后，我就产生了一种爱(痴迷？)用于序列化库。在我们的[当前项目](https://github.com/subor/sdk)中，我们使用 [Apache Thrift](https://github.com/apache/thrift) ，我们正在将其更新到最近发布的 0.12 版本。

## 阿帕奇节俭

使用 protobuf 的一个教训是，我们在消息传递层浪费了大量精力:标识(在单个通道上复用不同的消息类型)、成帧、错误处理、连接管理等。gRPC 仍处于起步阶段，因此我们着眼于提供更完整解决方案的选项，包括传输抽象。我们选定了[阿帕奇节俭](https://github.com/apache/thrift)。

我已经写了大量关于节俭的文章([【0】](//./migrating-to-aspnet-core-w-apache-thrift-45f6)[【1】](//./rust-w-apache-thrift-h8a)[【2】](//./apache-thrift-over-zeromq-66e)[【3】](//./aspnet-core-generic-host-with-rust-services-4eab)[【4】](//./rpc-forwarding-for-thrift-5egc))，所以我将以我在 OSX 建立节俭的一些笔记作为结束。

### 0.11

通常情况下，在 macOS 上做事情需要更多的努力。

大多听从 https://thrift.apache.org/docs/install/os_x[的指示](https://thrift.apache.org/docs/install/os_x):

*   升压 1.67

```
./bootstrap.sh
sudo ./b2 threading=multi address-model=64 variant=release stage install 
```

Enter fullscreen mode Exit fullscreen mode

*   openssl 1.0.2o 和 1.1.0h 都构建了，但是 libevent 以链接器错误结束
*   libevent 2.1.8(不加密)

```
./configure --disable-openssl --prefix=/usr/local && make -j4 && sudo make install 
```

Enter fullscreen mode Exit fullscreen mode

*   野牛 3.0.4

```
# Could not get compiling on OSX 10.13.4
./configure && make -j4 && sudo make install
# Instead install via brew
brew install bison
export PATH=/usr/local/opt/bison/bin:$PATH 
```

Enter fullscreen mode Exit fullscreen mode

*   节俭 0.11

```
./configure --prefix=/usr/local --with-boost=/usr/local --with-libevent=/usr/local
make -j4 && sudo make install 
```

Enter fullscreen mode Exit fullscreen mode

### 0.12

仅仅一年多没有更新之后， [Thrift 0.12](https://github.com/apache/thrift/releases) 终于在 2018 年末发布。依赖性与 0.11 相同，但是遇到了一些问题。

`make`失败:

```
libtool: compile: g++ -std=c++11 -DHAVE_CONFIG_H -I. -I../.. -I../../lib/cpp/src/thrift -I../../lib/c_glib/src/thrift -I/usr/local/include -I/usr/local/include -I./src -D __STDC_FORMAT_MACROS -D__ STDC_LIMIT_MACROS -Wall -Wextra -pedantic -g -O2 -MT src/thrift/transport/TSocket.lo -MD -MP -MF src/thrift/transport/.deps/TSocket.Tpo -c src/thrift/transport/TSocket.cpp -fno-common -DPIC -o src/thrift/transport/.libs/TSocket.o
src/thrift/transport/TSocket.cpp:173:15: error: out-of-line definition of
      'hasPendingDataToRead' does not match any declaration in
      'apache::thrift::transport::TSocket'
bool TSocket::hasPendingDataToRead() {
              ^ ~~~~~~~~~~~~~~~~~~~
src/thrift/transport/TSocket.cpp:179:3: error: unknown type name
      'THRIFT_IOCTL_SOCKET_NUM_BYTES_TYPE'
  THRIFT_IOCTL_SOCKET_NUM_BYTES_TYPE numBytesAvailable;
  ^
src/thrift/transport/TSocket.cpp:181:40: error: use of undeclared identifier
      'FIONREAD'
  int r = THRIFT_IOCTL_SOCKET(socket_, FIONREAD, &numBytesAvailable); 
```

Enter fullscreen mode Exit fullscreen mode

结果是它获得了已经安装到`/usr/local/include/thrift/`的 0.11 头文件，并且必须删除它们。

接下来，失败原因:

```
Making all in test
composer install --working-dir=../../..
make[4]: composer: No such file or directory
make[4]: *** [deps] Error 1 
```

Enter fullscreen mode Exit fullscreen mode

尽管使用`--without-php`和`--without-php_extension`运行 configure，但“Composer”似乎是它在寻找的 php 东西。我们没有调查这一点，但是可以通过不构建测试来避免:`./configure --disable-tests`。

## 痛点

### 工会

`netcore`生成器是作为 0.11 的一部分发布的，但是`union`选项生成了无法编译的代码。这个问题在 0.12 中被修复了。给出如下定义:

```
struct PlayMsg{
      1: string url,
}
union RequestMsg{
      1: PlayMsg Play,
      // 2: OtherMsg Other,
      //...
} 
```

Enter fullscreen mode Exit fullscreen mode

生成代码的删节版本:

```
public abstract partial class RequestMsg : TAbstractBase {

      public abstract object Data { get; }

      public class Play : RequestMsg {
            private PlayMsg _data;
            public override object Data { get { return _data; } }
            public Play(PlayMsg data) : base(true) {
                  this._data = data;
            } 
```

Enter fullscreen mode Exit fullscreen mode

使用时，非常适合[模式匹配](https://docs.microsoft.com/en-us/dotnet/csharp/pattern-matching) (C# 7):

```
// RequestMsg message = ...
switch (message)
{
case RequestMsg.Play msg:
    // Need a cast here T_T
    PlayMsg play = (PlayMsg)msg.Data; 
```

Enter fullscreen mode Exit fullscreen mode

注意到对`PlayMsg`的强制转换，因为属性是`object Data`。这种废话在强类型宇宙中没有位置！幸运的是，[这是一个 bug](https://issues.apache.org/jira/browse/THRIFT-4715) ，有望在 0.12.1 中得到修复，而不必等待 0.13。

### Nuget

节俭团队重新发布了一个[包来 nuget](https://www.nuget.org/packages/ApacheThrift/) 。它包含。NET 3.5、4.5 和标准 2.0 库。[正如我之前写的](//./nupkg-ergonomics-or-the-ongoing-saga-of-c-bindings-to-nng-native-library-2ald#when-to-not-multi-target)，我们有一个问题。NET Framework 4.6.1 项目使用 4.5 库，而我们希望使用标准库(实际上它们略有不同)。

我们对报告这个“问题”持观望态度。对我们的用例来说感觉更特殊，而不是普遍适用。或许这应该是 [`<PackageReference>`](https://docs.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files) 的一个特点。与此同时，我们正在发布[我们自己的套装](https://www.nuget.org/packages/Subor.Thrift.NETCore/)(但是会把人们引向官方套装)。
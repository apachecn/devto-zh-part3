# 2019 年在 Mac 上构建 Hadoop 原生库

> 原文：<https://dev.to/zejnilovic/building-hadoop-native-libraries-on-mac-in-2019-1iee>

**TL；博士在[端](#tldr)端**

最近，我遇到了“需要”Hadoop 原生库的情况。嗯，当我说“需要”的时候，我的意思是我已经受够了像这样不断的警告

```
WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable 
```

Enter fullscreen mode Exit fullscreen mode

所以我想我会建立自己的 Hadoop 原生库。能有多难，对吧？诚实回答？没有教程的话不到一个小时。15 分钟，其中大部分是编译时间。在我的搜索中，我发现许多教程和指南要么已经过时，要么没有提供完全编译和安装所需的一切，这就是为什么我写了我自己的，我在两台独立的 MAC 上测试，因此它应该是“测试足够了”。

## 为什么这么做

没有我希望解决的现实世界的问题。我只有几分钟空闲时间，我用它们来学习新的东西。但是我确实读到过速度提高的例子，如果你在本地开发或测试某些东西，这是件好事，因为本地机器往往很慢，任何改进都是受欢迎的。另一件事是，不久前我确实看到了两篇随机的文章，说他们确实在 Java 库上有一些问题，但是你们中的一些人有同样问题的可能性真的很小。

## 依赖关系

首先，我们需要安装构建的依赖项，我将包含链接，以便您可以检查您将要安装的确切内容:

*   [gcc](https://gcc.gnu.org/)
*   [自动确认](https://www.gnu.org/software/autoconf/)
*   [自动制造](https://www.gnu.org/software/automake/)
*   [libtool](https://www.gnu.org/software/libtool/)
*   [cmake](https://cmake.org/)
*   [爽快的](https://github.com/google/snappy)
*   [gzip](https://www.gzip.org/)
*   [bzip2](http://www.bzip.org/)
*   [zlib](https://zlib.net/)
*   [wget](https://www.gnu.org/software/wget/)
*   [openssl 1.0](https://www.openssl.org/) 的缩写形式
    *   Brew 上的 1.1 有问题。更多在评论区。感谢 [@imasli](https://dev.to/imasli)
*   [protobuf 2.5.0](https://github.com/protocolbuffers/protobuf)

(请注意，我跳过了 maven、java 和其他一些我认为你已经知道的东西。如果我错了，告诉我，让我们更新文章。以及 Hadoop 安装。章昊[在这里](https://isaacchanghau.github.io/post/install_hadoop_mac/)有一篇关于在 Mac 上安装 Hadoop 的漂亮文章。)

对于其中大多数的安装，我将使用[自制软件](https://brew.sh/)。这是一个很好的工具，有一行程序安装，并且使用它的平均时间非常短。由于链接提供了你需要的一切，我在这里跳过安装。

如果你不是第一次使用自制软件，更新和升级你的工具。如果你已经使用它一段时间了，并且想保留当前版本的一些东西，使用`brew pin`像[这个](https://docs.brew.sh/FAQ#how-do-i-stop-certain-formulae-from-being-updated)。

```
# Update
brew update
brew upgrade

# Then the installation
brew install wget gcc autoconf automake libtool cmake snappy gzip bzip2 zlib openssl 
```

Enter fullscreen mode Exit fullscreen mode

正如您可能已经注意到的那样，上面的列表中缺少了其中一个依赖项。是啊！它是一个`protobuf`已经被弃用，不能轻易从家酿安装。所以我们自己造吧。这样更干净，也比听起来更有趣。我们首先需要从 GitHub 获取它，并在某个地方将其解压缩。你可以删除它，所以你不需要一个特殊的文件夹结构。

```
wget https://github.com/google/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
tar -xzf protobuf-2.5.0.tar.gz
cd protobuf-2.5.0 
```

Enter fullscreen mode Exit fullscreen mode

接下来是建设和确保一切顺利进行的过程。这需要一些时间，我建议你一步一步地去观察和了解正在发生的事情。这里或那里的一些警告是正常的，所以你可以跳过它们。

```
./configure
make
make check
make install
# And just to check if everything is ok.
# This should print libprotoc 2.5.0
protoc --version 
```

Enter fullscreen mode Exit fullscreen mode

## OpenSSL setup

现在，手工链接 OpenSSL 库，因为 Homebrew 拒绝链接 OpenSSL，而编译器需要它们。这是一个已知的特性，需要通过运行`ln`来完成。

```
cd /usr/local/include
ln -s ../opt/openssl/include/openssl . 
```

Enter fullscreen mode Exit fullscreen mode

这将解决一个类似下面标题的错误。

```
[exec] -- Configuring incomplete, errors occurred!
[exec] See also /Users/user/github/hadoop/hadoop-tools/hadoop-pipes/target/native/CMakeCMake Error at /usr/local/Cellar/cmake/3.14.3/share/cmake/Modules/FindPackageHandleStandardArgs.cmake:137 (message):
[exec]   Could NOT find OpenSSL, try to set the path to OpenSSL root folder in the
[exec]   system variable OPENSSL_ROOT_DIR (missing: OPENSSL_INCLUDE_DIR)
[exec] Call Stack (most recent call first):
[exec]   /usr/local/Cellar/cmake/3.14.3/share/cmake/Modules/FindPackageHandleStandardArgs.cmake:378 (_FPHSA_FAILURE_MESSAGE)
[exec]   /usr/local/Cellar/cmake/3.14.3/share/cmake/Modules/FindOpenSSL.cmake:413 (find_package_handle_stFiles/CMakeOutput.log.
[exec] andard_args)
[exec]   CMakeLists.txt:20 (find_package)
[exec]
[exec] 
```

Enter fullscreen mode Exit fullscreen mode

## 构建原生库

最后！图书馆的建设。同样，这将创建一个最终可以删除的文件夹。这可能是你需要修改的第一个地方，也是你将要使用的 Hadoop 版本。

```
git clone https://github.com/apache/hadoop.git
cd hadoop
# Change the version as needed
git checkout branch-<VERSION>
# And just package.
mvn package -Pdist,native -DskipTests -Dtar
# After build, move your newly created libraries.
cp -R hadoop-dist/target/hadoop-<VERSION>/lib $HADOOP_HOME 
```

Enter fullscreen mode Exit fullscreen mode

## 设置环境变量

现在是关键的部分，让您的 shell 看到库。我不知道你用的是什么类型的 shell，不过，把这个放到你的 shell 配置文件中(`.bashrc`、`.zshrc`等等)。):

```
export HADOOP_OPTS="-Djava.library.path=${HADOOP_HOME}/lib/native"
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:${HADOOP_HOME}/lib/native
export JAVA_LIBRARY_PATH=$JAVA_LIBRARY_PATH:${HADOOP_HOME}/lib/native 
```

Enter fullscreen mode Exit fullscreen mode

这将为所有的库指明正确的道路，并使一切都井井有条。我们需要做的最后一件事就是检查是否一切正常(我说的一切是指几乎一切，因为`bzip`出了问题，我仍然没有找到解决的方法，当我找到时，我会更新它)。

```
hadoop checknative -a

#The output should be something like this.
19/05/17 19:00:14 WARN bzip2.Bzip2Factory: Failed to load/initialize native-bzip2 library system-native, will use pure-Java version
19/05/17 19:00:14 INFO zlib.ZlibFactory: Successfully loaded & initialized native-zlib library
Native library checking:
hadoop:  true /usr/local/Cellar/hadoop/2.7.5/lib/native/libhadoop.dylib
zlib:    true /usr/lib/libz.1.dylib
snappy:  true /usr/local/lib/libsnappy.1.dylib
lz4:     true revision:99
bzip2:   false openssl: true /usr/lib/libcrypto.35.dylib
19/05/17 19:00:14 INFO util.ExitUtil: Exiting with status 1 
```

Enter fullscreen mode Exit fullscreen mode

## 后记

希望一切顺利，你不再收到那些警告，如果我能帮助哪怕一个人，我会很高兴。因为如果对读者来说没有附加值，那就只是我对着我的墙说话。另一方面，如果您确实在代码或文章中发现了一些问题，请告诉我，我会尽我所能修复一切。

## TL；速度三角形定位法(dead reckoning)

这只是从上层文本中提取的一步一步的 shell 脚本。
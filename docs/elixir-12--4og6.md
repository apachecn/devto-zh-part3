# Elixir 入门 12 :输入输出和文件系统

> 原文：<https://dev.to/gumi/elixir-12--4og6>

本文在 Elixir 官方网站的许可下，基于“[IO and the file system](https://elixir-lang.org/getting-started/io-and-the-file-system.html) ”的解说，加以修改，对 Elixir 的输入输出方法和文件系统相关的操作、`IO`、`File`、`File`

# IO 模块

[`IO`模块](https://hexdocs.pm/elixir/IO.html)提供用于输入输出的主要功能。 读写地址为标准输入输出(`:stdio`)、标准错误(`:stderr`)、文件及其他输入输出设备等。

[`IO.puts/2`](https://hexdocs.pm/elixir/IO.html#puts/2)输出自变量的项目。 [`IO.gets/2`](https://hexdocs.pm/elixir/IO.html#gets/2)是从输入输出设备读取的。 均以第 1 参数的软元件的标准输入输出(`:stdio`)为默认值。

```
iex> IO.puts "hello world"
hello world
:ok
iex> IO.gets "yes or no? "
yes or no? hello  #<- helloとタイプして[enter]
"hello\n" 
```

Enter fullscreen mode Exit fullscreen mode

如果对第 1 自变量赋予`:stderr`，则标准错误为输入输出处。

```
iex> IO.puts :stderr, "hello world"
hello world
:ok 
```

Enter fullscreen mode Exit fullscreen mode

# 文件模块

[`File`模块](https://hexdocs.pm/elixir/File.html)具有将文件作为输入输出设备进行处理的函数。 下面的代码示例打开和关闭文件，以读写字符串。 文件默认以二进制模式(`:binary`)打开。 用于数据的读写的是`IO`模块的函数。

```
iex> {:ok, file} = File.open("hello", [:write])
{:ok, #PID<0.90.0>}
iex> IO.binwrite(file, "world")
:ok
iex> File.close(file)
:ok
iex> File.read("hello")
{:ok, "world"}
iex> {:ok, file} = File.open("hello", [:read])
{:ok, #PID<0.95.0>}
iex> IO.binread(file, :line)
"world"
iex> File.close(file)
:ok 
```

Enter fullscreen mode Exit fullscreen mode

*   [`File.open/2`](https://hexdocs.pm/elixir/File.html#open/2):打开第 1 参数路径的文件。 第 2 参数为模式列表，`:write`为写入，`:read`为读取。
*   [`File.close/1`](https://hexdocs.pm/elixir/File.html#close/1):关闭参数的文件。
*   [返回`File.read/1`](https://hexdocs.pm/elixir/File.html#read/1):`{:ok, binary}`的元组。 `binary`是参数路径的二进制数据。 无法读取时返回`{:error, reason}`。
*   [`IO.binwrite/2`](https://hexdocs.pm/elixir/IO.html#binwrite/2):第 1 自变量为设备，默认值为标准输入输出的`:stdio`。 将第二参数项作为二进制写入。
*   [`IO.binread/2`](https://hexdocs.pm/elixir/IO.html#binread/2):第 1 自变量为元件，默认值为标准输入输出的`:stdio`。 用第 2 参数决定读取的是 1 行(`:line`)还是全部(`:all`)。

打开文件的模式还会加上`:utf8`。 `File`在模块中，指定用于用 UTF-8 编码分析从文件读取的字节。

`File`模块中还包含操作文件系统的函数。 函数的名称是根据 UNIX 的命令命名的。 例如，以下函数:

*   [`File.rm/1`](https://hexdocs.pm/elixir/File.html#rm/1):删除文件的路径。
*   [`File.mkdir/1`](https://hexdocs.pm/elixir/File.html#mkdir/1):制作路径的目录。
*   [`File.mkdir_p/1`](https://hexdocs.pm/elixir/File.html#mkdir_p/1):建立路径的目录，包括父链。
*   [`File.cp_r/2`](https://hexdocs.pm/elixir/File.html#cp_r/3):将目录的内容复制到子目录。
*   [`File.rm_rf/1`](https://hexdocs.pm/elixir/File.html#rm_rf/1):删除包括子目录在内的目录内容。

`File`你会注意到，在模块的函数中，在同一个名字的末尾有带`!`符号的，也有不带。 例如，`File.read/1`和`File.read!/1`。 差异在于返回值。 前者返回元组。 与此相对，后者要么文件的内容会返回，要么就会发生错误。

```
iex> File.read("hello")
{:ok, "world"}
iex> File.read!("hello")
"world"
iex> File.read("unknown")
{:error, :enoent}
iex> File.read!("unknown")
** (File.Error) could not read file "unknown": no such file or directory
    (elixir) lib/file.ex:310: File.read!/1 
```

Enter fullscreen mode Exit fullscreen mode

返回元组的函数可以在返回值中使用模式匹配。

```
defmodule Example do
  def read_file(file) do
    case File.read(file) do
      {:ok, body} -> IO.puts(body)
      {:error, reason} -> IO.puts("error: #{reason}")
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
iex> Example.read_file("hello")
world
:ok
iex> Example.read_file("unknown")
error: enoent
:ok 
```

Enter fullscreen mode Exit fullscreen mode

如果是文件存在的前提，`File.read!/1`更直接。 万一没有文件时，模式匹配会发生不匹配的错误。 因为`File.read!/1`的话，错误消息会显示文件不存在。

```
iex> {:ok, body} = File.read("unknown")
** (MatchError) no match of right hand side value: {:error, :enoent} 
```

Enter fullscreen mode Exit fullscreen mode

# 路径模块

`File`模块的很多函数都在参数中包含路径。 路径通常是二进制的。 [`Path`模块](https://hexdocs.pm/elixir/Path.html)中具有用于将二进制作为路径处理的函数。

[`Path.join/2`](https://hexdocs.pm/elixir/Path.html#join/2)将两个自变量作为路径连接起来。 另外，[`Path.expand/1`](https://hexdocs.pm/elixir/Path.html#expand/1)展开路径，使其成为绝对路径。

```
iex> Path.join("elixir", "test")
"elixir/test"
iex> Path.expand("/elixir/test/../config")
"/elixir/config" 
```

Enter fullscreen mode Exit fullscreen mode

处理路径时，不要直接操作字符串，而使用`Path`模块的函数比较好。 因为操作系统的差异会被吸收。 操作文件时，路径中的正斜线`/`在 Windows 中会自动转换为反斜线`\`。

以上是输入输出和文件系统操作相关的主要模块的说明。 接下来，我们将讨论关于输入输出的稍微超前的话题。 因为不是为了写 Elixir 代码的解说，所以跳过也没关系。 了解 IO 系统在虚拟机中是如何实现的。

# 过程和小组领导

`File.open/2`返回元组。 这是因为`IO`模块在内部对进程起作用。

```
iex> {:ok, file} = File.open "hello", [:write]
{:ok, #PID<0.47.0>} 
```

Enter fullscreen mode Exit fullscreen mode

例如，使用[`IO.write/2`](https://hexdocs.pm/elixir/IO.html#write/2)时，`IO`模块会向 PID 所引用的进程发送信息，进行操作。 输出的 4 要素元组就是该消息。 之后，由于没有得到`IO`模块所需的结果而失败。

```
iex> pid = spawn fn ->
...>   receive do: (msg -> IO.inspect msg)
...> end
#PID<0.89.0>
iex> IO.write(pid, "hello")
{:io_request, #PID<0.84.0>, #Reference<0.3003912951.510132227.213667>,
 {:put_chars, :unicode, "hello"}}
** (ErlangError) Erlang error: :terminated
    (stdlib) :io.put_chars(#PID<0.89.0>, :unicode, "hello") 
```

Enter fullscreen mode Exit fullscreen mode

[`StringIO`模块](https://hexdocs.pm/elixir/StringIO.html)在字符串中安装输入输出设备的操作。 `StringIO`变为输入输出设备，可以使用`IO`模块的函数。 [`StringIO.open/2`](https://hexdocs.pm/elixir/StringIO.html#open/2)制作输入输出设备，[`IO.read/2`](https://hexdocs.pm/elixir/IO.html#read/2)从其参照读取第 2 参数的字符数。

```
iex> {:ok, pid} = StringIO.open("hello")
{:ok, #PID<0.96.0>}
iex> IO.read(pid, 4)
"hell" 
```

Enter fullscreen mode Exit fullscreen mode

Erlang VM 通过在流程中对输入/输出设备建模，使同一网络上的不同节点能够与文件操作进行交互，并在节点之间读写文件。

所有 IO 设备在流程中都有一个特殊的组领导。 在`:stdio`上留言时，信息会被发送到组领导那里。 组组长写入标准输出[文件描述符](https://ja.wikipedia.org/wiki/%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E8%A8%98%E8%BF%B0%E5%AD%90)。 [`Process.group_leader/0`](https://hexdocs.pm/elixir/Process.html#group_leader/0)返回调用程序的组领导的 PID。

```
iex> IO.puts :stdio, "hello"
hello
:ok
iex> IO.puts Process.group_leader, "hello"
hello
:ok 
```

Enter fullscreen mode Exit fullscreen mode

团体领导是在每个过程中建立的，用于各种情况。 例如，当在远程终端上执行代码时，可以确保远程节点上的消息将重定向到发出请求的终端并输出。

# 输入输出数据和文字数据

Elixir 中的字符串是字节的集合，字符列表是存储 Unicode 代码点的列表(参见“[Elixir 入门 06 :二进制和字符串以及字符列表](https://dev.to/gumi/elixir-06--35na))。

在`IO`和`File`模块的函数中，自变量中还会给出列表。 不仅如此，还可以在列表中混合整数、嵌套列表以及二进制文件。

```
iex> IO.puts '拝啓' ++ [25964, 20855]
拝啓敬具
:ok
iex> IO.puts [104, 'ello', ?\s, "world"]
hello world
:ok 
```

Enter fullscreen mode Exit fullscreen mode

但是，在输入输出操作中使用列表时，必须小心。 因为列表既可以显示字节的集合，也可以显示字符串。 使用哪一种取决于输入/输出设备的编码。

未指定编码而打开的文件将被视为原始模式。 `IO`从模块使用的函数必须以`bin`开头。 这些函数在参数中接收输入和输出数据。 这意味着将其作为表示字节和二进制的整数列表来处理。

根据`:stdio`和`:utf8`的模式，用 UTF-8 编码打开的文件也可以使用不带`bin`的`IO`模块的函数。 这些函数将字符数据接收为参数。 也就是说，将其作为字符或字符串的列表来处理。

虽说是细微的差异，但在将列表传递给输入输出的函数时要小心。 二进制文件已经根据字节表示，所以总是在 raw 模式下处理。

#### Elixir 入门彩票

*   [Elixir 入门 01 :编写代码并尝试](https://dev.to/gumi/elixir-01--2585)
*   [Elixir 入门 02 :型的基本](https://dev.to/gumi/elixir-02--30n1)
*   [Elixir 入门 03 :运算符的基本](https://dev.to/gumi/elixir-03--33im)
*   [Elixir 入门 04 :模式匹配](https://dev.to/gumi/elixir-04--1346)
*   [仙丹入門 05: 条件-情况/条件/条件](https://dev.to/gumi/elixir-05----casecondif-60o)
*   [Elixir 入门 06 :二进制和字符串以及字符列表](https://dev.to/gumi/elixir-06--35na)
*   [Elixir 入门 07 :关键词列表和地图](https://dev.to/gumi/elixir-07--39hi)
*   [Elixir 入门 08 :模块和函数](https://dev.to/gumi/elixir-08--1c4c)
*   [Introduction T0】 Elixir 09: Rethink](https://dev.to/gumi/elixir-09--1a0p)
*   [仙丹入門 10: EnumとStream](https://dev.to/gumi/elixir-10-enumstream-4fpb)
*   [Elixir 入门 11 :流程](https://dev.to/gumi/elixir-11--2mia)
*   Elixir 入门 12 :输入输出和文件系统
*   [仙丹入門 13: aliasとrequireおよびimport](https://dev.to/gumi/elixir-13-aliasrequireimport-55c1)
*   [Elixir 入门 14 :模块的属性](https://dev.to/gumi/elixir-14--3511)
*   [T0】 Elixir Beginner 15: Structure
*   [Elixir 入门 16 :协议](https://dev.to/gumi/elixir-16--lif)
*   [T0】 Elixir Beginner's Level 17: Record of Internal Package
*   [Elixir 入门 18 :锡吉尔](https://dev.to/gumi/elixir-18--5791)
*   [仙丹入門 19: tryとcatchおよびrescue](https://dev.to/gumi/elixir-19-trycatchrescue-50i8)
*   [Elixir 入门 20 :类型规格和行为](https://dev.to/gumi/elixir-20--j50)
*   [Elixir 入门 21 :调试](https://dev.to/gumi/elixir-21--21a1)
*   [Elixir 入门 22: Erlang 库](https://dev.to/gumi/elixir-22-erlang-2492)
*   [Elixir 入门 23 :下一步](https://dev.to/gumi/elixir-23--50ik)

##### Fanwai

*   [Elixir 入门:使用 Plug 为](https://dev.to/gumi/elixir-plug-40lb)
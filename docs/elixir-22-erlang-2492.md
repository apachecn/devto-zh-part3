# Elixir 入门 22 :错误库

> 原文：<https://dev.to/gumi/elixir-22-erlang-2492>

本文在 Elixir 官方网站的许可下，根据“[Erlang libraries](https://elixir-lang.org/getting-started/erlang-libraries.html) ”的解说，加以修改，对可在 Elixir 上使用的 Erlang 库进行说明。

Elixir 可以使用 Erlang 的库。 它不仅仅包装 Erlang 库，还提供了到 Erlang 代码的直接接口。 列举了 Elixir 上没有的、经常使用且方便的 Erlang 的功能来进行介绍。 详情请参照 Erlang「 [STDLIB Reference Manual](http://erlang.org/doc/apps/stdlib/index.html) ”。

# 二进制模块

内置 Elixir 的`String`模块处理用 UTF-8 编码的二进制文件。 Erlang 的[`binary`模块](http://erlang.org/doc/man/binary.html)在使用不一定是 UTF-8 编码的二进制文件时很方便。 [`String.to_charlist/1`](https://hexdocs.pm/elixir/String.html#to_charlist/1)返回 Unicode 的代码点。 [`:binary.bin_to_list/1`](http://erlang.org/doc/man/binary.html#bin_to_list-1)返回的是原始字节数据。

```
iex> String.to_charlist("π")
[960]
iex> :binary.bin_to_list("π")
[207, 128] 
```

Enter fullscreen mode Exit fullscreen mode

# 输出的格式

Elixir 没有与 c 等语言中的`printf`相对应的函数。 但是，可以使用 Erlang 标准库的函数[`:io.format/2`](http://erlang.org/doc/man/io.html#format-2)和[`:io_lib.format/2`](http://erlang.org/doc/man/io_lib.html#format-2)。 格式为前者为终端输出，后者为 iolist。 格式的规定方法与`printf`不同。 详情请参照[`:io.format/1`](http://erlang.org/doc/man/io.html#format-1)的说明。

```
iex> :io.format("Pi is approximately given by:~10.3f~n", [:math.pi])
Pi is approximately given by:     3.142
:ok
iex> to_string :io_lib.format("Pi is approximately given by:~10.3f~n", [:math.pi])
"Pi is approximately given by:     3.142\n" 
```

Enter fullscreen mode Exit fullscreen mode

另外，在使用 Erlang 的格式函数时，必须注意 Unicode 的处理。

```
iex> :io.format("円周率πは約~10.3f~n", [:math.pi])
åå¨çÏã¯ç´
              3.142
:ok
iex> to_string :io_lib.format("円周率πは約:~10.3f~n", [:math.pi])
<<195, 165, 194, 134, 194, 134, 195, 165, 194, 145, 194, 168, 195, 167, 194,
  142, 194, 135, 195, 143, 194, 128, 195, 163, 194, 129, 194, 175, 195, 167,
  194, 180, 194, 132, 58, 32, 32, 32, 32, 32, 51, 46, 49, 52, 50, 10>> 
```

Enter fullscreen mode Exit fullscreen mode

# crypto 模块

[`crypto`模块](http://erlang.org/doc/man/crypto.html)中包含散列函数、数字签名或者加密等功能。 [`crypto.hash/2`](http://erlang.org/doc/man/crypto.html#hash-2)根据第 2 参数的数据，基于第 1 参数的散列函数求出消息摘要(参照《[SHA-256](http://e-words.jp/w/SHA-256.html) )。 另外，[`Base.encode16/2`](https://hexdocs.pm/elixir/Base.html#encode16/2)是将二进制字符串编码为 base 16 的函数。

```
iex> Base.encode16(:crypto.hash(:sha256, "Elixir"))
"3315715A7A3AD57428298676C5AE465DADA38D951BDFAC9348A8A31E9C7401CB" 
```

Enter fullscreen mode Exit fullscreen mode

`crypto`模块不包含在 Erlang 标准库中。 但是，Erlang 是附送的。 这意味着在使用时必须将其包含在项目的 APP 应用程序列表中。 为此，请在`mix.exs`的`application`中加上以下内容(参照“[Project compilation](https://elixir-lang.org/getting-started/mix-otp/introduction-to-mix.html#project-compilation) ”)。

```
def application do
  [extra_applications: [:crypto]]
end 
```

Enter fullscreen mode Exit fullscreen mode

# 二进制模块

[`digraph`模块](http://erlang.org/doc/man/digraph.html)(及[digraph_utils](http://erlang.org/doc/man/digraph_utils.html) )是处理由顶点和线段组装的有向图([directed graph](https://en.wikipedia.org/wiki/Directed_graph) )的函数 制作图表后，使用模块的算法，例如可以找到两个顶点或循环内的最短路径。

```
iex> digraph = :digraph.new()
{:digraph, #Reference<0.2617837314.593887233.57913>,
 #Reference<0.2617837314.593887233.57914>,
 #Reference<0.2617837314.593887233.57915>, true}
iex> coords = [{0.0, 0.0}, {3.0, 0.0}, {3.0, 4.0}]
[{0.0, 0.0}, {3.0, 0.0}, {3.0, 4.0}]
iex> [v0, v1, v2] = (for c <- coords, do: :digraph.add_vertex(digraph, c))
[{0.0, 0.0}, {3.0, 0.0}, {3.0, 4.0}]
iex> :digraph.add_edge(digraph, v0, v1)
[:"$e" | 0]
iex> :digraph.add_edge(digraph, v1, v2)
[:"$e" | 1]
iex> :digraph.add_edge(digraph, v0, v2)
[:"$e" | 2]
iex> :digraph.get_short_path(digraph, v0, v2)
[{0.0, 0.0}, {3.0, 4.0}] 
```

Enter fullscreen mode Exit fullscreen mode

*   [`:digraph.new/0`](http://erlang.org/doc/man/digraph.html#new-0):制作并返回空的有向图。
*   [`:digraph.add_vertex/2`](http://erlang.org/doc/man/digraph.html#add_vertex-2):在第 1 自变量的图表中添加第 2 自变量的顶点。
*   [`:digraph.add_edge/3`](http://erlang.org/doc/man/digraph.html#add_edge-3):在第 1 自变量的图表中添加连接第 2 和第 3 自变量的线。
*   [`:digraph.get_short_path/3`](http://erlang.org/doc/man/digraph.html#get_short_path-3):在第 1 自变量的图表中，以列表返回从第 2 自变量的顶点到第 3 自变量的顶点的最短路径。

# Erlang 术语存储( ETS )

模块[`ets`](http://erlang.org/doc/man/ets.html)和[`dets`](http://erlang.org/doc/man/dets.html)处理大量的数据结构并存储在内存和磁盘中。

使用 ETS 时，会制作包含元组的桌子。 缺省情况下，ETS 中的表是受保护的。 这意味着只有所有者可以写入表。 其他进程只能读取。 ETS 具备的功能可以用于简单的数据库、密钥和值的保管、缓存的结构等。

`ets`模块的函数会作为副作用更改表的状态。

```
iex> table = :ets.new(:ets_test, [])
#Reference<0.2617837314.593887233.58208>
iex> :ets.insert(table, {"China", 1_390_080_000})
true
iex> :ets.insert(table, {"USA", 325_890_000})
true
iex> :ets.insert(table, {"Japan", 126_750_000})
true
iex> :ets.i(table)
<1   > {<<"USA">>,325890000}
<2   > {<<"China">>,1390080000}
<3   > {<<"Japan">>,126750000}
EOT  (q)uit (p)Digits (k)ill /Regexp --> 
```

Enter fullscreen mode Exit fullscreen mode

*   [`:ets.new/2`](http://erlang.org/doc/man/ets.html#new-2):制作表，返回用于参照的表 ID。 参数是名称和选项的列表。
*   [`:ets.insert/2`](http://erlang.org/doc/man/ets.html#insert-2):在表格中添加对象。
*   [`:ets.i/1`](http://erlang.org/doc/man/ets.html#i-1):用终端浏览桌子。

要退出等待状态，请按 q 键和[enter]键。

```
EOT  (q)uit (p)Digits (k)ill /Regexp -->q
:ok 
```

Enter fullscreen mode Exit fullscreen mode

# math 模块

[`math`模块](http://erlang.org/doc/man/math.html)可以进行三角函数、指数函数或对数函数等一般的数学运算。

```
iex> deg_to_rad = :math.pi() / 180
0.017453292519943295
iex> sin = :math.sin(60 * deg_to_rad)
0.8660254037844386
iex> sqrt_3 = :math.sqrt(3)
1.7320508075688772
iex> sin == sqrt_3 / 2
true
iex> :math.atan2(sqrt_3, 1) / deg_to_rad
59.99999999999999
iex> x = :math.exp(10)
22026.465794806718
iex> :math.log(x)
10.0 
```

Enter fullscreen mode Exit fullscreen mode

*   [`:math.pi/0`](http://erlang.org/doc/man/math.html#pi-0) : the weekly rate π
*   [`:math.sin/1`](http://erlang.org/doc/man/math.html#sin-1):三角函数 sinθ (θ为弧度)
*   [`:math.sqrt/1`](http://erlang.org/doc/man/math.html#sqrt-1) : square root √n
*   [`:math.atan2/2`](http://erlang.org/doc/man/math.html#atan2-2):以弧度返回在 atan2(y，x )中连接原点和坐标( x，y )的线段与 x 轴正方向所成的角度。
*   [`:math.exp/1`](http://erlang.org/doc/man/math.html#exp-1) : the exponential number e n.
*   [`:math.log/1`](http://erlang.org/doc/man/math.html#log-1) : the natural number ln n

# 队列模块

[`queue`](http://erlang.org/doc/man/queue.html)为数据结构，高效安装[FIFO](https://www.weblio.jp/content/%E3%83%95%E3%82%A3%E3%83%95%E3%82%A9) (先进先出)的[两端队列](https://ja.wikipedia.org/wiki/%E4%B8%A1%E7%AB%AF%E3%82%AD%E3%83%A5%E3%83%BC)。

```
iex> q = :queue.new
{[], []}
iex> q = :queue.in("A", q)
{["A"], []}
iex> q = :queue.in("B", q)
{["B"], ["A"]}
iex> {value, q} = :queue.out(q)
{{:value, "A"}, {[], ["B"]}}
iex> value
{:value, "A"}
iex> {value, q} = :queue.out(q)
{{:value, "B"}, {[], []}}
iex> value
{:value, "B"}
iex> {value, q} = :queue.out(q)
{:empty, {[], []}}
iex> value
:empty 
```

Enter fullscreen mode Exit fullscreen mode

*   [`:queue.new/0`](http://erlang.org/doc/man/queue.html#new-0):创建空队列并返回。
*   [`:queue.in/2`](http://erlang.org/doc/man/queue.html#in-2):将项目添加到队列的末尾，返回新的队列。
*   [`:queue.out/1`](http://erlang.org/doc/man/queue.html#out-1):从队列的开头取出项目。 返回值是`{{value, 項目}, キュー}`形式的元组。

# rand 模块

[`rand`模块](http://erlang.org/doc/man/rand.html)

有返回随机值的函数和设置随机种子的函数。

```
iex> :rand.uniform()
0.5094207040603064
iex> _ = :rand.seed(:exs1024, {123, 123534, 345345})
{%{
   jump: #Function<13.15449617/1 in :rand.mk_alg/1>,
   max: 18446744073709551615,
   next: #Function<12.15449617/1 in :rand.mk_alg/1>,
   type: :exs1024
 },
 {[1777391367797874666, 1964529382746821925, 7996041688159811731,
   16797603918550466679, 13239206057622895956, 2190120427146910527,
   18292739386017762693, 7995684206500985125, 1619687243448614582,
   961993414031414042, 10239938031393579756, 12249841489256032092,
   1457887945073169212, 16031477380367994289, 12526413104181201380,
   16202025130717851397], []}}
iex> :rand.uniform()
0.5820506340260994
iex> :rand.uniform(6)
4 
```

Enter fullscreen mode Exit fullscreen mode

*   [`:rand.uniform/0`](http://erlang.org/doc/man/rand.html#uniform-0):在 0.0≦x <1.0 的范围内，以浮点数返回[均匀分布](https://ja.wikipedia.org/wiki/%E4%B8%80%E6%A7%98%E5%88%86%E5%B8%83)的随机数 x。 流程词典将被更新。
*   [`:rand.uniform/1`](http://erlang.org/doc/man/rand.html#uniform-1):对于自变量的整数 N(1≦N )，返回在 0.0≦x <n 的范围内均匀分布[的整数的随机数 x。 流程词典将被更新。](https://ja.wikipedia.org/wiki/%E4%B8%80%E6%A7%98%E5%88%86%E5%B8%83)
*   [`:rand.seed/2`](http://erlang.org/doc/man/rand.html#seed-2):通过第 1 自变量的[算法](http://erlang.org/doc/man/rand.html#type-builtin_alg)和第 2 自变量的整数元组，为进程词典的随机数生成提供种子。 返回值为初始状态。

# zip 和 zlib 模块

使用[`zip`模块](http://erlang.org/doc/man/zip.html)，可以读写存储器和磁盘的 ZIP 文件。 另外，可以取出文件信息。 以下代码计算 ZIP 文件中的文件数。

```
iex(75)> :zip.foldl(fn _, _, _, acc -> acc + 1 end, 0, :binary.bin_to_list("samples.zip"))
{:ok, 70} 
```

Enter fullscreen mode Exit fullscreen mode

*   [`:zip.foldl/3`](http://erlang.org/doc/man/zip.html#foldl-3):对第 3 参数的档案中的文件调用第 1 参数的回调函数。 第二个参数是传递给回调的初始值。 回调函数接收的参数有以下四个:
    *   归档文件中的文件名
    *   返回文件信息的函数
    *   返回文件内容的函数
    *   从初始值合计的值
*   [`:binary.bin_to_list/1`](http://erlang.org/doc/man/binary.html#bin_to_list-1):将参数作为字节列表返回。

[`zlib`模块](https://dev.toThe%20zlib%20module)是[zlib 库](http://www.zlib.net/)的 API，处理数据的压缩和解压缩。

```
iex> song = "
...> The snow glows white on the mountain tonight
...> Not a footprint to be seen
...> A kingdom of isolation
...> And it looks like I'm the queen"
"\nThe snow glows white on the mountain tonight\nNot a footprint to be seen\nA kingdom of isolation\nA
nd it looks like I'm the queen"
iex> compressed = :zlib.compress(song)<<120, 156, 29, 204, 49, 14, 195, 48, 12, 67, 209, 221, 167, 224, 214, 115, 116,
  236, 210, 169, 23, 112, 16, 197, 22, 108, 139, 77, 172, 192, 215, 175, 208,
  145, 15, 248, 76, 159, 42, 152, 198, 133, 210, 185, 38, 86, 85, 23, 208, 224,
  ...>>
iex> byte_size(song)
127
iex> byte_size(compressed)
107
iex> :zlib.uncompress(compressed)
"\nThe snow glows white on the mountain tonight\nNot a footprint to be seen\nA kingdom of isolation\nA
nd it looks like I'm the queen" 
```

Enter fullscreen mode Exit fullscreen mode

*   [`:zlib.compress/1`](http://erlang.org/doc/man/zlib.html#compress-1):用 zlib 报头和校验和压缩数据。
*   [`byte_size/1`](https://hexdocs.pm/elixir/Kernel.html#byte_size/1):返回存储比特串所需的字节数。
*   [`:zlib.uncompress/1`](http://erlang.org/doc/man/zlib.html#uncompress-1):通过 zlib 头和校验和解除数据的压缩。

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
*   [Elixir 入门 12 :输入输出和文件系统](https://dev.to/gumi/elixir-12--4og6)
*   [仙丹入門 13: aliasとrequireおよびimport](https://dev.to/gumi/elixir-13-aliasrequireimport-55c1)
*   [Elixir 入门 14 :模块的属性](https://dev.to/gumi/elixir-14--3511)
*   [T0】 Elixir Beginner 15: Structure
*   [Elixir 入门 16 :协议](https://dev.to/gumi/elixir-16--lif)
*   [T0】 Elixir Beginner's Level 17: Record of Internal Package
*   [Elixir 入门 18 :锡吉尔](https://dev.to/gumi/elixir-18--5791)
*   [仙丹入門 19: tryとcatchおよびrescue](https://dev.to/gumi/elixir-19-trycatchrescue-50i8)
*   [Elixir 入门 20 :类型规格和行为](https://dev.to/gumi/elixir-20--j50)
*   [Elixir 入门 21 :调试](https://dev.to/gumi/elixir-21--21a1)
*   Elixir 入门 22 :错误库
*   [Elixir 入门 23 :下一步](https://dev.to/gumi/elixir-23--50ik)

##### Fanwai

*   [Elixir 入门:使用 Plug 为](https://dev.to/gumi/elixir-plug-40lb)
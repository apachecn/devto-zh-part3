# 查找 PHP 程序中的内存问题—第 2 部分

> 原文：<https://dev.to/tddenbraber/finding-memory-issues-in-php-programs-part-2-b4i>

### 寻找 PHP 程序中的内存问题—第 2 部分

[![](img/d4b3081bc639876579ee7adf33d6cb7e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xf0nPJ9F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/0%2AAVdDGsKTeEYSw5ig)

内存问题的原因通常很难找到。在[之前的文章](https://www.moxio.com/blog/33/finding-memory-issues-in-php-programs)中，我们研究了两种寻找内存问题元凶的方法。万一你错过了，就去看看吧！

在这篇文章中，我们将看看另一个查找内存泄漏的工具: [php-memprof](https://github.com/arnaud-lb/php-memory-profiler/) ，由 Arnaud Le Blanc 创建。

### 另一个侧写员……为什么？

每个分析器都有自己的特点。在上一篇文章中，我们看到 Xdebug profiler 生成了一个“累积内存配置文件”:它显示了某个函数在程序的生命周期中所做的所有分配的总和。这使得生成的概要文件有时很难解释。使用 Xdebug 生成的配置文件，您无法看到在生成配置文件时内存是否已经被释放。但是，这种情况很可能表明存在内存泄漏。php-memprof 就是为了填补这一空白而产生的。它提供了可以用来发现函数是否“泄漏”内存的信息。

### 设置 php-memprof

php-memprof 的安装可以使用 PECL (pecl install memprof)或者手动完成。下载源代码后，确保已经安装了 [libJudy](http://judy.sourceforge.net/) ，并在源代码目录中运行以下命令。

```
phpize
./configure 
make
make install 
```

确保加载扩展(在 php.ini 中设置 extension=memprof.so，或者用 PHP-dex tension = memprof . so my _ script . PHP 运行脚本)。对于我自己的分析，我使用一个 Docker 映像,它已经默认安装并加载了扩展。

注意，并行运行 Xdebug 和 php-memprof 将不起作用。为了能够运行 php-memprof，必须禁用 Xdebug。

### 运行示例

作为我们的运行示例，我们将使用一个简单的基于数组的缓存实现。store 函数是保持内存分配的(有效)代码的一个很好的例子，即使在它终止之后。

```
<?php
class ArrayBasedCache {
  private $cache = [];

  public function store(string $key, $data) {
    $this->cache[$key] = $data;
  }

  public function has(string $key) {
    return isset($this->cache[$key]);
  }

  public function get(string $key) {
    if ($this->has($key) === false) {
      throw new LogicException("Tried to get '$key' from cache, but it does not exist. has(\$key) should be checked first!");
    }
    return $this->cache[$key];
  }

  public function clear() {
    $this->cache = [];
  }
} 
```

### 使用 php-memprof

要通知 php-memprof 应该开始分析，必须调用 memprof_enable()函数。在调用这个函数之后，php-memprof 开始跟踪内存分配。这意味着在调用 memprof_enable()之前分配的内存*将不会被考虑在内。*

php-memprof 收集的信息可以使用以下函数之一进行转储:

*   memprof_dump_array()以数组格式转储信息
*   memprof _ dump _ callgrind(resource $stream)将信息以 call grind 格式转储到给定的$ stream，可以使用 [KCachegrind](https://kcachegrind.github.io/html/Home.html) 或 [qCachegrind](https://sourceforge.net/projects/qcachegrindwin/) 对其进行分析(如前一篇文章所示)
*   memprof _ dump _ pprof(resource $stream)将信息以 [pprof](https://github.com/google/pprof) 格式转储到给定的$ stream

让我们运行下面的代码:

```
<?php 
$cache = new ArrayBasedCache;

memprof_enable();
printf("A:\n%s\n", json_encode(memprof_dump_array(), JSON_PRETTY_PRINT));

$cache->store("a", 123);

printf("B:\n%s\n", json_encode(memprof_dump_array(), JSON_PRETTY_PRINT));
memprof_disable(); 
```

该脚本的 JSON 编码输出如下:

答:

```
{
    "memory_size": 0,
    "blocks_count": 0,
    "memory_size_inclusive": 0,
    "blocks_count_inclusive": 0,
    "calls": 1,
    "called_functions": []
} 
```

乙:

```
{
    "memory_size": 103,
    "blocks_count": 3,
    "memory_size_inclusive": 479,
    "blocks_count_inclusive": 5,
    "calls": 1,
    "called_functions": {
        "ArrayBasedCache::store": {
            "memory_size": 376,
            "blocks_count": 2,
            "memory_size_inclusive": 376,
            "blocks_count_inclusive": 2,
            "calls": 1,
            "called_functions": []
        }
    }
} 
```

首先要注意的是转储结构是递归的。顶层字段指示来自主程序的数据，即在任何函数之外执行的代码。

在 called_functions 字段中，我们可以从全局上下文中看到被调用者。对于每个被调用的函数，相同的字段再次出现。

让我们检查输出中看到的所有字段，找出它们的含义:

*   memory_size:从当前上下文分配的字节数；
*   blocks_count:从当前上下文分配的内存块数；
*   memory_size_inclusive:由函数本身分配的字节数加上由当前上下文的任何被调用方分配的所有内存；
*   blocks_count_inclusive:与 memory_size_inclusive 相同，但以块数表示；
*   调用:从总体上下文中调用此函数的次数；
*   called_functions:一个字典，关键字是函数名，字段是这个表中的所有字段。该字段表示调用堆栈。

我们可以看到，A-dump 不包含 ArrayBasedCache 本身的创建信息:这个调用发生在 php-memprof 启用之前，因此不被考虑。由于 A-dump 是在启用 php-memprof 之后立即生成的，我们看到转储没有关于任何已分配内存的信息。

B-dump 显示 ArrayBasedCache::store 被‘main’程序调用过一次。我们还可以看到，ArrayBasedCache::store 的 memory_size 为 376 字节，尽管在生成 B-dump 时它的执行已经终止。这些字节仍然在内存中，因为 store 方法将数据添加到了本地$cache 字段，而不是删除它。

让我们看另一个例子:

```
<?php 
$cache = new ArrayBasedCache;

memprof_enable();

$cache->store("a", 123);
$cache->clear();

json_encode(memprof_dump_array(), JSON_PRETTY_PRINT);
memprof_disable(); 
```

它的输出:

```
{
    "memory_size": 103,
    "blocks_count": 3,
    "memory_size_inclusive": 103,
    "blocks_count_inclusive": 3,
    "calls": 1,
    "called_functions": {
        "ArrayBasedCache::store": {
            "memory_size": 0,
            "blocks_count": 0,
            "memory_size_inclusive": 0,
            "blocks_count_inclusive": 0,
            "calls": 1,
            "called_functions": []
        },
        "ArrayBasedCache::clear": {
            "memory_size": 0,
            "blocks_count": 0,
            "memory_size_inclusive": 0,
            "blocks_count_inclusive": 0,
            "calls": 1,
            "called_functions": []
        }
    }
} 
```

调用$cache->clear()后，缓存中的所有项目都被删除。由$cache->store(...)因此被删除，我们可以在转储中看到，在进行转储时，ArrayBasedCache::store 确实没有分配任何内存。

可以想象，对于较大的程序，数组输出会变得非常大。对于小的、孤立的脚本，memory_dump_array 工作得很好。对于较大的程序，我发现查看 memory_dump_callgrind 函数的可视化输出更有用。

### 包装完毕

在这篇文章中，我们看了另一个分析器，它从一个不同于我们上次看的 Xdebug 分析器的角度来处理内存分析。这个由两部分组成的系列旨在帮助您更快地找到内存问题的原因，并采用一个更有条理的过程。已经讨论了几种技术，比如记录内存使用、使用分析器和分析工具。您是否错过了本系列中应该讨论的一项重要技术？[让我们知道](http://www.twitter.com/moxio)！

照片由[金奎大·穆伊吉](https://unsplash.com/photos/91LGCVN5SAI?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/leak?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

*原载于 2019 年 2 月 21 日*[*【www.moxio.com】*](https://www.moxio.com/blog/36/finding-memory-issues-in-php-programs-part-2)*。*

* * *
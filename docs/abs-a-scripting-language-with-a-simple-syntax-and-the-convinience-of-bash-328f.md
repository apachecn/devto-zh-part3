# ABS，一种具有简单语法和 bash 便利性的脚本语言

> 原文：<https://dev.to/vonheikemen/abs-a-scripting-language-with-a-simple-syntax-and-the-convinience-of-bash-328f>

# ABS 编程语言:shell 脚本的乐趣。

按照他的创造者的话说:

> 它允许将 shell 的便利性与现代编程语言的语法相结合。

我看 [ABS](https://github.com/abs-lang/abs) 的方式就像 bash 上面的一层。它填补了当 Bash 感觉笨拙而 Python(或 Node)太多时的空白。它有一个简化的类似 C 的语法和一个简单的方法来调用 shell 命令并处理它们的输出。

## 有什么好的？

*   它在 [go](https://golang.org/) 里写着。
*   有适用于 Windows、Linux 和 macOS 的[二进制文件](https://github.com/abs-lang/abs/releases)。
*   容易学习，因为在语法上没有什么特别的。它与许多主流编程语言非常相似。
*   [文档](https://www.abs-lang.org/)已经足够好了。

## 有什么蹊跷？

*   这是新的，非常新。写这篇文章的时候才 5 个月大。

*   目前还没有任何相关的工具，没有用于编辑器的花哨插件，没有语法亮点，没有 linters 或语言服务器协议之类的东西。

*   社区基本不存在。

## 腹肌&并肩痛击

这些片段摘自[主页](https://www.abs-lang.org/)，它们展示了如何获取我们的 IP 地址并打印其各部分的总和。

这就是你如何使用好的 Bash 来做这件事。

```
# Simple program that fetches your IP and sums it up
RES=$(curl -s 'https://api.ipify.org?format=json' || "ERR")

if [ "$RES" = "ERR" ]; then echo "An error occurred"
  exit 1
fi IP=$(echo $RES | jq -r ".ip")
IFS=. read first second third fourth <<EOF  ${IP##*-}  EOF total=$((first + second + third + fourth))
if [ $total -gt 100 ]; then echo "The sum of [$IP] is a large number, $total."
fi 
```

Enter fullscreen mode Exit fullscreen mode

我最近不怎么写 shell 脚本，所以我有点迷失在这里了。我对正在发生的事情有一点了解，但仅仅是因为变量名和一些常见的命令。这种语法与主流编程语言如此不同，以至于对我们许多人来说感觉像是黑魔法。

现在让我们看看 ABS 的“翻译”版本。

```
res = `curl -s 'https://api.ipify.org?format=json'`

if !res.ok {
  echo("An error occurred: %s", res)
  exit(1)
}

ip = res.json().ip
total = ip.split(".").map(int).sum()
if total > 100 {
  echo("The sum of [%s] is a large number, %s.", ip, total)
} 
```

Enter fullscreen mode Exit fullscreen mode

因为我大部分时间都在编写 Javascript 和 PHP，所以在这里我感觉就像在家里一样。请注意，这与其他语言非常相似，以至于在 markdown 块中,`go`语法高亮显示效果很好。

### 里面发生了什么？

我现在解释第二个片段。

让我们从头开始。

```
res = `curl -s 'https://api.ipify.org?format=json'` 
```

Enter fullscreen mode Exit fullscreen mode

这是[在 ABS 中执行命令](https://www.abs-lang.org/syntax/system-commands)的一种方式。反斜杠内的任何内容都被视为 bash 命令。而这只是牛逼的一半，另一半在这个命令的返回值里。

你从命令中得到的就像一个 javascript 对象，它有方法和属性，它也使用点符号来访问它们。因为这是一个命令结果，所以它有一个`ok`属性，让您知道命令是成功结束还是有错误，所以您可以像这样进行验证:

```
if !res.ok {
  echo("An error occurred: %s", res)
  exit(1)
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的老朋友`if`语句确实如你所想，你也可以使用你所熟悉和喜爱的[逻辑运算符](https://www.abs-lang.org/syntax/operators)。在`if`块中，你可以看到两个[内置函数](https://www.abs-lang.org/types/builtin-function)，它们无需特殊语法就可以调用普通命令。正如您所料，`echo`和`exit`做了与 Bash 相同的事情。

美好还不止于此。这个`res`变量是一个[字符串](https://www.abs-lang.org/types/string)，这意味着它有其他方法，包括可以将字符串类型转换为[散列](https://www.abs-lang.org/types/hash)(如果是有效的 json 字符串)的`.json()`。一旦我们有了代表由`curl`发出的 http 请求的散列类型，我们就可以开始处理 IP 值了。

```
ip = res.json().ip
total = ip.split(".").map(int).sum() 
```

Enter fullscreen mode Exit fullscreen mode

我想让你注意这部分:`.map(int)`，如果你看一下[数组](https://www.abs-lang.org/types/array)类型的方法，你会看到`map`将一个函数作为它的参数，这很重要，因为这意味着更高阶的函数是可能的(查看[这篇文章](https://dev.to/damcosset/higher-order-functions-in-javascript-4j8b)以了解为什么这很棒)。)

## 让我们一起玩吧

我用 [cmus](https://cmus.github.io/) 作为我的音乐播放器。它是如此的“轻量级”,以至于当它开始播放一首歌曲时，它甚至不会显示桌面通知，但它确实让你有机会在每次播放时通过设置`status_display_program`选项来执行程序。我们将尝试做与这个 python 脚本几乎相同的事情[。](https://github.com/cmus/cmus/wiki/status_diplay_notify_send.py)

我将保持简单，所以我将忽略 python 版本中的许多东西，我们将专注于通知部分。这是我从 python 脚本中所感兴趣的:

```
# See if script is being called by cmus before proceeding. if sys.argv[1].startswith("status"):

#... moar code ... 
# We only display a notification if something is playing. if status_data("status") == "playing":

#... moar code ... 
# Execute notify-send with our song data. subprocess.call('notify-send -u low -t 500 "' + \
                 notify_summary + '" "by ' + \
                 notify_body + ' "', shell=True) 
```

Enter fullscreen mode Exit fullscreen mode

这就是我想要实现的目标。我们在 ABS 中的主块应该是这样的:

```
if is_playing {
  summary, body = get_data(process_args())

  `notify-send "$summary" "$body"`
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的剧本会有更多，但这基本上是我想做的。这是我们将要采取的步骤。

*   想想我们如何能得到状态。
*   收集 cmus 给我们的其他论点。
*   拿重要的东西。
*   向`notify-send`命令发送数据。

让我们从`is_playing`开始。

如果你记得在 python 版本中，我们从`sys.argv`属性获取状态，它检查参数列表是否以字符串‘status’开始。在 Bash 中，你可以检查特殊变量`$1`或`$2`或`$3`...你明白这一点，每一个都保存了一个给脚本的位置参数的值。在 ABS 中，我们有`arg`函数，你给它一个数字，它返回一个参数。参数列表从 0 开始，前两个总是解释器的二进制和脚本路径，所以我们应该用索引 2 和 3 检查参数，它们会给我们状态字符串和状态值。

我们这样定义我们的条件:

```
is_playing = arg(2) == "status" && arg(3) == "playing" 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们获取 cmus 提供的其余数据。在这一点上，如果有一个函数返回一个带有参数列表的数组就好了，但是我们没有。相反，我们将创建一个用数字调用`arg`的函数，直到我们得到一个空字符串，这意味着我们已经到达了列表的末尾。这个函数将参数存储在一个散列中并返回它。

```
process_args = f() {
  result = {}
  index = 4
  current_arg = arg(index)

  while current_arg != "" {
    result[current_arg] = arg(index + 1)

    index += 2
    current_arg = arg(index)
  }

  result
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个用户定义的[功能](https://www.abs-lang.org/types/function)。这里有几件有趣的事情，首先是我们用“关键字”`f`和一些括号创建了一个函数。另一件有趣的事情是，我们没有一个`return`关键字，取而代之的是，我们在函数的末尾有一个孤独的 hash。有一个`return`关键字，但是只有当你想提前返回或者解释器不知道在函数的最后一条语句中返回什么的时候才有必要。

谜题的最后一块是`get_data`，这个函数将处理来自`process_args`的结果，并确保我们的通知总是得到一个`summary`和一个`body`。

```
get_data = f(args) {
  title  = args.title   || args.file.split("/").pop()
  artist = args.artist  || "Unknown"
  album  = args.album   || false

  msg = fmt("<b>Artist:</b> %s", artist)

  if album {
    msg += fmt("\n<b>Album:</b> %s", album)
  }

  return [title, msg]
} 
```

Enter fullscreen mode Exit fullscreen mode

我们不能相信 cmus 给我们的参数，因为我们想要的大多数数据都在文件的元数据中，可能会丢失，这就是为什么我们评估每个属性，并使用`||`操作符有一个“备用”值以防万一。接下来，我们为通知的主体创建一条漂亮的消息，我们使用了与其他语言中的`printf`类似的`fmt`函数。一旦我们得到了所有需要的东西，我们就返回一个数组。

所以最终的结果是这样的:

```
process_args = f() {
  result = {}
  index = 4
  current_arg = arg(index)

  while current_arg != "" {
    result[current_arg] = arg(index + 1)

    index += 2
    current_arg = arg(index)
  }

  result
}

get_data = f(args) {
  title  = args.title   || args.file.split("/").pop()
  artist = args.artist  || "Unknown"
  album  = args.album   || false

  msg = fmt("<b>Artist:</b> %s", artist)

  if album {
    msg += fmt("\n<b>Album:</b> %s", album)
  }

  return [title, msg]
}

is_playing = arg(2) == "status" && arg(3) == "playing"

if is_playing {
  summary, body = get_data(process_args())

  `notify-send "$summary" "$body"`
} 
```

Enter fullscreen mode Exit fullscreen mode

## 还想了解更多？

请务必访问 github 资源库中的[作者网站](https://odino.org/)、[文档](https://www.abs-lang.org/)或[示例目录](https://github.com/abs-lang/abs/tree/master/examples)。

如果你知道`go`考虑为这个项目做贡献。

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png) [ abs-lang ](https://github.com/abs-lang) / [ abs](https://github.com/abs-lang/abs)

### ABS 编程语言之家:shell 脚本的乐趣。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![abs language logo](img/757ca5a43a18d0a756fdd707d2c61888.png)](https://www.abs-lang.org/)

[![GitHub Workflow Status (branch)](img/467a0a67d107d0049b7cfb5b73e2c23d.png)](https://github.com/abs-lang/abs)[![License](img/3fe6b597b077925abec5225e952b302a.png)](https://github.com/abs-lang/abs)[![Version](img/0e55ec093a2130a335b5e3136b83ef38.png)](https://github.com/abs-lang/abs)[![undefined](img/0eb04ebb3e4609adf8030f267816a1c2.png)](https://camo.githubusercontent.com/817db22148aba4cc08e58c3165f27241bba8fad8783e2c7694e25879b5964034/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f72656c656173652d646174652f6162732d6c616e672f6162732e7376673f7374796c653d666c6174)[![undefined](img/5103e74acfcb826e936a18f5ab3f70ef.png)](https://camo.githubusercontent.com/f6c5a016b1a50e6cc7f12bc986439ea0df4f1c419ce9cef3e0fef76d93553dd1/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f646f776e6c6f6164732f6162732d6c616e672f6162732f746f74616c2e7376673f7374796c653d666c6174)
[![undefined](img/a89a45ec97b9de925eba0865ad95d31a.png)](https://camo.githubusercontent.com/162613af9398a49995f5b10c97d890af5f931b4d8766d011b329ee25de4afc03/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f706c6174666f726d2d6c696e7578207c206d61636f7378207c2077696e646f77732d7265642e737667)[![undefined](img/4a77ac6443643f2247a681bbe5c514fb.png)](https://camo.githubusercontent.com/e23b79befe88ddb3bd555d7c3a964a612eb96753d56bc2aa3a2a7633707b76e9/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6c6173742d636f6d6d69742f6162732d6c616e672f6162732e7376673f7374796c653d666c6174)[![Coverage Status](img/c8021d203fa35d74232a868b4b44ee01.png)](https://coveralls.io/github/abs-lang/abs)
[![undefined](img/09d69f109585e3ec959bf92c60771a2f.png)](https://camo.githubusercontent.com/799dae3845839d903941a8b4e625f95113c380eafca5a0c02fb3e79a733aef75/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f636f6e7472696275746f72732f6162732d6c616e672f6162732e7376673f7374796c653d666c6174)![undefined](img/d048baff5c09c71dc42069440470e049.png)[![undefined](img/f6d7f8d9d6e54591e6d01863910c2688.png)](https://camo.githubusercontent.com/f511d0395dc015c739f6fed77c0bd169d5be12afadd8de8b5bde6fd8054ed8b6/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f6973737565732d70722d636c6f7365642f6162732d6c616e672f6162732e7376673f7374796c653d666c6174)[![undefined](img/731ff5561074627dfee42c10ca78bc3b.png)](https://camo.githubusercontent.com/7325796578556666a83661e99169a68029d26066f1f04cb79aba2877486eb89e/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f73746172732f6162732d6c616e672f6162732e7376673f7374796c653d736f6369616c)

# ABS 编程语言

ABS 是一种编程语言，当你在你的终端上写脚本时效果最好。它试图将 Python 或 Ruby 等语言的优雅与 Bash 的便利结合起来。

```
tz = `cat /etc/timezone`
continent, city = tz.split("/")
echo("Best city in the world?")
selection = stdin()
if selection == city {
  echo("You might be biased...")
}
```

Enter fullscreen mode Exit fullscreen mode

观看它的实际应用:

[![asciicast](img/7aa0a00208ddd358535049cfe865bed4.png)](https://asciinema.org/a/218909)

让我们尝试获取我们的 IP 地址，并打印其各部分的总和，如果它大于 100。在 Bash 中，您可以这样做:

```
# Simple program that fetches your IP and sums it up
RES=$(curl -s 'https://api.ipify.org?format=json' || "ERR")
if [ "$RES" = "ERR" ]; then
    echo "An error occurred"
    exit
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/abs-lang/abs)

* * *

感谢您的阅读。如果你觉得这篇文章有用，并想支持我的努力，请给我买一杯☕咖啡。

[![buy me a coffee](img/9e6f0f1293ebfd51e2e5cab78e6e6a08.png)](https://www.buymeacoffee.com/vonheikemen)
# 使用 Ruby 和 Rexe 增强您的 Shell 脚本

> 原文：<https://dev.to/keithrbennett/boost-your-shell-scripting-with-ruby-and-rexe-54pb>

【注意:这是一篇长文！大多数章节都是独立的，所以可以随意浏览或跳过。]

* * *

Rexe 是一个 Ruby 脚本和 gem，它通过以下方式增加了 Ruby 在命令行上的有用性和简洁性:

*   使用 JSON、YAML、Ruby marshalling、Awesome Print 等工具自动解析和格式化
*   简化 Ruby 作为外壳过滤器的使用，可以选择将输入简化为行、枚举器或一个大字符串
*   从命令行提取管道；requires 和其他选项可以在环境变量中设置
*   启用 Ruby helper 文件的加载来保持代码的干爽和命令行代码的高水平
*   阅读和评估 a ~/。rexerc 文件，用于您的共享定制代码和公共需求

* * *

Shell 脚本对于简单的任务来说很棒，但是对于任何不重要的任务来说，它很容易变得晦涩和笨拙(双关语！).

这个问题通常可以通过编写 Ruby 脚本来解决。Ruby 提供了细粒度的控制，这是一种关于清晰、简洁和表现力的语言。

不幸的是，当有多个操作系统命令需要调用时，Ruby 也会很笨拙。

有时一个好的解决方案是在同一个命令行上结合 Ruby 和 shell 脚本。Rexe 会加倍你的力量。

### 在命令行上使用 Ruby 解释器

让我们先来看看 Ruby 解释器已经提供了什么。这里我们在命令行上使用`ruby`，使用一个中间环境变量来简化逻辑并保存数据供将来的命令使用。代码后面是输出的摘录:

```
➜  ~   export EUR_RATES_JSON=`curl https://api.exchangeratesapi.io/latest`
➜  ~   echo $EUR_RATES_JSON | ruby -r json -r yaml -e 'puts JSON.parse(STDIN.read).to_yaml' 
```

```
---
rates:
  MXN: 21.96
  AUD: 1.5964
  HKD: 8.8092
  ...
base: EUR
date: '2019-03-08' 
```

不幸的是，配置设置(`require`)以及读取、解析和格式化使命令变得冗长乏味，阻碍了这种方法。

### 雷克斯

Rexe 可以简化这样的命令。此外，rexe 提供了开关激活的输入解析和输出格式化，因此从一种格式转换到另一种格式很简单。前面的`ruby`命令可以在`rexe`中表示为:

```
➜  ~   echo $EUR_RATES_JSON | rexe -mb -ij -oy self 
```

或者，更简洁地说(`self`是 rexe 命令的默认 Ruby 源代码):

```
➜  ~   echo $EUR_RATES_JSON | rexe -mb -ij -oy 
```

这些命令选项可能看起来晦涩难懂，但是它们是符合逻辑的，所以学习它们应该不会花很长时间:

*   `-mb` - **模式**将所有标准输入作为单个**大**字符串来消耗
*   `-ij` -用 **JSON** 解析**输入**；`self`将被解析的对象
*   `-oy` - **输出**最终值为 **YAML**

如果输入来自 JSON 或 YAML 文件，rexe 会根据文件的扩展名确定输入格式，这就更简单了:

```
➜  ~   rexe -f eur_rates.json -oy 
```

雷克斯在[https://github.com/keithrbennett/rexe](https://github.com/keithrbennett/rexe)，可以安装`gem install rexe`。Rexe 提供了几种在命令行上简化 Ruby 的方法，扭转了局面，因此更频繁地这样做是可行的。

* * *

这是撰写本文时 rexe 的帮助文本:

```
rexe -- Ruby Command Line Executor/Filter -- v1.0.1 -- https://github.com/keithrbennett/rexe

Executes Ruby code on the command line, 
optionally automating management of standard input and standard output,
and optionally parsing input and formatting output with YAML, JSON, etc.

rexe [options] [Ruby source code]

Options:

-c  --clear_options        Clear all previous command line options specified up to now
-f  --input_file           Use this file instead of stdin for preprocessed input; 
                           if filespec has a YAML and JSON file extension,
                           sets input format accordingly and sets input mode to -mb
-g  --log_format FORMAT    Log format, logs to stderr, defaults to none
                           (see -o for format options)
-h, --help                 Print help and exit
-i, --input_format FORMAT  Input format
                             -ij  JSON
                             -im  Marshal
                             -in  None (default)
                             -iy  YAML
-l, --load RUBY_FILE(S)    Ruby file(s) to load, comma separated;
                             ! to clear all, or precede a name with '-' to remove
-m, --input_mode MODE      Input preprocessing mode (determines what `self` will be):
                             -ml  line; each line is ingested as a separate string
                             -me  enumerator (each_line on STDIN or File)
                             -mb  big string; all lines combined into one string
                             -mn  none (default); no input preprocessing; 
                                  self is an Object.new 
-n, --[no-]noop            Do not execute the code (useful with -g);
                           For true: yes, true, y, +; for false: no, false, n
-o, --output_format FORMAT Output format (defaults to puts):
                             -oi  Inspect
                             -oj  JSON
                             -oJ  Pretty JSON
                             -om  Marshal
                             -on  No Output (default)
                             -op  Puts
                             -os  to_s
                             -oy  YAML
-r, --require REQUIRE(S)   Gems and built-in libraries to require, comma separated;
                             ! to clear all, or precede a name with '-' to remove

---------------------------------------------------------------------------------------

In many cases you will need to enclose your source code in single or double quotes.

If source code is not specified, it will default to 'self', 
which is most likely useful only in a filter mode (-ml, -me, -mb).

If there is a .rexerc file in your home directory, it will be run as Ruby code 
before processing the input.

If there is a REXE_OPTIONS environment variable, its content will be prepended
to the command line so that you can specify options implicitly 
(e.g. `export REXE_OPTIONS="-r awesome_print,yaml"`) 
```

### 简化 Rexe 调用

有两种主要方法可以让 rexe 命令行更加简洁:

*   通过将配置提取到`REXE_OPTIONS`环境变量
*   通过将低级和/或共享代码提取到使用`-l`或使用`~/.rexerc`隐式加载的帮助文件中

### REXE _ OPTIONS 环境变量

`REXE_OPTIONS`环境变量可以包含在 rexe 命令行中指定的命令行选项:

与其这样:

```
➜  ~   rexe -r wifi-wand -oa WifiWand::MacOsModel.new.wifi_info 
```

你可以这样做:

```
➜  ~   export REXE_OPTIONS="-r wifi-wand -oa"
➜  ~   rexe WifiWand::MacOsModel.new.wifi_info
➜  ~   # [more rexe commands with the same options] 
```

将配置选项放在`REXE_OPTIONS`中可以有效地创建自定义默认值，当您在大多数或所有命令中使用相同的选项时，这很有用。在 rexe 命令行上指定的任何选项都将覆盖环境变量选项。

像任何环境变量一样，`REXE_OPTIONS`也可以在您的启动脚本中设置，在命令行上使用`export`输入，或者在加载了`source`或`.`的另一个脚本中输入。

### 加载文件

环境变量方法对命令行选项很有效，但是如果我们想要指定你的 rexe 代码可以使用的 Ruby T2 代码呢？

为此，rexe 允许您使用`-l`选项*加载* Ruby 文件，或者在`~/.rexerc`文件的情况下隐式加载(无需您指定)。这里有一个例子，你可能会在这样的文件:

```
# Open YouTube to Wagner's "Ride of the Valkyries"
def valkyries
  `open "http://www.youtube.com/watch?v=P73Z6291Pt8&t=0m28s"`
end 
```

跑题一点，为什么要这样？您可能希望能够去另一个房间，直到一个长时间的工作完成，并在完成时得到通知。方法将启动一个浏览器窗口，指向理查德·瓦格纳的《女武神之旅》，从音乐中一个活跃的点开始(参见脚注^2 关于自动播放)。(`open`命令是 Mac 专用的，在 Windows 上可以替换为`start`、浏览器命令名等。)[见脚注^3 关于操作系统的可移植性]。

如果你喜欢这种音频通知，你可以下载公共领域的音频文件，并使用类似播放器的命令，比如 Mac OS 上的`afplay`，或者 Linux 上的`mpg123`或`ogg123`。这种方法是轻量级的，不需要网络访问，并且不会留下一个打开的浏览器窗口让你关闭。

这里有一个如何使用`valkyries`方法的例子，假设上面的配置是从你的`~/.rexerc`文件或者一个显式加载的文件:
中加载的

```
➜  ~   tar czf /tmp/my-whole-user-space.tar.gz ~ ; rexe valkyries 
```

(注意，使用了`;`而不是`&&`，因为无论命令是否成功，我们都希望听到音乐。)

您可能认为为这个`open`创建一个别名或一个最小的 shell 脚本(而不是 Ruby 脚本)会是一个更简单、更自然的方法，我同意您的观点。然而，随着时间的推移，这些功能的数量可能会变得难以管理，而使用 Ruby，您可以构建一个非常广泛且组织良好的功能库。此外，该功能可以提供给所有的 Ruby 代码(例如，通过把它放在 gem 中)，而不仅仅是命令行。

例如，在一个 gem 或加载的文件中可能有这样的内容:

```
def play(piece_code)
  pieces = {
    hallelujah: "https://www.youtube.com/watch?v=IUZEtVbJT5c&t=0m20s",
    valkyries: "http://www.youtube.com/watch?v=P73Z6291Pt8&t=0m28s",
    wm_tell: "https://www.youtube.com/watch?v=j3T8-aeOrbg&t=0m1s",
    # ... and many, many more
  }
  `open #{Shellwords.escape(pieces.fetch(piece_code))}`
end 
```

...你可以这样称呼它:

```
➜  ~   tar czf /tmp/my-whole-user-space.tar.gz ~ ; rexe 'play(:hallelujah)' 
```

(您需要引用`play`调用，因为否则 shell 将处理并删除括号。或者，您可以用反斜杠对括号进行转义。)

本文末尾的一个例子展示了如何为成功和失败演奏不同的音乐。

### 测井

代码完成后，可以选择将日志条目输出到标准错误。这个条目是下面$RC 部分描述的`$RC` OpenStruct 的散列表示(准确地说是`to_h`)。它包含版本、执行日期/时间、要评估的源代码、选项(在解析了`REXE_OPTIONS`环境变量和命令行之后)，以及 Ruby 代码的执行时间:

```
➜  ~   echo $EUR_RATES_JSON | rexe -gy -ij -mb -oa -n self 
```

```
---
:count: 0
:rexe_version: 1.0.0
:start_time: '2019-04-15T13:12:15+08:00'
:source_code: self
:options:
  :input_filespec:
  :input_format: :json
  :input_mode: :one_big_string
  :loads: []
  :output_format: :awesome_print
  :requires:
  - awesome_print
  - json
  - yaml
  :log_format: :yaml
  :noop: true
:duration_secs: 0.050326 
```

我们为 YAML 格式指定了`-gy`；还有其他格式(参见本文档的帮助输出)，默认格式是`-gn`，这意味着根本不输出日志条目。

您看到的需求没有明确指定，而是自动添加的，因为 Rexe 会添加自动解析和格式化所需的任何需求，我们在命令行选项`-gy -ij -oa`中指定了这些格式。

这个额外的输出被发送到标准错误( *stderr* )而不是标准输出( *stdout* )，这样当 stdout 被传送到另一个命令时，它就不会污染“真实”数据。

如果您想将这个信息输出附加到一个文件中(例如`rexe.log`，您可以这样做:

```
➜  ~   rexe ... -gy 2>>rexe.log 
```

### 输入模式

Rexe 试图让您以不同的方式简单方便地处理标准输入。以下是与输入模式相关的帮助文本:

```
-m, --input_mode MODE      Input preprocessing mode (determines what `self` will be):
                             -ml  line; each line is ingested as a separate string
                             -me  enumerator (each_line on STDIN or File)
                             -mb  big string; all lines combined into one string
                             -mn  none (default); no input preprocessing; 
                                  self is an Object.new 
```

前三种是*滤镜*模式；它们将标准输入作为`self`提供给你的代码。

最后一种(也是默认的)是*执行器*模式。它只是帮助您执行您提供的代码，而不需要对标准输入进行任何特殊的隐式处理。以下是关于这些模式的更多详细信息:

#### -ml "线"过滤模式

在这种模式下，每行输入都会调用一次代码，在每次调用中，`self`会计算每行文本:

```
➜  ~   echo "hello\ngoodbye" | rexe -ml puts reverse
olleh
eybdoog 
```

`reverse`在标准输入的每一行被隐式调用。`self`是每个调用中的输入线路(我们也可以使用`self.reverse`，但是`self.`是多余的)。

要知道，在这种模式下，如果你使用的是自动输出模式(除了默认的`-on`无输出模式之外的任何模式)，虽然你可以控制输出记录的*内容*，但是没有办法选择性的*排除*记录被输出。即使代码的结果是 nil 或空字符串，也会输出一个换行符。要防止这种情况，您可以执行下列操作之一:

*   改为使用`-me`枚举器模式，调用`select`、`filter`、`reject`等。
*   使用(默认)`-on` *无输出*模式，并为您*想要的输出*显式调用`puts`

#### -me“枚举器”过滤模式

在这种模式下，你的代码只被调用一次，而`self`是一个分配所有标准输入行的枚举器。更准确地说，它是由`each_line`方法在`$stdin`或输入文件上返回的枚举数，视情况而定。

作为一个枚举器处理输入使您能够使用丰富的`Enumerable`方法，如`select`、`to_a`、`map`等。

下面是一个使用`-me`向目录列表中的前 3 个文件添加行号的例子:

```
➜  ~   ls / | rexe -me "first(3).each_with_index { |ln,i| puts '%5d  %s' % [i, ln] }"

    0  AndroidStudioProjects
    1  Applications
    2  Desktop 
```

由于`self`是可枚举的，我们可以对它调用`first`。我们使用了默认的输出模式`-on` ( *no output* mode)，它说不做任何自动输出，只输出由源代码中的`puts`明确指定的输出。

#### -mb“大字符串”过滤模式

在这种模式下，所有标准输入被组合成一个单独的(可能很大，也可能是多行的)字符串。

当您需要解析一个对象的多行 JSON 或 YAML 表示时，这是一个很好的例子；您需要将所有标准输入传递给 parse 方法。这是本文第一个 rexe 示例中使用的模式。

#### -mn【无输入】执行器模式-默认

在这种模式下，根本不需要对标准输入进行特殊处理；如果你想要标准输入，你需要自己编码(例如用`STDIN.read`)。

`self`评估为`Object`的新实例，如果您定义了方法、常量、实例变量等，将会使用它。，在您的代码中。

#### 过滤输入模式记忆注意事项

如果您正在使用其中一种过滤模式，并且输入的内容可能会超过内存容量，您可以执行以下操作之一:

*   使用`-ml`(线)模式，这样你一次只能输入一条线
*   使用枚举器，要么 a)指定`-me`(枚举器)模式选项，要么 b)结合使用`-mn`(无输入)模式和类似`STDIN.each_line`的东西。然后:
    *   确保不要调用任何会产生所有输入数组的方法(如`map`、`select`)，因为这样会将所有记录提取到内存中，或者:
    *   使用[懒惰枚举器](https://www.honeybadger.io/blog/using-lazy-enumerators-to-work-with-large-files-in-ruby/)

### 输入格式

如果您愿意，Rexe 可以用几种格式中的任何一种来解析您的输入。您可以在*输入格式* ( `-i`)选项中请求这一点。合法值包括:

*   `-ij`JSON
*   `-im` -元帅
*   `-in` - [无](https://dev.todefault)
*   `-iy`亚 ML

除了将文本原封不动地传递给代码的`-in`，您的输入将被解析为指定的格式，结果对象作为`self`传递给代码。

如果输入*模式*为`-mn`(默认为“无输入”执行模式)，则输入格式选项被忽略，因为在该模式下没有标准输入的预处理。

### 输出格式

为了方便起见，提供了几种输出格式:

*   `-oa` - Awesome Print -在对象上调用`.ai`来获得`ap`将要打印的字符串
*   `-oi` -检查-在对象上调用`inspect`
*   `-oj` - JSON -在对象上调用`to_json`
*   `-oJ` - Pretty JSON 用对象调用`JSON.pretty_generate`
*   `-on` -(默认)无输出-输出被抑制
*   `-op` - Puts -产生`puts`将输出的内容
*   `-os` -对对象进行字符串调用`to_s`
*   `-oy` - YAML -在对象上调用`to_yaml`

所有格式都将隐式地`require`完成其任务所需的任何内容(例如`require 'yaml'`)。

默认设置是`-on`不产生任何输出(除非明确编码这样做)。如果您喜欢不同的缺省值，如*的*模式的`-op`，您可以在您的`REXE_OPTIONS`环境变量中指定。

如果提供了两个字母，第一个字母将用于 tty 设备(例如，未被重定向或管道化时的终端)，第二个字母用于块设备(例如，当被重定向或管道化到另一个进程时)。

您可能想知道为什么提供这些格式，因为它们的功能可以包含在定制代码中。原因如下:

*   命令行长度的节省大大提高了这些命令的可读性和可行性。
*   转换格式要简单得多，因为不需要改变代码本身。
*   这种方法支持输出格式的参数化。

### 从文件中读取输入

Rexe 还简化了从文件而不是标准输入中获取输入。`-f`选项获取一个 filespec，并对其内容进行与标准输入完全相同的处理。这就缩短了:

```
➜  ~   cat filename.ext | rexe ... 
```

...到...

```
➜  ~   rexe -f filename.ext ... 
```

如果你正在使用扩展名为`.yml`、`.yaml`或`.json`(不区分大小写)的文件，这将变得更加有用。在这种情况下，将自动为您设置输入格式和模式，以便:

*   `-iy` (YAML)或`-ij` (JSON)，视文件扩展名而定
*   `-mb`(一个大字符串模式)，假设最常见的用例是一次解析整个文件

所以我们上面举的例子:

```
➜  ~   export EUR_RATES_JSON=`curl https://api.exchangeratesapi.io/latest`
➜  ~   echo $EUR_RATES_JSON | rexe -mb -ij -oy self 
```

...可改为:

```
➜  ~   curl https://api.exchangeratesapi.io/latest > eur_rates.json
➜  ~   rexe -f eur_rates.json -oy self 
```

使用`-f`的另一个可能的好处是，因为它是命令行选项，所以可以在`REXE_OPTIONS`中指定。如果您在同一个文件上执行许多操作，这可能会很有用。

如果您需要覆盖为文件输入自动配置的输入模式和格式，您可以简单地在命令行中的`-f`:
后指定所需的选项

```
➜  ~   rexe -f eur_rates.json -mb -in 'puts self.class, self[0..20]'
String
{"base":"EUR","rates" 
```

### ‘self’作为默认源代码

为了使 rexe 更加简洁，当您希望源代码是`self`时，您不需要指定任何源代码。简单的格式转换就是这种情况，如上面提到的 JSON 到 YAML 的转换:

```
➜ ~  rexe -f eur_rates.json -oy
# or
➜ ~  echo $EUR_RATES_JSON | rexe -mb -ij -oy 
```

```
---
rates:
  JPY: 126.63
  BRL: 4.3012
  NOK: 9.6915
  ... 
```

这个特性可能只在过滤器模式下有用，因为在执行器模式下(`-mn` ) self 是`Object`的一个新实例，作为输出值几乎没有用处。

### $ RC 全局 OpenStruct

为了方便起见，通过访问包含 OpenStruct 的`$RC`全局变量，您的代码可以在运行时使用详细模式下显示的信息。让我们用 YAML:
打印出它的内容

```
➜  ~   rexe -oy '$RC' 
```

```
--- !ruby/object:OpenStruct
table:
  :count: 0
  :rexe_version: 1.0.0
  :start_time: '2019-04-15T13:25:56+08:00'
  :source_code: "$RC"
  :options:
    :input_filespec:
    :input_format: :none
    :input_mode: :none
    :loads: []
    :output_format: :yaml
    :requires:
    - yaml
    :log_format: :none
    :noop: false
modifiable: true 
```

运行时对象中最有用的可能是记录计数，可以通过`$RC.count`和`$RC.i`访问。这只有在线路模式下才真正有用，因为在其他模式下它总是 0 或 1。这里有一个例子，你可以用它作为一种进步指标:

```
➜  ~   find / | rexe -ml -on \
'if $RC.i % 1000 == 0; puts %Q{File entry ##{$RC.i} is #{self}}; end' 
```

```
...
File entry #106000 is /usr/local/Cellar/go/1.11.5/libexec/src/cmd/vendor/github.com/google/pprof/internal/driver/driver_test.go
File entry #107000 is /usr/local/Cellar/go/1.11.5/libexec/src/go/types/testdata/cycles1.src
File entry #108000 is /usr/local/Cellar/go/1.11.5/libexec/src/runtime/os_linux_novdso.go
... 
```

注意，这里的 Ruby 代码使用了单引号；如果使用双引号，`$RC`将被 shell 解释并删除。

### 实现领域特定语言(DSL)

在加载的文件中定义方法使您能够有效地定义一个供命令行使用的 [DSL](https://en.wikipedia.org/wiki/Domain-specific_language) 。您可以为不同的项目、域或上下文使用不同的加载文件，并定义别名或单行脚本来赋予它们有意义的名称。例如，如果您在`~/projects/ansible-tools/rexe-ansible.rb`中有一个可转换的助手代码，您可以在启动脚本中定义一个别名:

```
➜  ~   alias rxans="rexe -l ~/projects/ansible-tools/rexe-ansible.rb $*" 
```

...然后通过调用`rxans`，您将有一个可供我使用的 Ansible DSL。

此外，因为您还可以在任何对象的上下文中调用`pry`，所以您可以轻松地在 [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) (shell)中提供 DSL。为了举例说明，下面是如何在 File 类上打开一个 REPL:

```
➜  ~   ruby -r pry -e File.pry
# or
➜  ~   rexe -r pry File.pry 
```

`self`将评估为`File`类，所以你可以只使用它们的名字来调用类方法:

```
➜  ~   rexe -r pry File.pry 
```

```
[6] pry(File)> size '/etc/passwd'
6804
[7] pry(File)> directory? '.'
true
[8] pry(File)> file?('/etc/passwd')
true 
```

如果您在一个定制对象上调用`pry`，这个定制对象有特别适合您的任务的方法:
，这将非常方便

```
➜  ~   rexe -r wifi-wand,pry  WifiWand::MacOsModel.new.pry 
```

```
[1] pry(#<WifiWand::MacOsModel>)> random_mac_address
"a1:ea:69:d9:ca:05"
[2] pry(#<WifiWand::MacOsModel>)> connected_network_name
"My WiFi" 
```

Ruby 非常适合 DSL，因为它不需要括号来调用方法，所以对定制方法*的调用看起来*像是内置的语言命令和关键字。

### Ruby 代码中的引号和引用字符串

使用像 rexe 这样在命令行上指定 Ruby 代码的工具的一个复杂之处是，您需要小心 shell 对某些字符的特殊处理。为此，经常需要引用 Ruby 代码。您可以使用单引号或双引号让 shell 将您的源代码视为单个参数。StackOverflow 上有一个很好的参考资料，可以说明它们之间的区别，网址是 https://stack overflow . com/questions/6697753/difference-between-single-and-double-quotes-in-bash。

就个人而言，我发现单引号更有用，因为我通常不希望 shell 处理我的 Ruby 代码中的特殊字符，如`$`。

有时候无所谓:

```
➜  ~   rexe 'puts "hello"'
hello
➜  ~   rexe "puts 'hello'"
hello 
```

我们也可以使用`%q`或`%Q`，有时这完全消除了对外部引号的需要:

```
➜  ~   rexe puts %q{hello}
hello
➜  ~   rexe puts %Q{hello}
hello 
```

有时，需要根据内部需要哪些引号来选择外部使用的引号(在 shell 中引用您的命令)。例如，在下面的命令中，我们需要在 Ruby 中使用双引号来进行插值，所以我们在外面使用单引号:

```
➜  ~   rexe puts '"The time is now #{Time.now}"' 
```

```
The time is now 2019-03-29 16:41:26 +0800 
```

在这种情况下，我们还需要在外面使用单引号，因为我们需要在一个`%Q{}`表达式中使用双引号:

```
➜  ~   rexe 'puts %Q{The operating system name is "#{`uname`.chomp}".}' 
```

```
The operating system name is "Darwin". 
```

我们可以使用`%Q{}` :
来消除 Ruby 代码中任何引号的需要

```
➜  ~   rexe puts '%Q{The time is now #{Time.now}}' 
```

```
The time is now 2019-03-29 17:06:13 +0800 
```

当然，你可以用反斜杠来转义引号，但是这可能更难阅读。

### 无 Op 模式

`-n`无操作模式将导致指定的源代码*而不是*被执行。如果您正在构建一个 rexe 命令，并且希望在执行 Ruby 代码之前检查配置选项，那么这有时与`-g`(日志记录)选项结合使用会很方便。

### 模仿方法参数

您可能希望在 rexe 命令中支持参数。这有点混乱，但是您可以通过将参数作为 rexe 的 stdin 输入来实现。

前面的一个例子下载了货币兑换率。为了准备一个如何做到这一点的例子，让我们找出可用的货币代码:

```
➜  /   echo $EUR_RATES_JSON | \
rexe -ij -mb -op "self['rates'].keys.sort.join(' ')" 
```

```
AUD BGN BRL CAD CHF CNY CZK DKK GBP HKD HRK HUF IDR ILS INR ISK JPY KRW MXN MYR NOK NZD PHP PLN RON RUB SEK SGD THB TRY USD ZAR 
```

代码输出是合法的参数，可以作为下面命令中的参数发送到 rexe 的 stdin。让我们找出欧元对菲律宾比索对 T2 的汇率

```
➜  ~   echo PHP | rexe -ml -op -rjson \
        "rate = JSON.parse(ENV['EUR_RATES_JSON'])['rates'][self];\ %Q{1 EUR = #{rate} #{self}}"

1 EUR = 58.986 PHP 
```

在这个代码中，`self`是货币代码`PHP`(菲律宾比索)。我们已经从先前填充的环境变量中访问了要解析的 JSON 文本。

因为我们为`PHP`参数“用完”了 stdin，所以我们需要从环境变量中显式读取 JSON 数据，这使得命令更加复杂。常规的 Ruby 脚本可以更好地处理这个问题。

### 使用剪贴板进行文本处理

对于在编辑器中编辑文本，rexe 可以用于文本转换，否则需要手动完成。

系统的剪贴板粘贴和复制命令可以处理编辑器和 rexe 之间的文本移动。在 Mac 上，我们有以下命令:

*   `pbcopy` -将其 stdin *的内容复制到*剪贴板
*   `pbpaste` -将内容*从*剪贴板复制到它的标准输出

假设我们在屏幕上显示了以下货币代码(为简洁起见，数据被省略):

```
AUD BGN BRL PHP TRY USD ZAR 
```

...我们希望将它们转换成 Ruby 符号，作为 hash 中的键包含在 Ruby 源代码中，hash 的值将是货币的显示名称，例如“澳元”)。

我们可以手动选择该文本，并使用系统菜单命令或按键将其复制到剪贴板，或者我们可以这样做:

```
➜  ~   echo AUD BGN BRL PHP TRY USD ZAR | pbcopy 
```

将这一行复制到剪贴板后，我们可以运行:

```
➜  ~   pbpaste | rexe -ml -op \
        "split.map(&:downcase).map { |s| %Q{    #{s}: '',} }.join(%Q{\n})"
    aud: '',
    bgn: '',
    brl: '',
    # ... 
```

如果我将`| pbcopy`添加到 rexe 命令中，那么输出文本将被复制到剪贴板中，而不是显示在终端中，然后我可以将它粘贴到我的编辑器中。

在手动操作中使用剪贴板很方便，但是在自动化脚本中使用它是一个非常糟糕的主意，因为每个用户会话只有一个剪贴板。如果您在自动化脚本中使用剪贴板，那么如果它的内容被另一个进程更改，您就有出错的风险，或者相反，当您更改剪贴板的内容时，您可能会搞乱另一个进程。

### 多行红宝石命令

虽然 rexe 使用简短的一行代码是最简洁的，但是您也可以使用它在 shell 脚本中包含重要的 Ruby 代码。如果这样做，您可能需要在 Ruby 代码行中添加尾随反斜杠。

可能不太明显的是，您经常需要使用分号作为语句分隔符。比如下面这个不带分号的例子:

```
➜  ~   cowsay hello | rexe -me "print %Q{\u001b[33m} \ puts to_a" 
```

```
rexe: (eval):1: syntax error, unexpected tIDENTIFIER, expecting '}'
...new { print %Q{\u001b[33m} puts to_a }
...                           ^~~~ 
```

shell 将所有反斜杠结束的行合并成一行文本，所以当 Ruby 解释器看到你的代码时，它们都在一行:

```
➜  ~   cowsay hello | rexe -me "print %Q{\u001b[33m} puts to_a" 
```

添加分号解决了这个问题:

```
➜  ~   cowsay hello | rexe -me "print %Q{\u001b[33m}; \ puts to_a" 
```

```
 _______
< hello >
 -------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     || 
```

### 清除需求和负载列表

有时，您可能已经在命令行上或在`REXE_OPTIONS`环境变量中指定了 load 或 require，但是您想在一次调用中覆盖它。以下是您的选择:

1)分别用`-r!`和`-l!`命令行选项取消指定*所有*需求或加载。

2)不明确的个人要求或通过在名称前加上`-`来加载，例如`-r -rails`。使用数组减法，数组减法去掉被减(减数)数组中每个元素的*所有*次出现，所以:

```
➜  ~   rexe -n -r rails,rails,rails,-rails -gP
...
   :requires=>["pp"],
... 
```

...会显示最终的`-rails`取消了之前所有的`rails`规格。

我们也可以使用`$RC`(如上所述)通过这样做以编程方式提取需求列表:

```
➜  ~   rexe -oP -r rails,rails,rails,-rails '$RC[:options][:requires]'
["pp"] 
```

### 清算*所有*选项

您也可以使用*清除选项*选项(`-c`)清除*到某个时间点指定的所有*选项。如果您已经在`REXE_OPTIONS`环境变量中指定了选项，并且想要忽略所有选项，那么这将非常有用。

### 逗号分隔要求和负载

为了与`ruby`解释器保持一致，rexe 支持带有`-r`选项的 requires，但也允许使用逗号将它们组合在一起:

```
 vvvvvvvvvvvvvvvvvvvvv
➜  ~   echo $EUR_RATES_JSON | rexe -r json,awesome_print 'ap JSON.parse(STDIN.read)'
                                    ^^^^^^^^^^^^^^^^^^^^^ 
```

用`-l`选项加载的文件以同样的方式处理。

### 谨防组态要求

所有对 rexe 的调用都需要 gem 和模块，这会让你的命令更简单、更简洁，但是如果不需要的话，会浪费执行时间。您可以检查执行时间，看看消耗了多少时间。例如，通过观察和比较有和没有 require 的执行时间，我们可以发现 rails 在一个系统上的加载时间大约为 0.63 秒(输出用`grep`缩写):

```
➜  ~   rexe -gy -r rails 2>&1 | grep duration
:duration_secs: 0.660138
➜  ~   rexe -gy          2>&1 | grep duration
:duration_secs: 0.027781 
```

(要使以上工作，需要安装`rails` gem 及其依赖项。)

### 操作系统支持

Rexe 已经在 Mac OS、Linux 和 Windows Subsystem for Linux (WSL)上测试成功。它旨在作为 Unix shell 的一个工具，因此，没有试图支持 Windows 非 Unix shell。

### 更多例子

这里有更多的例子来说明 rexe 的用法。

* * *

#### 使用 Rexe 作为简单的计算器

要将结果输出到 stdout，可以调用`puts`或者指定`-op`选项:

```
➜  ~   rexe puts 1 / 3.0
0.3333333333333333 
```

或者:

```
➜  ~   rexe -op 1 / 3.0
0.3333333333333333 
```

由于`*`是 shell 解释的，所以如果做乘法，就需要引用表达式:

```
➜  ~   rexe -op '2 * 7'
14 
```

当然，如果将`-op`放在`REXE_OPTIONS`环境变量中，就不需要明确输出:

```
➜  ~   export REXE_OPTIONS=-op
➜  ~   rexe '2 * 7'
14 
```

* * *

#### 输出包络

使用 AwesomePrint 输出`ENV`的内容【参见脚注^4 关于 env . to _ s】:

```
➜  ~   rexe -oa ENV 
```

```
{
...
  "LANG" => "en_US.UTF-8",
   "PWD" => "/Users/kbennett/work/rexe",
 "SHELL" => "/bin/zsh",
...
} 
```

* * *

#### 重新格式化命令的输出

显示 Mac 主硬盘的主分区上已用/可用的磁盘空间:

```
➜  ~   df -h | grep disk1s1 | rexe -ml \
"x = split; puts %Q{#{x[4]} Used: #{x[2]}, Avail: #{x[3]}}"
91% Used: 412Gi, Avail: 44Gi 
```

(注意，`split`相当于`self.split`，因为使用了`-ml`选项，`self`是文本的行。

* * *

#### 格式为数值排序

显示当前目录中最长的 3 个文件名及其长度，降序排列:

```
➜  ~   ls  | rexe -ml -op "%Q{[%4d] %s} % [length, self]" | sort -r | head -3
[  50] Agoda_Booking_ID_9999999 49_–_RECEIPT_enclosed.pdf
[  40] 679a5c034994544aab4635ecbd50ab73-big.jpg
[  28] 2018-abc-2019-01-16-2340.zip 
```

当您使用 printf 格式右对齐数字时，按字母顺序对行进行排序也会导致按数字顺序进行排序。

* * *

#### 打印黄色(相信我！):

这使用了一个 [ANSI 转义码](https://en.wikipedia.org/wiki/ANSI_escape_code)以黄色向终端输出文本:

```
➜  ~   cowsay hello | rexe -me "print %Q{\u001b[33m}; puts to_a"
➜  ~     # or
➜  ~   cowsay hello | rexe -mb "print %Q{\u001b[33m}; puts self"
➜  ~     # or
➜  ~   cowsay hello | rexe "print %Q{\u001b[33m}; puts STDIN.read" 
```

```
 _______
 < hello >
  -------
         \   ^__^
          \  (oo)\_______
             (__)\       )\/\
                 ||----w |
                 ||     ||` 
```

* * *

#### 更多 YouTube:区分成功与失败

让我们进一步看一下“加载文件”一节中的 YouTube 例子。让我们为命令的成功或失败加载不同的视频。

如果我们把这个放到一个加载文件中(比如~/。rexerc):

```
def play(piece_code)
  pieces = {
    hallelujah: "https://www.youtube.com/watch?v=IUZEtVbJT5c&t=0m20s",
    rick_roll: "https://www.youtube.com/watch?v=dQw4w9WgXcQ&t=0m43s",
    valkyries: "http://www.youtube.com/watch?v=P73Z6291Pt8&t=0m28s",
    wm_tell: "https://www.youtube.com/watch?v=j3T8-aeOrbg",
  }
  `open #{Shellwords.escape(pieces.fetch(piece_code))}`
end

def play_result(success)
  play(success ? :hallelujah : :rick_roll)
end

# Must pipe the exit code into this Ruby process, 
# e.g. using `echo $? | rexe play_result_by_exit_code`
def play_result_by_exit_code
  play_result(STDIN.read.chomp == '0')
end 
```

然后当我们发出一个成功的命令时，哈利路亚合唱团就开始演奏[见脚注^2]:]

```
➜  ~   uname; echo $? | rexe play_result_by_exit_code 
```

...但是当命令失败时，在这种情况下，由于找不到可执行文件，它会播放里克·阿斯特利的“永不放弃你”:

```
➜  ~   uuuuu; echo $? | rexe play_result_by_exit_code 
```

* * *

#### 重新格式化帮助文本的源代码

另一个格式化示例...我想重新格式化这个源代码...

```
 'i' => Inspect
         'j' => JSON
         'J' => Pretty JSON
         'n' => No Output
         'p' => Puts (default)
         's' => to_s
         'y' => YAML 
```

...变成更适合我的帮助文本。不可否认，用 rexe 做这件事花费的时间可能超过了手工做这件事的时间，但是这是一个有趣的练习，并且可以很容易地尝试不同的格式。在这里，复制原文到剪贴板后:

```
➜  ~   pbpaste | rexe -ml -op "sub(%q{'}, '-o').sub(%q{' =>}, %q{ })"
         -oi  Inspect
         -oj  JSON
         -oJ  Pretty JSON
         -on  No Output
         -op  Puts (default)
         -os  to_s
         -oy  YAML 
```

* * *

#### 货币换算

我经常旅行，当我第一次访问一个国家时，我经常对汇率感到困惑。我把这个放在我的`~/.rexerc` :

```
# Conversion rate to US Dollars
module Curr
  module_function
  def myr;      4.08  end  # Malaysian Ringits
  def thb;     31.72  end  # Thai Baht
  def usd;      1.00  end  # US Dollars
  def vnd;  23199.50  end  # Vietnamese Dong
end 
```

如果我足够幸运，当我需要做一个转换时，在我的电脑前，例如，找出 150 马来西亚林吉特的美元值，我可以这样做:

```
➜  rexe git:(master) ✗   rexe puts 150 / Curr.myr
36.76470588235294 
```

显然，利率会随着时间的推移而变化，但这会给我一个大致的概念，这通常是我所需要的。

* * *

#### 重新格式化 Grep 输出

最近有人要求我为我的`rock_books`会计宝石中的数据提供一个模式。`rock_books`数据的大小非常小，不使用数据库。相反，每次运行时都会解析输入数据，并按需生成报告。但是，运行时内存中有数据结构(实际上是类实例)，它们的类继承自`Struct`。定义行看起来像这样:

```
class JournalEntry < Struct.new(:date, :acct_amounts, :doc_short_name, :description, :receipts) 
```

`grep`命令行实用程序在这些匹配项前添加一个字符串，如下所示:

```
lib/rock_books/documents/journal_entry.rb: 
```

所以这是对我很有效的方法:

```
➜  ~   grep Struct **/*.rb | grep -v OpenStruct | rexe -ml -op \
"a = \ gsub('lib/rock_books/', '') \ .gsub('< Struct.new',    '') \ .gsub('; end',           '') \ .split('.rb:') \ .map(&:strip); \  \ %q{%-40s %-s} % [a[0] + %q{.rb}, a[1]]" 
```

...哪个产生了这个输出:

```
cmd_line/command_line_interface.rb       class Command (:min_string, :max_string, :action)
documents/book_set.rb                    class BookSet (:run_options, :chart_of_accounts, :journals)
documents/journal.rb                     class Entry (:date, :amount, :acct_amounts, :description)
documents/journal_entry.rb               class JournalEntry (:date, :acct_amounts, :doc_short_name, :description, :receipts)
documents/journal_entry_builder.rb       class JournalEntryBuilder (:journal_entry_context)
reports/report_context.rb                class ReportContext (:chart_of_accounts, :journals, :page_width)
types/account.rb                         class Account (:code, :type, :name)
types/account_type.rb                    class AccountType (:symbol, :singular_name, :plural_name)
types/acct_amount.rb                     class AcctAmount (:date, :code, :amount, :journal_entry_context)
types/journal_entry_context.rb           class JournalEntryContext (:journal, :linenum, :line) 
```

虽然这段代码中有很多内容，但是垂直和水平的对齐方式以及间距使得这段代码很容易理解。它是这样做的:

*   grep`"Struct"`的代码库
*   用`grep -v`排除对`"OpenStruct"`的引用
*   用`gsub`删除不需要的文本
*   将这一行分成 1)相对于`lib/rockbooks`的 filespec，和 2)类定义
*   去掉不需要的空间，因为这会打乱输出的水平对齐。
*   使用 C 样式的 printf 格式将文本对齐成两列

* * *

### 结论

Rexe 不是革命性的技术，它只是从命令行中删除解析、格式化和低级配置的管道，以便您可以专注于手头的高级任务。

当我们考虑一个新的软件时，我们通常会想“这会对现在有什么帮助？”。然而，对我来说，rexe 的强大之处不在于我现在可以用它在一个用例中做什么，而是随着时间的推移，随着我积累更多的经验和专业知识，我将能够做什么。

我建议开始使用 rexe，即使是对工作流的适度改进，即使它看起来并不令人信服。随着时间的推移，当您使用它时，很有可能会有新的想法出现在您的脑海中，工作流程的改进也会成倍增加。

不过需要注意的是——跨系统共享 rexe 脚本的复杂性和难度与您使用环境变量和加载文件进行配置和共享代码的程度成正比。负责并遵守规则，使配置和代码尽可能的干净和有组织。

* * *

#### 脚注

[1]: Rexe 是在 https://github.com/thisredone/rb对最少但很优秀的`rb`剧本的修饰。我开始使用`rb`,并想到了许多我想拥有的其他特性，所以我开始开发 rexe。

[2]:当此页面在您的浏览器中打开时，可能不会自动播放。您可能需要更改默认浏览器，或者更改打开 URL 的代码。火狐新(截至 2019 年 3 月)66 版抑制自动播放；你可以注册这个策略的例外:打开 Firefox 偏好设置，搜索“自动播放”，添加“[https://www.youtube.com](https://www.youtube.com)”。

[3]:让这款手机真正支持操作系统要比表面上看起来复杂得多。在 Linux 上，默认情况下可能不会安装`xdg-open`。此外，开箱即用的 Windows Linux 子系统(WSL)不能启动图形应用程序。

这里是一个*开始*在一个方法中，打开一个跨操作系统可移植的资源:

```
 def open_resource(resource_identifier)
    command = case (`uname`.chomp)
    when 'Darwin'
      'open'
    when 'Linux'
      'xdg-open'
    else
      'start'
    end

    `#{command}  #{resource_identifier}`
  end 
```

[4]:Ruby 语言的一个有趣的特点是`ENV.to_s`返回`"ENV"`而不是`ENV`对象的内容。因此，许多其他输出格式也会返回某种形式的`"ENV"`。您可以通过指定`ENV.to_h`来处理这个问题。
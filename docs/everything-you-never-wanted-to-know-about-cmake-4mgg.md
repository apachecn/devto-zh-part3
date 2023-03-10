# 关于 CMake 你不想知道的一切

> 原文：<https://dev.to/slurpsmadrips/everything-you-never-wanted-to-know-about-cmake-4mgg>

这篇文章最初发表在我的个人网站上。它已被重新张贴在这里的可见性。

仅仅听到 CMake 这个词就足以让我脊背发凉。最近几周，当我完成一个名为 [IXM](https://github.com/ixm-one/ixm) 的 CMake 库时，我一直在深入研究它的行为和工具。虽然 IXM 如何工作的各种细节，我为什么写它，以及所有漂亮的小用法细节肯定是另一个帖子，但快速总结是，它抽象出了 CMake 用户的大多数常见需求，从而允许您编写*甚至更少的* CMake(我想我们都同意这是一件好事)。在 YOSPOS 子论坛上写了一篇关于我最近几周了解到的关于 CMake 的所有恶心的事情的小帖子后，我决定写一篇更深入的描述。事不宜迟，让我们开始教你，普通用户，关于 CMake 你从来不想知道的一切。

## 快速介绍

与其解释 CMake 是什么，它做什么，它是如何工作的，我将快速描述 CMake 在整个构建过程中采取的各个步骤。实际上，CMake 的工作流程如下
:

*   配置
*   产生
*   建设

在这些步骤中，我们可以执行以下操作:

*   配置
    *   复制文件
    *   执行流程
    *   读取/写入文件
    *   检查主机系统状态
    *   创建、导入和修改构建目标
*   产生
    *   写入文件
    *   运行生成器表达式
    *   ...大概就是这样
*   建设
    *   后期生成命令
    *   预构建命令
    *   执行流程
    *   生成文件供以后的构建阶段使用，但是只有在 CMake 能够证明它可以通过 DAG 使用这些文件的情况下，*才会生成文件。*

对 CMake 的许多批评之一是，什么命令将在什么阶段运行并不明显。一些命令执行，然后创建在`generate`步骤执行的
步骤，另一些命令在`configure`和
步骤期间运行，还有一些命令最终将在`build`期间执行。

IXM 本身主要关心配置和生成步骤。因为我们无法向用户指定在什么时间执行哪个阶段，所以我们试图将`generate`操作隐藏在`configure`步骤命令调用之后。这意味着当我们依赖用户在配置阶段执行工作时，他们要做的工作更少，因为我们只需设置*生成器表达式*稍后在后台执行。这样做的好处是，我们可以在生成阶段确定我们的 DAG 是否实际上是安全和正确的，而不仅仅是在配置阶段“希望最好的”。

## 诅咒和自定义变量

CMake 中的变量只不过是被诅咒的邪恶恐怖，它们伺机恐吓绝对的 T2，让任何不期待它的人抓狂。幸运的是，我喝很多咖啡，也吃止痛片，所以这对我来说并不是什么*新的事情。*

从 CMake 3.0 开始，CMake 处理变量的方式发生了变化。实际上，“未加引号”的参数可以是除空格或`(`、`)`、`#`、`\`、`"`或`>`之外的任何字符。是的，这意味着 CMake 变量可以包含表情符号！对于现代编程语言来说,*怎么样？* 

```
# In awe at the byte size of this lad.
# What an absolute code unit.
set(🙃 "Why would you do this?") 
```

但是有一个警告。当显式取消对变量*的引用*(即`${🙃}`)时，必须对任何非字母数字字符或字符`_`、`+`、`-`、`.`和`/`进行转义。只不过，你要转义的不是字符，而是*字节本身*！因此，我们永远也不可能实际上取消引用存储在🙃，除非 CMake 替我们做。这是通过`if()`、`elseif()`、`else()`、`while()`和`foreach(IN LISTS)`命令完成的。

此外，因为`function()`和`macro()`可以带一个*无引号的参数*，这意味着我们*也可以*用*字面上的任何东西*来命名函数和宏。在这种情况下，困难的部分是我们如何调用一个命令。在这种情况下，唯一有效的字符是字母数字和字符`_`。为什么 CMake 会让我们创建不能被调用的函数？我怎么知道！🤣

这就把我们带到了关于 CMake 中变量的最后一点信息。您可以使用一点小魔法，创建自己的变量名称空间。因此，CMake 的当前变量集存在于下面的解引用“空格”中。还有`$`，这是默认的查找规则。此外，还有`$CACHE`和`$ENV`。这两者分别研究了`CMakeCache.txt`文件和系统环境变量。这种变量解引用的风格已经传播到了 CMake 的其他部分。最明显的模块是`ExternalData`，它提供了特殊的`DATA{}`变量引用。

在 IXM 的例子中，我们更进一步，创建了一个自定义语法，允许通过各种“提供者”变量引用获取内容。在这种语法中，可以像其他构建系统/包管理器组合一样指定包。举个例子，要获得非常流行的 Catch2 C++库，可以通过`HUB{catchorg/catch2@v2.6.0}`指定它。然后这个名称可以被传递，它最终将被用来构造`FetchContent`模块的参数。是的，这很痛苦，很可怕，我不会告诉你怎么做，因为这涉及到滥用 CMake 的 regex 引擎，临时字符串替换，以及自 Icarus 坠楼身亡前所未见的傲慢。

## 基本预测

CMake 当前的内置数据类型是字符串。然而，隐藏在这个字符串数据类型后面的是一个实际的类型系统。那么，CMake 如何知道什么是库，什么是源文件呢？然而，它缺少的一个主要东西是一个基本的键值系统。事实证明，我们可以滥用当前的库类型系统来创建我们自己的字典类型。方法是简单地创建一个`INTERFACE IMPORTED`库。然后，我们简单地添加一些函数，自动将`INTERFACE_`添加到任何传入的键中。因为接口目标是导入的，任何可能*依赖于这个库的东西伪装成一个字典将不会要求在安装步骤中导出目标。因此，我们*可以通过`$<TARGET_PROPERTY>`生成器表达式*获得属性，但是由我们来确保`INTERFACE_`部分被预先考虑。我很想看到一个替代方案，但是好吧。*

这种方法的唯一缺点是，由于 CMake 中的作用域规则，字典是伪全局的。换句话说，它们可以从创建它们的目录和任何子目录中用于 CMake 脚本。遗憾的是，它们不能局限于函数作用域。也许这在将来会改变，我们会得到一个真正的诚实的字典类型，但是不要屏住呼吸。我宁愿看到 CMake 语言完全消失，也不愿得到一种字典类型。🙂

## 不当属性

还记得不久前我们谈到的“有效”字符串和 UTF-8 吗？好吧，我撒谎了。因为结果是你的系统只有 10 个字节.这意味着我们可以使*无效* UTF-8 字符序列。一个永远不会存在于有效的 UTF-8 序列中的值是`C0`字节。好吧，碰巧的是，我们可以把它放在一个地方。

```
string(ASCII 192 C0)
set_property(TARGET ${target} PROPERTY INTERFACE_${C0} value) 
```

看哪！CMake 给了我们拥有无效属性的能力，而且它甚至都不会给出一个*狗屎*。

巧合的是，上面的属性名用于跟踪通过 IXM 接口添加到`dict()`的键。当我们将字典类型序列化到磁盘时，这就起作用了，因为知道要保存的确切键会很方便。这一点尤其正确，因为我们希望序列化它们，但不包括它们的`INTERFACE_`前置字符串。

此外，在 CMake 中有一种奇怪的处理缓存变量的方法。缓存变量实际上存在于它们自己的作用域中。这就是为什么我们可以用`$CACHE{VAR}`来跳过典型的变量查找。除了通过`set()`设置缓存变量的值，我们还可以通过`set_property`设置它们。毕竟缓存变量有*属性*，其中一个是`VALUE`。我们可以根据需要获取或设置。不需要调用`set(... FORCE)`或
或`-DVAR:TYPE=VALUE`变量。

## 序列化和自定义文件格式

CMake 的“将一切视为字符串”的脚本方法意味着我们有有趣的副作用。具体来说，CMake(在撰写本文时)无法对二进制数据执行 IO。相反，您必须使用预先存在的语言或工具来确保您可以提取二进制数据。坦率地说，这非常令人沮丧。*然而*，我们可以作弊，制作自己的文件格式。如果你还记得的话，ASCII(以及扩展的 Unicode)拥有所谓的 C0 控制码。虽然由于 TCP/IP 的存在，其中许多控制代码，如`SOH` ( *报头开始*)或`STX` ( *文本开始*)已经变得多余，但我们仍然可以使用 4 个特定的控制代码将我们的数据分成层次结构。具体来说，文件分隔符、组分隔符、记录分隔符和单元分隔符控制代码很容易掌握。这意味着我们可以拆分相当大量的数据。

CMake 将所有由`;`分隔的字符串视为一个列表。这意味着拥有列表的列表是困难的。但是有了上面分隔符的魔力，我们只需执行一个`string(REPLACE)`调用。缺点是我们必须在每一层深度至少做一次*，但这已经足够简单了。实际上，编码看起来是这样的* 

```
string(ASCII 31 unit-separator)
string(REPLACE ";" "${unit-separator}" data "${data}")
list(APPEND output "${data}") 
```

当然，反过来就是在从输出中提取时简单地切换`;`和`${unit-separator}`的位置。

IXM 中实际的`dict(SAVE)`函数如下所示

```
function (ixm_dict_save name)
  parse(${ARGN} @ARGS=1 INTO)
  if (NOT INTO)
    error("dict(SAVE) missing 'INTO' parameter")
  endif()
  ixm_dict_noop(${name})
  dict(KEYS ${name} keys)
  string(ASCII 29 group)
  string(ASCII 30 record)
  string(ASCII 31 unit)
  foreach (key IN LISTS keys)
    dict(GET ${name} ${key} value)
    if (value)
      string(REPLACE ";" "${unit}" value "${value}")
      list(APPEND output "${key}${record}${value}")
    endif()
  endforeach()
  list(JOIN output "${group}" output)
  ixm_dict_filepath(INTO "${INTO}")
  string(ASCII 1 SOH)
  string(ASCII 2 STX)
  string(ASCII 3 ETX)
  string(ASCII 25 EM)
  string(ASCII 30 RS)
  string(ASCII 31 US)
  file(WRITE ${INTO} "${SOH}IXM${STX}${RS}version${US}1${ETX}${output}${EM}")
endfunction() 
```

是的，我们也在向文件中写入各种`STX`和`EM`值(甚至
认为*在技术上*这不是它们的本意)，然而这是为了
未来证明这个文件的版本，因为文件的实际布局可能
在未来会改变，特别是因为 IXM 目前*甚至还没有稳定的
alpha 版本。*

这种来自磁带机时代 <sup id="fnref1">[1](#fn1)</sup> 的“流”格式对 CMake 来说很好，因为我们缺乏对字符串的细粒度字节访问。我们不能随便乱跳。我们要么必须依赖于以 CMake 安全格式(regexes)存储的内容，要么在 CMake 语言中一次读取一个字节(不，谢谢！🙅).通过将 CMake 内容视为一个“数据流”,我们可以将整个序列化格式缝合回 CMake 中的一个状态，并在几乎没有问题的情况下将其写回。

目前，IXM 的“数据库”格式看起来有点像下面的
:

```
␁IXM␂␞version␟1␞additional␟key␟value␟pairs␃␜<filename>␝<dict-name>␞key␟value
␟list␞another␟key␟pair␞OPTIONS␟BUILD_TESTING␟OFF[␜<filename>␝<dict-name>...]␙ 
```

上面的文本格式可能有点难以理解，但是让我们从头到尾看一遍。首先，我们使用*报头开始*控制码。这使得多个*数据库文件*可以毫无问题地连接在一起，或者甚至可以一起嵌入到另一个文本文件中。随后是*文本开始*控制码。这用于终止*割台开始*控制代码。然后，我们将*记录分隔符*和*单元分隔符*视为在数据库头中设置键值对的单一深度方式。目前，我们只是设置了日期，但将来可能会存储额外的元数据。

接下来，我们存储一个“文件”。这种表示在技术上是多余的。到目前为止，我们只有一个文件，除非其他文件被写入并被连接。不管怎样，让它向前兼容是很好的。每个*组*由目标的名称分隔，后跟它的键值对。这些分别由*记录*和*单元*分开。如果键的值是单个值，则*单元*分隔符可能不会出现。没有值的键永远不会写入磁盘。没有关键字的实例也不会被写入磁盘。

那么，为什么要这样做呢？这给了我们更多的灵活性。我们可以为昂贵的操作存储以前的运行，而不是污染 CMake 缓存来存储以前的运行(然后有时不得不删除缓存来修复一些损坏的状态)。想要解决`try_compile`的问题并提高`check()`的吞吐量吗？虽然尚未实现，但这种以人们习惯的方式序列化数据的方法允许我们避开一些 CMake 的时代错误。

## 事件和内心举行的噩梦

还记得我在上面说过我们不能调用不符合调用约定的命令吗？抱歉，我又说谎了！这只有一种工作方式，那就是通过事件的方式。是的， *CMake 有事件*。不，它们是*而不是*记录的，并且操作的顺序是易变的，可能会因为一些用户“做了一件你意想不到的事情”而改变。除了*一个*操作。隐藏的不太为人所知的*后配置*步骤。

CMake 有一个非常有趣的命令，通常用于调试。它被称为`variable_watch`，它带有一个变量和一个命令的名称。因为它是一个参数，所以这个命令名可以是一个不带引号的参数。同样类型的无引号参数，允许我们在函数名中使用表情符号或无效的 UTF 8 字节序列。

当 CMake 完成其配置步骤时，`CMAKE_CURRENT_LIST_DIR`变量被设置为空字符串。这意味着，不管出于什么目的，我们都可以执行析构函数。是的。*我们可以强制 CMake 拥有 RAII* 。我们甚至可以检查当前堆栈，看看执行时我们在哪里。这使得以下情况成为可能:

```
function (ixm::exit variable access value current stack)
  #[[Do whatever your heart desires here]]
endfunction()
variable_watch(CMAKE_CURRENT_LIST_DIR ixm::exit) 
```

我应该指出的是，如果你试图打破 CMake，不要每次想开始一个新项目时都像往常一样摧毁你的灵魂，这是非常有用的。

## 文件(生成)

我们列表的最后一个是非常强大的*非常可怕的* `file(GENERATE)`命令。这个命令允许我们将生成表达式提供给 CMake，然后用于在`generate`步骤生成一个文件。实际上，这些是我们可以得到的最接近于*后生成*步骤的模拟。本质上，这允许我们生成任何类型的文件，这些文件可以依赖于在配置步骤中创建的内容。为了让你保持理智，我不打算发布我为获得下面讨论的行为而编写的大量代码。如果你好奇的话，你完全可以仔细阅读这个项目本身。

例如，这就是你如何基于目标为你的 C 或 C++
编译器生成一个响应文件。

```
function (ixm_generate_response_file target)
  parse(${ARGN} @ARGS=? LANGUAGE)
  get_property(rsp TARGET ${target} PROPERTY RESPONSE_FILE)
  if (NOT rsp)
    set(output "${CMAKE_CURRENT_BINARY_DIR}/IXM/${target}.rsp")
    set_target_properties(${target}
    PROPERTIES
      RESPONSE_FILE ${output})
  endif()
  # This function generates the actual generator expressions. They're not
  # shown here for brevity.
  ixm_generate_response_file_expressions(${target})
  string(JOIN "\n" content
    ${Default}
    ${Release}
    ${Debug}
    ${INCLUDE_DIRECTORIES}
    ${COMPILE_DEFINITIONS}
    ${COMPILE_OPTIONS}
    ${COMPILE_FLAGS})

  file(GENERATE
    OUTPUT $<TARGET_PROPERTY:${target},RESPONSE_FILE>
    CONTENT ${content})
endfunction() 
```

从本质上讲，这为您提供了一种方法来获取指定目标的所有标志，而不必手动跟踪所有可能的标志。遗憾的是，我们无法在*目录*或*源文件*级别的范围内获得属性粒度。无论如何，生成响应文件意味着我们可以做一些事情，比如，以跨平台的方式生成预编译头文件。不需要[控制](https://github.com/sakra/cotire)生成 PCH 的方法，我们也不需要添加自定义语言，如 [CMakePCHCompiler](https://github.com/nanoant/CMakePCHCompiler) 所示。更好的是，我们可以使用`file(GENERATE)`到*有条件地*创建 *unity 基于每个目标构建*。如果我们用`add_library(<name> OBJECT)`创建我们的库目标，那么我们已经重新创建了像虚幻这样的游戏引擎中的每目录 unity 构建的能力。把这个和忍者结合起来，你的建造速度会大大提高。

最后，我们还可以用`file(GENERATE)`做一些事情，包括但不限于:

*   为 AppImage、systemd 或 launchd 等服务生成文件，而无需用户离开 CMake 或使用`configure_file`。想要生成一个文件来自动将您的可执行文件转换成 Windows 服务吗？你也可以这样做。
*   编写一个在生成时创建的`CPackConfig.cmake`文件，在所有对`install()`的调用之后不再需要`include(CPack)`，并设置各种全局变量。
*   生成一个`CTestConfig.cmake`文件，或者完全忽略它，这样你就可以有一个像样的单元测试运行程序了。

## 欢迎来到 H*ck

现在，我已经分享了 CMake 中黑暗可怕的秘密，我希望你，读者，能够将静静等待释放的噩梦内在化。也许有一天你会丢失这些信息，但是你永远会知道你曾经知道这些，这是比大多数人更糟糕的命运。不管怎样，在我深入了解 CMake 之后，有一件事是真的:

I͠ K̸no͜w ͝Too̢ ͡Muc͘ḩ

* * *

1.  是的，我知道磁带机仍然存在。冷静点。 [↩](#fnref1)*
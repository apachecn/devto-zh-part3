# 使用自定义命令在 Kakoune 中运行查询

> 原文：<https://dev.to/staab/running-queries-in-kakoune-with-a-custom-command-4741>

[上一次](https://dev.to/staab/using-kakoune-as-a-query-browser-2j72)，我写了关于使用 bash 和 Kakoune 构建一个最小查询浏览器的文章。在其中，我使用 [entr](http://eradman.com/entrproject/) 设置了一个文件监视器，并自动运行我对文件更改的查询。

一种可能更好的方法是注册一个自定义命令来完成这项工作，因为我的查询在生产环境中可能并不总是安全的。至少可以说，Kakoune 关于主题的[文档非常密集，所以让我们一起来看一个例子吧！](https://github.com/mawww/kakoune/blob/master/doc/pages/commands.asciidoc#declaring-new-commands)

# 项目结构

对于这个项目，我们需要两个组件:Kakoune 命令和连接配置。命令应该可以在项目之间重用，而连接应该是特定于项目的。

编写 Kakoune 插件的最佳实践是将它们单独放在一个文件中，在 Kakoune 的[自动加载目录](https://github.com/mawww/kakoune/blob/17c5e7aa5fb020186378f7f738e617b5743839fc/README.asciidoc#231-configuration)中的某个地方。我将把我的插件放到`~/.config/kak/autoload/jstaab/eval-query.kak`中。

另一方面，连接配置将放在我的项目根目录中，在`.eval-query.json`中。现在，我将只使用一个简单的 json 对象来描述传递给 psql 的参数:

```
 "connections":  {  "local":  "-d my_project",  "production":  "-h 192.168.0.1 -d my_project"  } 
```

Enter fullscreen mode Exit fullscreen mode

# 添加我们的命令

对于这个插件，我们只需要一个命令，我将以我的插件命名为:`eval-query`。[命令](https://github.com/mawww/kakoune/blob/master/doc/pages/commands.asciidoc#declaring-new-commands)是使用带有一些可选开关的`def`关键字定义的。我们当然需要一个 docstring，我们将添加`-override`开关，这样我们可以在工作时重新定义这个命令(当我们完成时，我们希望删除它)。

```
def eval-query \
  -override \
  -docstring "Evaluate current selection using given connection." \
%{
    echo "I don't do anything yet"
} 
```

Enter fullscreen mode Exit fullscreen mode

该语法是多行字符串的 kakoune-speak。命令体遵守[命令解析文档](https://github.com/mawww/kakoune/blob/17c5e7aa5fb020186378f7f738e617b5743839fc/doc/pages/command-parsing.asciidoc#command-parsing)中描述的规则。当调用该命令时，该字符串的内容将被评估。

让我们通过在正常模式下键入`:eval-query`来调用我们的无操作命令。这将在屏幕的左下方打印我们的消息，“我还没有做任何事情”。

# 请求输入

我们需要做的第一件事是添加一种方式来询问用户我们想要使用哪个连接。有很多方法可以做到这一点，但我们将使用 [`-shell-script-candidates`](https://github.com/mawww/kakoune/blob/master/doc/pages/commands.asciidoc#declaring-new-commands) 。这个开关接受一个 shell 脚本，该脚本提供了一个以换行符分隔的可能完成列表，Kakoune 将对该列表进行模糊匹配。我们还必须使用`-params`告诉 Kakoune 需要多少参数。我们还会得到我们所提供的命令的回应:

```
def eval-query \
  -override \
  -docstring "Evaluate current selection using given connection." \
  -params 1 \
  -shell-script-candidates %{ echo something } \
%{
    echo %arg{@}
} 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们只有一个自动完成的选项，“某事”。当我们用`:eval-query`调用这个命令时，它用“某物”提示我们，并把它回显给我们。

让我们通过读取配置文件并使用 [jq](https://stedolan.github.io/jq/) 获取密钥来填写完成脚本。

```
def eval-query \
  -override \
  -docstring "Evaluate current selection using given connection." \
  -params 1 \
  -shell-script-candidates %{
    cat .eval-query.json \
      | jq -r '.connections|keys|@sh' \
      | sed s/\'//g \
      | tr -s " " "\n"
  } \
%{
    echo %arg{@}
} 
```

Enter fullscreen mode Exit fullscreen mode

它查看项目的配置文件，提取键，并将它们作为提示提供给命令的自动完成。现在我们知道使用哪个连接了！

# 评估查询

难题的最后一部分是找到我们想要的查询，并将其传递给我们的数据库来执行。Kakoune 通过为我们的 shell 扩展提供一个`$kak_selection`变量使这变得简单。另一方面，正确引用字符串可能会很棘手。

```
def eval-query \
  -override \
  -docstring "Evaluate current selection using given connection." \
  -params 1 \
  -shell-script-candidates %{
    cat .eval-query.json \
      | jq -r '.connections|keys|@sh' \
      | sed s/\'//g \
      | tr -s " " "\n"
  } \
%{
  info -title "Query output" %sh{
    echo "${kak_selection}"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我已经将`echo`命令更改为`info`命令，这将打开一个信息框，其中填充了`%sh`扩展的标准输出。在 shell 扩展中，我们将回显当前选择的内容。

为了将它传递给 postgres，我们从指定的连接中获取选项，并将它们和当前选择一起传递给 postgres:

```
def eval-query \
  -override \
  -docstring "Evaluate current selection using given connection." \
  -params 1 \
  -shell-script-candidates %{
    cat .eval-query.json \
      | jq -r '.connections|keys|@sh' \
      | sed s/\'//g \
      | tr -s " " "\n"
  } \
%{
  info -title "Query output" %sh{
    psql `cat .eval-query.json | jq .connections.$1 | cut -d\" -f 2` \
      -c "${kak_selection}" 2>&1
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我将 stderr 重定向到 stdout，这样我们就可以看到可能出现的任何错误(例如，不存在的数据库)。这看起来是这样的:

[![Evaluating a query in Kakoune](img/a1268accb575905738d4484c713b5f56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oVWbIm8c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cnrmwcq66p2gp4a67abq.gif)

# 绑定到热键

你会注意到这并不完全符合人体工程学。我必须每次都输入完整的命令，包括我的连接吗？答案是否定的。我们可以使用 [`map`](https://github.com/mawww/kakoune/blob/master/doc/pages/mapping.asciidoc) 将我们的命令——以及任何有用的变体——绑定到键盘快捷键。

```
map global user \" \
  -docstring "Evaluate current selection in local database" \
  ':eval-query local<ret>'

map global user \' \
  -docstring "Evaluate current paragraph in local database" \
  '<a-i>p:eval-query local<ret>' 
```

Enter fullscreen mode Exit fullscreen mode

我在这里创建了两个绑定；第一个只是用填充的`local`连接调用我们的命令。第二个做同样的事情，但是选择当前段落。

这使得我们可以通过两次击键来运行查询，即使我们还没有选择它。如果我们需要更多的控制，例如只运行查询的一部分，我们可以使用更基本的双引号版本。您可以在下面的操作中看到:

[![Shortcuts!](img/b6071bbe47da016a56f9491198fdf485.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VBSLKfMM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ujdgbg6oqq3yi135tqe8.gif)

注意，我选择绑定的键是完全任意的；把它们改成你喜欢的任何东西！

# 补遗

这个“插件”还远远没有完成。像每一个软件项目一样，它有可能成为一个商业/所有消费的困扰，所以我现在要在这里停下来。一部分作为免责声明，一部分作为读者的练习，这里有一个不完全的改进列表:

*   大型结果集将不显示；我不确定内部发生了什么，但 Kakoune 似乎对可以放入`info`块的文本数量有一个上限。处理这种情况的一种方法是将结果放在一个[暂存缓冲区](https://github.com/mawww/kakoune/blob/master/doc/pages/commands.asciidoc#files-and-buffers)中。
*   插件真的不应该把键盘快捷键放在用户范围内。更好的方法可能是创建一个[自定义用户模式](https://github.com/mawww/kakoune/blob/17c5e7aa5fb020186378f7f738e617b5743839fc/doc/pages/modes.asciidoc#user-modes)来命名插件定义的快捷方式，然后[声明一个选项](https://github.com/mawww/kakoune/blob/17c5e7aa5fb020186378f7f738e617b5743839fc/doc/pages/options.asciidoc)，用户可以在她的配置文件中覆盖它。为了将它加入到映射中，当用户设置模块选项时，您可能需要监听一个`GlobalSetOption` [钩子](https://github.com/mawww/kakoune/blob/17c5e7aa5fb020186378f7f738e617b5743839fc/doc/pages/hooks.asciidoc)来重新绑定映射。
*   可以更可靠地处理错误和边缘情况。在这篇文章中，我基本上忽略了它。
*   为了共享这些代码(并承担维护一个开源项目的负担)，我们应该把它包装在一个可安装包中。这可能就像让文件可以在某个地方下载一样简单。

也许我会写更多关于如何完成这些事情的文章😉

无论如何，我在这里的观点主要不是演示如何创建一个 Kakoune 插件；它展示了定制 Kakoune 以适应您的工作流程的灵活性和简易性。一个只关注一个人的用例的特定架构可能远不如一个面向一般用途的插件健壮。不要害怕推开 JetBrains，做一些你自己的工具！
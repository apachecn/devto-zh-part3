# 用 OCaml 制作解释器——启动 lexer

> 原文：<https://dev.to/nt591/crafting-interpreters-in-ocaml-starting-a-lexer-4h3b>

我目前正在通过鲍勃·尼斯特伦的书*制作口译员*学习口译员是如何工作的。这篇文章是我正在进行的系列文章的一个例子。

* * *

要了解原始资料，请查看《制作解释器》的[扫描章节](https://craftinginterpreters.com/scanning.html)

* * *

为了方便起见，我会推荐你克隆这个[启动回购](https://github.com/nt591/olox-starter-template)。我们将为 OCaml 中的 Lox 实现构建一个扫描器，我们称之为 Olox。让我们创建一个名为`olox.ml`的文件。这将是我们应用程序的入口点，类似于原始资料中的`Lox.java`。

Bob 的 Java 代码处理以下三种情况:

1)如果传入了多个参数，则输出一行告诉用户正确的用法并退出。如果传入了一个参数，运行一个同名文件。
3)否则，打开提示即可。

好的，让我们开始在我们的`olox.ml`
中写出下面的代码

```
 let main = fun _ -> match Array.length Sys.argv with
    | 0 -> run_prompt ()
    | 1 -> run_file (Array.get Sys.argv 0)
    | _ -> print_endline "Usage: olox [script]"; exit 64; 
```

我们正在对传入的参数向量的长度进行模式匹配。 [Sys.argv](https://caml.inria.fr/pub/docs/manual-ocaml/libref/Sys.html) 接受一个我们可以使用的字符串数组。如果没有参数传入，我们运行提示。如果传入了一个，我们就用第一个元素运行一个文件。否则，我们将打印一个错误并退出。我们不能真正运行这段代码，因为我们在`run_prompt`和`run_file`中有两个未定义的函数，但我们只是跟着做。

所以现在需要定义这两个函数。Bob 的实现通过从文件中读取字节将`run_file`委托给一个名为`run`的函数。`run_prompt`也只是从输入中取一行，调用`run`。所以我们现在需要定义这三个函数。

让我们在主函数
上方的模块中添加以下内容

```
 let run source =
    let tokens = Scanner.scan_tokens in
    List.iter (fun token -> print_endline token) tokens

  let run_prompt = fun _ ->
    while true do
      print_string "> ";
      let input = read_line ()
      in run input;
    done

  let run_file filename =
    let channel = open_in filename in
    try
      (* read entire file *)
      let line = really_input_string channel (in_channel_length channel) in
      run line;
      flush stdout;
      close_in channel
    with e ->
      close_in_noerr channel;
      raise e 
```

像 Bob 的 Java 代码一样，我们有一个`run`函数，它只从扫描器获取令牌——在他的例子中，是一个扫描器类的实例。我们将只使用一个待定义的模块。然后我们将遍历返回的令牌并打印出来。

我们的`run_prompt`函数将打印一个字符串作为提示，然后读入下一行并将其传递给我们的 run 函数。

我们的`run_file`函数获取一些文件名并创建一个输入通道，放在文件的开头。然后，它通过读取`in_channel_length channel`个字符来获取整个文件，然后传递给`run`。为清楚起见，请参见以下类型签名。

`val really_input_string : in_channel -> int -> string`
T1】

`really_input_string`接受一个通道和一个 int，返回一个 string。`in_channel_length`返回一个通道的长度。

到目前为止一切顺利！如果您将它复制并粘贴到 utop 中，您会在`Scanner`模块上出错。这很好——可以随意用类似于`let run = print_endline`的东西替换`run`,并测试所有这些功能。事实上，让我们现在就试一试。创建一个名为`test.md`的文件，并添加以下代码。

```
Hello, world!
This is the second line!
Now on line 3 with spec!@l characters 
```

在您的终端中，运行`utop`，复制并粘贴下面的代码，用一个简单的输出替换`run`。

```
let run = print_endline

let run_prompt = fun _ ->
  while true do
    print_string "> ";
    let input = read_line ()
    in run input;
  done

let run_file filename =
  let channel = open_in filename in
  try
    (* read entire file *)
    let line = really_input_string channel (in_channel_length channel) in
    run line;
    flush stdout;
    close_in channel
  with e ->
    close_in_noerr channel;
    raise e 
```

试着运行`run_file "test.md";;`，然后`run_prompt ();;`你提示符中的任何东西都应该被返回。整洁！现在`ctrl+D`要退出了。

接下来是错误处理！鲍勃提出了以下观点，我认为这很有道理

> 我把错误报告放在这里而不是塞进扫描器和其他发生错误的阶段的另一个原因是为了提醒您，将产生错误的代码与报告错误的代码分开是一个很好的工程实践。

然而，为了不使用全局可变变量，并保持我的一些逻辑包含在扫描器中(目前)，我将继续在扫描器中处理所有的错误管理。通过这种方式，我们可以安全地隔离所有的扫描仪逻辑(行号、字符位置),并在其上进行报告。

让我们直接开始吧。创建一个名为`scanner.ml`的文件。Bob 的例子为 Token 和 TokenType 创建了类。在我们的 FP 世界中，这些可能只是我们的扫描器所知道的类型，所以我们将把它们都包装在一个文件中。如果我们以后需要重构，我们可以。

查看源代码中的`Token.java`，我们看到一个巨大的枚举。当我看到 enum 的时候，我会想`variant types`——如果你需要的话，在这里阅读[。让我们在扫描器中创建一个变量类型。](https://dev.realworldocaml.org/variants.html) 

```
module Scanner = struct
  type token_type =
    (* single-character tokens *)
    | LEFT_PAREN
    | RIGHT_PAREN
    | LEFT_BRACE
    | RIGHT_BRACE
    | COMMA
    | DOT
    | MINUS
    | PLUS
    | SEMICOLON
    | SLASH
    | STAR

    (* One or two character tokens *)
    | BANG
    | BANG_EQUAL
    | EQUAL
    | EQUAL_EQUAL
    | GREATER
    | GREATER_EQUAL
    | LESS
    | LESS_EQUAL

    (* Literals *)
    | IDENTIFIER
    | STRING
    | NUMBER
    (* Keywords *)
    | AND
    | CLASS
    | ELSE
    | FALSE
    | FUN
    | FOR
    | IF
    | NIL
    | OR
    | PRINT
    | RETURN
    | SUPER
    | THIS
    | TRUE
    | VAR
    | WHILE
    | EOF
end 
```

引用第 4.2.2 节，

> 有文字值的词位——数字和字符串等。因为扫描器必须遍历文字中的每个字符才能正确识别它，所以它还可以将它转换为真正的运行时值，供解释器稍后使用。

在我们继续之前，我们需要一些包装文字的方法。我们的令牌需要有一个令牌类型和可选文字的引用——例如，令牌可以是不带文字的 TRUE 类型，或者是带文字的 NUMBER 类型`2`。所以，让我们从两种类型的文字开始——字符串和数字

```
 type literal_type = STRING_LITERAL of string | NUMBER_LITERAL of float 
```

这并没有解决我们所有的问题，但至少我们现在对字面量有了一些概念。继续制作我们的令牌，我们应该定义一个表示令牌的类型。Bob 的 Token 类有一个 TokenType 类型、一个 String 的 lexeme、一个某个对象的 literal 和一行 Int。我们可以用
做类似的事情

```
type token = {
  lexeme: string;
  literal: literal_type option;
  line: int;
  token_type: token_type;
} 
```

*Nikhil 为什么字面意思是一个选项？*正如我们之前提到的——令牌不需要包含文字。token_type `VAR`没有文字，所以我们的令牌中可能不需要文字。期权代表了这一点。真实世界 OCaml 关于选项的[章节是获取更多信息的很好的入门读物。](https://dev.realworldocaml.org/guided-tour.html#options)

让我们花点时间看看 4.4 节中的初始扫描器代码。该文件定义了一个类`Scanner`，它有一个字段`source`，一个表示我们正在扫描的代码的字符串。`scanTokens`获取一个令牌列表，检查我们是否在源的末尾，如果不是，扫描令牌。一旦结束，我们添加一个表示文件结束的标记，并返回标记列表。我们还使用了一个助手函数来确定我们是否在源代码的末尾。

我们看到原来的 Java 代码有一些字段可供使用。让我们拥抱我们的 FP 世界，而不是创建一个类，我们将创建一个类型来表示一些字段记录。然后，我们可以确保我们所有的函数都接受并发出该类型。

```
type scanner_context = {
  source: string;
  start: int;
  current: int;
  line: int;
} 
```

现在我们有了一个包含所有已知字段的类型，让我们来定义我们的方法。首先，让我们创建一个接口文件。用下面的
创建名为`scanner.mli`的文件

```
module Scanner : sig
  type token_type

  type literal_type

  type token

  type scanner_context

  val scan_tokens: string -> token list

  val is_at_end: scanner_context -> bool
end 
```

在我们的接口中，我将使用抽象类型。`type token`和`type scanner_context`现在对任何实现这个签名的模块隐藏类型的实现。你可以在 [OCaml 网站](https://ocaml.org/learn/tutorials/modules.html#Abstract-types)和这堂课的[笔记](http://www.cs.cornell.edu/courses/cs3110/2019sp/textbook/modules/abstract_types.html)上读到更多关于他们的内容。

我们的签名实现了 Bob 的 Java 代码所做的事情- `is_at_end`为应用程序的状态返回 true 或 false。`scan_tokens`将检查源代码并返回所有令牌。

让我们证明我们的接口确实做了一些事情！打开`dune`并用以下代码
替换

```
(executable
  (name olox)
  (libraries oUnit)
)

(alias
  (name    runtest)
  (deps    (:x olox.exe))
  (action  (run %{x}))) 
```

现在只需运行`dune build`和`dune runtest`。您应该会看到以下错误。

```
File "scanner.ml", line 1:
Error: The implementation scanner.ml
       does not match the interface .olox.eobjs/byte/scanner.cmi:
       ...
       In module Scanner:
       The value `is_at_end' is required but not provided
       File "scanner.mli", line 56, characters 2-40: Expected declaration
       In module Scanner:
       The value `scan_tokens' is required but not provided
       File "scanner.mli", line 54, characters 2-48: Expected declaration 
```

上面还有一点内容，我们稍后再来讨论。我们的界面工作正常！我们声明该模块必须有两个特定的功能，但是没有实现它们。现在要做到这一点。

我们先写`is_at_end`。该函数接受我们的包装上下文并返回一个布尔值。我写了

```
let is_at_end ctx = ctx.current >= (String.length ctx.source) 
```

然后我们会有我们的`scan_tokens`函数。这个函数只接受源代码字符串作为输入，并将返回一个令牌列表。在中间的某个地方，我们需要包装我们上下文的其余部分。OCaml 有本地的`let`绑定，我们可以使用它。我们现在还意识到，我们需要维护正在添加的令牌的运行列表。让我们给我们的`scanner_context`添加代币。在`type scanner_context`的定义里面加上`tokens: token list;`就可以了。

看看 Bob 的实现，我们有了更多的信息。我们有一个`scanToken`函数...一些事情。我们至少可以假设，由于循环内部的重置，它会全局地改变`current`的值。我们还添加了一些 EOF 标记。让我们勾画出一个`scan_token`函数可能是什么样子。假设它将改变两个值，current 和我们的令牌列表，让它接收并输出一个`scanner_context`。在我们的`.mli`文件中添加

```
 val scan_token: scanner_context -> scanner_context 
```

Bob 的令牌添加基本上只是实例化一个令牌对象，并将其添加到全局列表中。让我们做同样的事情，通过编写一个函数`add_token`，它接受一个令牌的所有字段以及上下文，并返回新的上下文。

```
 val add_token: token_type -> string -> literal_type option -> int -> scanner_context -> scanner_context 
```

好吧！我们有一些松散定义的代码接口。让我们实施。在`scanner.ml`中，我们将先做两者中的第一个。

```
let add_token token_type lexeme literal line ctx =
  {ctx with tokens = ctx.tokens @ [{
      token_type;
      lexeme;
      literal;
      line
    }]
  } 
```

这里有一个性能问题，OCaml 的列表附加操作符(或者是`@`或者是`List.append`)通过遍历第一个列表的长度而在`O(n)`时间内运行。这并不理想，但这就是我们使用链表所得到的。现在，我们将忽略这一点，当它是一个性能问题时再回来讨论。

我们的下一个功能是扫描。看看 Bob 的实现。换句话说——他获取下一个字符，检查它是什么，然后添加一个标记。我将把它分成三个函数——一个函数用于递增`current`计数器，一个函数用于获取下一个字符，还有我们上面的 add 标记。

我们可以添加以下两个接口

```
 val advance: scanner_context -> scanner_context

  val current_char: scanner_context -> char option 
```

然后定义我们的函数

```
 let advance context = {context with current = context.current + 1}

  let current_char context =
    try Some (String.get context.source context.current) with Invalid_argument _ -> None 
```

我对`current_char`使用了一个`option`,因为字符串访问可能会出现越界异常，安全总比后悔好。

我们还有一个 Bob 定义的一元函数`addToken`,它只是遵从一个二元函数。我将把这两个函数定义为增加一个字面量，或者一个非字面量。这意味着我们将需要一个函数`add_literal_token`和`add_non_literal_token`——前者接受一个`token_type`和`Some literal`，后者可以遵从前者，接受一个`None`。我们将再次把`context`作为最后一个参数传递，以使我们的函数易于流水线化。

```
val add_literal_token: token_type -> literal_type option -> scanner_context -> scanner_context

val add_non_literal_token: token_type -> scanner_context -> scanner_context 
```

太好了。现在来写这些小助手...

```
let add_literal_token token_type literal_type context =
  let text = (String.sub context.source context.start (context.current - context.start)) in
  add_token token_type text literal_type context.line context

let add_non_literal_token token_type context = add_literal_token token_type None context 
```

我们从起始位置创建一个子串，并捕获起始位置和当前位置之间的差异，然后添加标记。在 OCaml 中需要一个开始和长度，而不是一个开始和结束，所以我们需要做一点数学计算。

现在我们有了这些，我们可以写出我们的`scan_token`函数，最后写出`scan_tokens`。同样，因为我们看到 Bob 例子中的`scan_token`关心两个字段——当前字符和令牌列表，所以让我们传入上下文并取出上下文。

```
val scan_token: scanner_context -> scanner_context 
```

```
let scan_token context =
  let advanced_context = advance context in
  match (current_char advanced_context) with
  | Some '(' -> add_non_literal_token LEFT_PAREN advanced_context
  | Some ')' -> add_non_literal_token RIGHT_PAREN advanced_context
  | Some '{' -> add_non_literal_token LEFT_BRACE advanced_context
  | Some '}' -> add_non_literal_token RIGHT_BRACE advanced_context
  | Some ',' -> add_non_literal_token COMMA advanced_context
  | Some '.' -> add_non_literal_token DOT advanced_context
  | Some '-' -> add_non_literal_token MINUS advanced_context
  | Some '+' -> add_non_literal_token PLUS advanced_context
  | Some ';' -> add_non_literal_token SEMICOLON advanced_context
  | Some '*' -> add_non_literal_token STAR advanced_context
  | _ -> failwith "Disallowed character" 
```

咻。那是一大堆工作，但我们做到了。我们现在有了一种从字符串中扫描标记的方法。现在是有趣的部分。

在我们的下一篇文章中，让我们试着写更多匹配的案例。如果幸运的话，一些测试。
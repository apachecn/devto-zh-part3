# 用 OCaml 制作解释器——完成 Lexer

> 原文：<https://dev.to/nt591/crafting-interpreters-in-ocaml-finishing-the-lexer-2ebp>

* * *

这是基于我的上一篇文章。

要了解原始资料，请查看《制作解释器》的[扫描章节](https://craftinginterpreters.com/scanning.html)

* * *

转到本书的第 4.5.2 节，我们有多字符操作符。酷！具体来说，我们有`!=`、`==`、`<=`和`>=`操作员。Bob 定义了一个`match`方法，该方法采用某个期望的字符，并检查当前字符是否是某个字符。如果我们在终点，那不是。如果当前字符不是预期的字符，则为 false。否则，我们前进并返回 true。好的，这看起来很简单。

让我们定义一个`match`函数。请记住，我们没有任何全局变量，所以我们将不得不通过上下文以及字符。因为我们想要有条件地变异`current`，所以我们的函数接受并返回一个`scanner_context`对象是有意义的。我们还想维护布尔返回，所以让我们返回一个[元组](https://ocaml.org/learn/tutorials/data_types_and_matching.html#Structures)。

我们还需要我们的 match 函数来查看下一个位置的字符，所以我们需要一个 helper 函数。

在我们的`scanner.mli`文件中，我们可以定义以下内容:

```
val next_char: scanner_context -> char option

val match_character: char -> scanner_context -> bool * scanner_context 
```

这定义了一个函数，它接受一个字符和一个上下文，并返回一个布尔和上下文类型的二元组。

现在我们可以写一些代码

```
let next_char context =
  try Some (String.get context.source (context.current)) with Invalid_argument _ -> None

let match_character expected_char context = if (is_at_end context) then (false, context) else
  match next_char context with
    | Some c when c = expected_char -> (true, advance context)
    | _ -> (false, context) 
```

你可能会注意到`next_char`和`current_char`看起来非常相似。我真的没有一个好的解决方案，但现在没关系。

现在，我们可以在匹配语句中添加一些新的案例。我们将不得不编写许多类似于
的案例

```
let x = match match_character '=' advanced_context with
  | (true, ctx) -> add_non_literal_token BANG_EQUAL ctx
  | (false, ctx) -> add_non_literal_token BANG ctx 
```

但是对于以上所有四个双字符运算符。既然我终于要偷懒了，那就写个助手吧。我们的新函数需要一个预期的匹配字符、一个真值的标记、一个假值的标记和一个上下文。它将返回添加了令牌的新上下文。该签名将看起来像

```
val add_conditional_non_literal_token: char -> token_type -> token_type -> scanner_context -> scanner_context 
```

干净利落。现在我们可以编写函数

```
let add_conditional_non_literal_token expected_char token_if_true token_if_false context =
    match (match_character expected_char context) with
    | (true, new_context) -> add_non_literal_token token_if_true new_context
    | (false, new_context) -> add_non_literal_token token_if_false new_context 
```

我们的`scan_token`函数现在有了以下新子句:

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
  | Some '!' -> add_conditional_non_literal_token '=' BANG_EQUAL BANG advanced_context
  | Some '=' -> add_conditional_non_literal_token '=' EQUAL_EQUAL EQUAL advanced_context
  | Some '>' -> add_conditional_non_literal_token '=' GREATER_EQUAL GREATER advanced_context
  | Some '<' -> add_conditional_non_literal_token '=' LESS_EQUAL LESS advanced_context
  | _ -> failwith "Disallowed character" 
```

到目前为止，我们做得很好！第 4.6 节增加了更多的词汇处理。具体来说，评论。让我们用文字描述一下 Bob 的代码是做什么的。

如果我们看到一个斜杠，检查下一个字符是否也是一个斜杠。如果是，就继续向前，直到我们看到一个换行符或者到达文件的末尾。如果下一个字符不是斜杠，那么只需添加一个斜杠标记。在我们的世界里，我们没有多行注释。

所以如果我们向前看一点，我们也有这个`peek`函数，如果我们在最后，返回一个空字符，否则返回当前位置的字符。我们可以在这里使用另一个`char option`,因为在这个神奇的 ML 世界中我们不需要 nulls。

原来我们已经写了这个函数了！做完全相同的事情。所以我们就用这个。如果你愿意，可以别名为`let peek = next_char`但我不会。

我们可以在这里使用我们的`match_character`助手创建另一个助手函数。我们想要一个函数，要么添加一个斜杠标记，要么继续前进。现在让我们称这个函数为`add_conditional_slash`。它将获取上下文并返回上下文。

```
val add_conditional_slash: scanner_context -> scanner_context 
```

```
let add_conditional_slash context =
    let rec consume_line context =
      match (not (next_char context = Some '\n')), not (is_at_end context)) with
      | (true, true) -> consume_line (advance context)
      | _ -> context
    in
    if match_character '/' context then consume_line context else add_non_literal_token SLASH context 
```

让我们解释一下这段代码:

我们嵌套了一个函数来消耗整行，该函数说“如果下一个字符不是换行符，并且我们不在文件的末尾，那么向前移动一个字符并再次检查。一旦我们看到一个新行或文件结束，返回上下文对象。

然后我们简单的说如果下一个字符是斜杠，消耗掉这一行。否则，添加一个斜杠标记。也许以后我们会取出这个嵌套函数，但是现在我认为没问题，我们没有其他的用例。

好的，我们有一些鲍勃列出的违约案例。让我们通过返回上下文来翻译它们，或者在换行符的情况下，在我们的上下文中增加该行。

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
    | Some '!' -> add_conditional_non_literal_token '=' BANG_EQUAL BANG advanced_context
    | Some '=' -> add_conditional_non_literal_token '=' EQUAL_EQUAL EQUAL advanced_context
    | Some '>' -> add_conditional_non_literal_token '=' GREATER_EQUAL GREATER advanced_context
    | Some '<' -> add_conditional_non_literal_token '=' LESS_EQUAL LESS advanced_context
    | Some ' ' -> advanced_context
    | Some '\r' -> advanced_context
    | Some '\t' -> advanced_context
    | Some '\n' -> {advanced_context with line = advanced_context.line + 1}
    | _ -> failwith "Disallowed character" 
```

没那么糟！这看起来越来越有可读性了。这有多棒。让我们处理文字，然后编写一些测试。

Bob 将所有的字符串处理委托给一个名为`string`的函数。换句话说，它执行以下操作。

只要下一个字符不是引号，只要我们不在末尾，就消费。如果我们看到一个换行符，增加一行并继续下去。如果我们看到文件的结尾，抛出一个错误。一旦我们看到了右引号，再前进一个，然后获取字符串并添加一个字符串标记。好吧！让我们创建自己的函数。我们称之为`add_string_literal`，它将获取并输出一个 scanner_context。

这里有一些条件行递增逻辑，为了方便起见，我会让它有自己的函数。

```
val increment_line_if_newline: scanner_context -> scanner_context

val add_string_literal: scanner_context -> scanner_context 
```

```
 let increment_line_if_newline context =
    match next_char context with
    | Some '\n' -> {context with line = context.line+1}
    | _ -> context

  let rec add_string_literal context =
     if( not ( next_char context = Some('"')) && (not (is_at_end context)) ) then
      context |> increment_line_if_newline |> advance |> add_string_literal
    else if (is_at_end context) then failwith "Unterminated string" else
    let str = (String.sub context.source (context.start + 1) (context.current - 1)) in
    add_literal_token STRING (Some (STRING_LITERAL str)) context 
```

这是有点棘手和必要的。我想找到一种模式匹配出来的方法。但是现在我们可以将下面的内容添加到我们的`scan_tokens`

```
| Some '"' -> add_string_literal advanced_context 
```

开始变得令人兴奋了。让我们也在 4.6.2 节中处理数字文字。数字的规则很简单——如果有一个`.`字符，它的两边必须有 1 个或更多的数字。所以让我们试着实现它。

我们需要一个接受一个`char option`并返回真或假的函数`is_digit`，然后一个接受并返回一个`scanner_context`的`add_number_literal`函数。我们还将创建一个`peek_next`，它接受一个`scanner_context`并返回`char option`。

为了使事情变得简单，我们应该编写一个简单地捕获数字的函数。姑且称之为`capture_digits`。它只是抽象出`while (isDigit(peek())) advance();`的 Java 代码。我们也可以用`capture_decimal`写一个函数来遵从上面的

在我们的界面:

```
val is_digit: char option -> bool

val capture_digits: scanner_context -> scanner_context

val capture_decimal: scanner_context -> scanner_context

val peek_next: scanner_context -> char option

val add_number_literal: scanner_context -> scanner_context 
```

又回到了`scanner.ml`和

```
let peek_next context =
    try Some (String.get context.source (context.current + 1)) with Invalid_argument _ -> None

  let is_digit character = match character with
    | Some c when c >= '0' && c <= '9' -> true
    | _ -> false

  let rec capture_digits context =
    if is_digit (next_char context) then context |> advance |> capture_digits else context

  let capture_decimal context = match (next_char context) with
    | Some '.' when (is_digit peek_next context) -> context |> advance |> capture_digits
    | _ -> context

  let add_number_literal context =
    let new_context = context |> capture_digits |> capture_decimal in
    let stringified_number = (String.sub new_context.source new_context.start (new_context.current - new_context.start)
    let parsed_number = int_of_string stringified_number in
    add_literal_token NUMBER NUMBER_LITERAL(stringified_number) new_context 
```

然后只需在`scan_tokens`中添加以下条款

```
| digit when (is_digit digit) -> add_number_literal advanced_context 
```

辣。如果您继续操作，您应该会得到一些关于未使用字符的编译器错误，但没有语法或接口错误。呜哇！

让我们快速完成标识符。再一次，在 Bob 的章节中，我们看到了断言`if isAlpha`或英语，“如果下一个词位以字母或下划线开始，捕捉后面的所有字母数字，并假设它是一个标识符。

我们的版本`is_alpha`将接受一个`char option`并返回布尔值。`is_alpha_numeric`也会如此。我们的`add_identifier_literal`将需要获取一个扫描器上下文并返回一个。让我们创造一个新的`literal_type`T5

```
type literal_type = STRING_LITERAL of string | NUMBER_LITERAL of float | IDENTIFIER_LITERAL of string 
```

这个新类型将包装用户创建的标识符。如果用户定义了一个顶级函数，它就是 IDENTIFIER_LITERAL。如果它是一个常见的关键字，如和，它只是一个标识符。

来定义实现。

```
 val is_alpha: char option -> bool

val is_alpha_numeric: char option -> bool

val add_identifier_literal: scanner_context -> scanner_context 
```

而我们的实现

```
let is_alpha character = match character with
  | Some c when c >= 'a' && c <='z' -> true
  | Some c when c >= 'A' && c <='Z' -> true
  | Some c when c = '_' -> true
  | _ -> false

  let is_alpha_numeric character = (is_alpha character) || (is_digit character)

  let rec add_identifier_literal context =
    if (is_alpha_numeric (next_char context)) then context |> advance |> add_identifier_literal
    else
    let substring = (String.sub context.source context.start (context.start - context.current)) in
    match substring with
    | "and" -> add_non_literal_token AND context
    | "class" -> add_non_literal_token CLASS context
    | "else" -> add_non_literal_token ELSE context
    | "false" -> add_non_literal_token FALSE context
    | "for" -> add_non_literal_token FOR context
    | "fun" -> add_non_literal_token FUN context
    | "if" -> add_non_literal_token IF context
    | "nil" -> add_non_literal_token NIL context
    | "or" -> add_non_literal_token OR context
    | "print" -> add_non_literal_token PRINT context
    | "return" -> add_non_literal_token RETURN context
    | "super" -> add_non_literal_token SUPER context
    | "this" -> add_non_literal_token THIS context
    | "true" -> add_non_literal_token TRUE context
    | "var" -> add_non_literal_token VAR context
    | "while" -> add_non_literal_token WHILE context
    | _ -> add_literal_token IDENTIFIER (Some (IDENTIFIER_LITERAL substring)) context 
```

然后我们可以在我们的`scan_tokens`子句中添加最后一块。

```
| alpha when (is_alpha alpha) -> add_identifier_literal advanced_context 
```

现在，如果你再`dune runtest`一次，你应该得到的唯一问题是对`olox.ml`的`Scanner.scan_tokens`的调用。继续前进，在顶部的`open Scanner`来修复它。

我们现在有一个正常工作的扫描仪。在下一章，我们将注释掉`olox.ml`中未完成的代码，并编写一些单元测试。
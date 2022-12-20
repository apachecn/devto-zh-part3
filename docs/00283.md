# 信任你自己的口齿不清

> 原文：<https://dev.to/deciduously/rust-your-own-lisp-50an>

这是我在上一篇文章《通过避免问题来解决问题》中谈到的代码的更完整的演练。

该项目是由 [orangeduck](http://theorangeduck.com/page/about) 将[构建自己的 Lisp](http://www.buildyourownlisp.com/) 翻译成 [Rust](https://www.rust-lang.org/) 。他的书棒极了，既是对 C 的介绍，也是对编写解释器的介绍。

这篇文章远不能取代那篇文章，去读那本书吧。太棒了。然而，在翻译成 Rust 时，有一些必要的区别值得注意。这篇文章并不包括完整的代码，而是每个概念的例子，对于任何试图在 Rust 中进行类似项目或翻译的人来说可能是有用的。为了清楚起见，我还删除了大多数调试日志。完整实施可在本报告的中找到。

我从这个项目中学到了很多关于 C、解释器和 Rust 的知识，强烈推荐这个练习。不管是好是坏(可能更坏)，我把这个实现叫做`blispr`。

## 锈线

首先，我们得收集一些绳子。我强烈推荐 [`rustyline`](https://github.com/kkawakam/rustyline) ，一个纯锈的`readline`实现。您可以获得现成的行编辑、键盘命令和命令历史。这就是你要做的一切:

```
fn repl(e: &mut Lenv) -> Result<()> {
    println!("Blispr v0.0.1");
    println!("Use exit(), Ctrl-C, or Ctrl-D to exit prompt");

    let mut rl = Editor::<()>::new();
    if rl.load_history("./.blispr-history.txt").is_err() {
        println!("No history found.");
    }

    loop {
        let input = rl.readline("blispr> ");

        match input {
            Ok(line) => {
                rl.add_history_entry(line.as_ref());
                print_eval_result(eval_str(e, &line));
            }
            Err(ReadlineError::Interrupted) => {
                info!("CTRL-C");
                break;
            }
            Err(ReadlineError::Eof) => {
                info!("CTRL-D");
                break;
            }
            Err(err) => {
                warn!("Error: {:?}", err);
                break;
            }
        }
    }
    rl.save_history("./.blispr-history.txt")?;
    Ok(())
}

fn print_eval_result(v: BlisprResult) {
    match v {
        Ok(res) => println!("{}", res),
        Err(e) => eprintln!("Error: {}", e),
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

需要注意的一点是，我并没有传播`eval_str`可能在这里用`?`向调用者抛出的错误——我不希望 blispr 评估错误导致 repl 崩溃。在`eval_str()`内部可能发生的任何事情，我只想用`eprintln!()`通知用户，然后再次循环。通过的`&mut Lenv`是全球环境——下面会有更多的介绍。

大部分评估在`match`的`Ok()`臂中暗示——工作的实质发生在`eval_str()` :

```
pub fn eval_str(e: &mut Lenv, s: &str) -> BlisprResult {
    let parsed = BlisprParser::parse(Rule::blispr, s)?.next().unwrap();
    let mut lval_ptr = lval_read(parsed)?;
    lval_eval(e, &mut *lval_ptr)
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是了，这就是全部的翻译。这个函数执行评估以文本字符串形式给出的编程语言所需的所有步骤。第一行将解析树存储到`parsed`。这用我们将在下面定义的语义语法标签来标记我们的输入字符串。下一行在`lval_ptr`将该树读入 AST，这将整个程序表示为可以递归计算的 lisp 值。最后，我们返回使用`lval_eval`对 AST 进行全面评估的结果，这确保了不会发生进一步的评估。过程中发生的任何错误都被`?`操作符捕获——下面我们将看到`Result<T>`别名代表什么。

## Lval

为了表示 AST，我使用了一个名为`Lval` :
的 Rust `enum`

```
// The recursive types hold their children in a `Vec`
type LvalChildren = Vec<Box<Lval>>;
// This is a function pointer type
pub type LBuiltin = fn(&mut Lval) -> BlisprResult;

// There are two types of function - builtin and lambda
#[derive(Clone)]
pub enum LvalFun {
    Builtin(String, LBuiltin), // (name, function pointer)
    Lambda(HashMap<String, Box<Lval>>, Box<Lval>, Box<Lval>), // (environment, formals, body), both should be Qexpr
}

// The main type - all possible Blispr values
#[derive(Debug, Clone, PartialEq)]
pub enum Lval {
    Fun(LvalFun),
    Num(i64),
    Sym(String),
    Sexpr(LvalChildren),
    Qexpr(LvalChildren),
} 
```

Enter fullscreen mode Exit fullscreen mode

每个变体都包含其内容。当我们阅读文本时，每个元素都将被转换成适当类型的`Lval`。例如，像`"4"`这样的字符串将被解析成`Lval::Num(4)`。现在，这个值可以用在更大的评估环境中。我还为此类型实现了 [`fmt::Display`](https://doc.rust-lang.org/std/fmt/trait.Display.html) ，它负责定义最终显示给用户的输出字符串。使用自动衍生的`Debug`特征，我们得到类似于`Lval::Num(4)`的东西，而使用`Display`我们只得到`4` :

```
impl fmt::Display for Lval {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        match self {
            Lval::Blispr(_cells) => write!(f, "<toplevel>"),
            Lval::Fun(lf) => match lf {
                LvalFun::Builtin(name, _) => write!(f, "<builtin: {}>", name),
                LvalFun::Lambda(_, formals, body) => write!(f, "(\\ {} {})", formals, body),
            },
            Lval::Num(n) => write!(f, "{}", n),
            Lval::Sym(s) => write!(f, "{}", s),
            Lval::Sexpr(cell) => write!(f, "({})", lval_expr_print(cell)),
            Lval::Qexpr(cell) => write!(f, "{{{}}}", lval_expr_print(cell)),
        }
    }
}

fn lval_expr_print(cell: &[Box<Lval>]) -> String {
    let mut ret = String::new();
    for i in 0..cell.len() {
        ret.push_str(&format!("{}", cell[i]));
        if i < cell.len() - 1 {
            ret.push_str(" ");
        }
    }
    ret
} 
```

Enter fullscreen mode Exit fullscreen mode

我们有数字、符号、函数(两种不同类型的函数——稍后会详细介绍),以及两种类型的表达式列表——s 表达式和 q 表达式。s 表达式将作为代码计算，在第一个位置查找函数，而 q 表达式只作为数据列表计算。读入的整个程序将是一个包含`Lval::Sexpr`的大程序，我们只需要对它求值，直到我们得到一个不需要进一步求值的结果，要么是`Num`、`Sym`，要么是`Qexpr`。

举个简单的例子，`"+ 1 2"`将被存储为`Sexpr(Sym("+"), Num(1), Num(2))`。当这个`Sexpr`求值时，它会先在环境中查找`+`，找到一个指向内置加法函数的函数指针:`Sexpr(Fun(Builtin("+"), Num(1), Num("2")))`。那么这个`Sexpr`会被求值为一个函数调用，产生`Num(3)`，不能进一步求值。

这段代码使用了`Box`指针类型，这是一个指向堆分配值的智能指针。因为一个`Lval`可以保存许多不同类型的数据，所以给定的`Lval`的大小在编译时是未知的。通过在堆上只存储指向值的指针，我们可以构建它们的列表。因为这些`Box` es 遵循 Rust 的所有权和借用语义，所以当不再需要它们时，Rust 会设法为我们清理它们。这就是我们在程序的生命周期中管理内存的方式——比相应的 C 语言要简单得多！为了建立一个新的，我们使用一个构造函数。比如:

```
pub fn lval_num(n: i64) -> Box<Lval> {
    Box::new(Lval::Num(n))
} 
```

Enter fullscreen mode Exit fullscreen mode

每个变体都有一个。调用这个函数将在堆上用`Box::new()`分配适当的空间，并返回指针。没有必要使用析构函数——`Box`会尽可能快地自行消失。

包含类型以一个空的`Vec`子元素开始，可以用`lval_add`和`lval_pop` :
操作

```
// Add lval x to lval::sexpr or lval::qexpr v
pub fn lval_add(v: &mut Lval, x: &Lval) -> Result<()> {
    match *v {
        Lval::Sexpr(ref mut children)
        | Lval::Qexpr(ref mut children)
        | Lval::Blispr(ref mut children) => {
            children.push(Box::new(x.clone()));
        }
        _ => return Err(BlisprError::NoChildren),
    }
    Ok(())
}

// Extract single element of sexpr at index i
pub fn lval_pop(v: &mut Lval, i: usize) -> BlisprResult {
    match *v {
        Lval::Sexpr(ref mut children)
        | Lval::Qexpr(ref mut children)
        | Lval::Blispr(ref mut children) => {
            let ret = (&children[i]).clone();
            children.remove(i);
            Ok(ret)
        }
        _ => Err(BlisprError::NoChildren),
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这两个函数都适当地改变了它们的第一个参数，或者删除或者添加一个子元素。

## 错误

与这本书的实现的一个不同之处是，我没有一个单独的特定的 AST 变体来处理我们程序中的错误。相反，我构建了一个单独的错误类型，并在整个过程中利用`Result<T, E>`风格的错误处理:

```
#[derive(Debug)]
pub enum BlisprError {
    DivideByZero,
    EmptyList,
    FunctionFormat,
    NoChildren,
    NotANumber,
    NumArguments(usize, usize),
    ParseError(String),
    ReadlineError(String),
    WrongType(String, String),
    UnknownFunction(String),
} 
```

Enter fullscreen mode Exit fullscreen mode

为了简化通篇使用的类型签名，我有几个类型别名:

```
pub type Result<T> = std::result::Result<T, BlisprError>;
pub type BlisprResult = Result<Box<Lval>>; 
```

Enter fullscreen mode Exit fullscreen mode

大多数评估函数都将返回一个`Result<Box<Lval>, BlisprError>`，现在我只需输入`BlisprResult`。这里或那里少数没有成功类型`Box<Lval>`的函数仍然可以使用这个新的`Result<T>`别名，而不是更冗长的内置`Result<T, E>`，错误类型将自动总是这个`BlisprError`。

为了能够在我们的整个程序中使用它，我为抛出的一些其他类型的错误提供了`impl From<E> for BlisprError`，例如`std::io::Error`和`pest::error::Error`:

```
impl<T> From<pest::error::Error<T>> for BlisprError
where
    T: Debug + Ord + Copy + Hash,
{
    fn from(error: pest::error::Error<T>) -> Self {
        BlisprError::ParseError(format!("{}", error))
    }
}

impl From<std::io::Error> for BlisprError {
    fn from(error: std::io::Error) -> Self {
        BlisprError::ParseError(error.to_string())
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

通过这种方式，我仍然可以在返回其他错误类型的函数调用中使用`?`操作符，这些错误类型在返回`BlisprResult`的函数中，任何返回的错误都将被自动转换为适合我的`BlisprError`。在我们的评估过程中，不是存储特定的错误类型`Lval`来完成整个计算并最终打印出来，所有的错误都是通过类型系统冒泡出来的，但是您仍然会得到完整的`pest`生成的错误:

```
blispr> eval {* 2 3)
Parse error:  --> 1:12
  |
1 | eval {* 2 3)
  |            ^---
  |
  = expected expr 
```

Enter fullscreen mode Exit fullscreen mode

充分披露:为了写`pest::error::Error<T>`块，我只是写了我想要的，即`BlisprError::ParseError(format!("{}", error))`，安抚了编译器。很可能有一个更好的方法来解决这个问题，但它确实有效！

## 解析

这本书使用作者自己的解析器组合子库，名为 [mpc](https://github.com/orangeduck/mpc) 。如果我在 C 语言中处理另一个类似的问题，我可能会再次使用它。然而，Rust 有自己强大的解析生态系统。这个领域的一些重量级人物是 [nom](https://github.com/Geal/nom) 、 [combine](https://github.com/Marwes/combine) 和 [pest](https://github.com/pest-parser/pest) 。在这个项目中，我选择了 pest，尽可能地接近原始资料。鉴于`nom`和`combine`会让你定义自己的[解析器组合子](https://dev.to/deciduously/parser-combinators-are-easy-4bjm)，而`pest`则提供一个 PEG(或[解析表达式语法](https://en.wikipedia.org/wiki/Parsing_expression_grammar))，与你的代码分开。然后，Pest 使用 Rust 强大的定制衍生工具自动创建语法解析。

下面是我为这种语言使用的语法:

```
COMMENT = _{ "/*" ~ (!"*/" ~ ANY)* ~ "*/" }
WHITESPACE = _{ (" " | NEWLINE ) }

num = @{ int }
    int = { ("+" | "-")? ~ digit+ }
    digit = { '0'..'9' }

symbol = @{ (letter | digit | "_" | arithmetic_ops | "\\" | comparison_ops | "&")+ }
    letter = { 'a' .. 'z' | 'A' .. 'Z' }
    arithmetic_ops = { "+" | "-" | "*" | "/" | "%" | "^" }
    comparison_ops = { "=" | "<" | ">" | "!" }

sexpr = { "(" ~ expr* ~ ")" }

qexpr = { "{" ~ expr* ~ "}" }

expr = { num | symbol | sexpr | qexpr }

blispr = { SOI ~ expr* ~ EOI } 
```

Enter fullscreen mode Exit fullscreen mode

这存储在它自己的名为`blispr.pest`的文件中，与源代码放在一起。每行细化一个解析规则。我觉得这非常易读，也很容易调整。从底部开始，我们看到一个有效的`blispr`单元由输入开始(SOI)和输入结束(EOI)之间的一个或多个`expr`组成。一个`expr`是给定的选项中的任意一个。它可以为您处理注释和空白。我也喜欢语法完全独立于任何 Rust 代码的方式。很容易得到这个工作与生锈:

```
use pest::{iterators::Pair, Parser};

#[cfg(debug_assertions)]
const _GRAMMAR: &str = include_str!("blispr.pest");

#[derive(Parser)]
#[grammar = "blispr.pest"]
pub struct BlisprParser; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用`BlisprParser`结构将字符串输入解析到带有`parse()`的解析树中。为了评估它，我们需要建立一个大的`Lval` AST:

```
fn lval_read(parsed: Pair<Rule>) -> BlisprResult {
    match parsed.as_rule() {
        Rule::blispr => {
            let mut ret = lval_blispr();
            read_to_lval(&mut ret, parsed)?;
            Ok(ret)
        }
        Rule::expr => lval_read(parsed.into_inner().next().unwrap()),
        Rule::sexpr => {
            let mut ret = lval_sexpr();
            read_to_lval(&mut ret, parsed)?;
            Ok(ret)
        }
        Rule::qexpr => {
            let mut ret = lval_qexpr();
            read_to_lval(&mut ret, parsed)?;
            Ok(ret)
        }
        Rule::num => Ok(lval_num(parsed.as_str().parse::<i64>()?)),
        Rule::symbol => Ok(lval_sym(parsed.as_str())),
        _ => unreachable!(), // COMMENT/WHITESPACE etc
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将解析树从`pest`传递到`lval_read`，它将递归地为我们构建 AST。该函数查看顶层规则并采取适当的行动，要么分配一个新的`Lval`变量，要么调整的子级。然后，解析树中的每个孩子都作为孩子添加到这个包含`Lval`中，通过`lval_read()`本身将它变成正确的`Lval`。`qexpr`的规则类似，其他规则只是从给定的类型中创建相应的`Lval`。一个奇怪的是`Rule::expr`——这是一种元规则，匹配任何有效的表达式类型，所以它没有自己的 lval，只是包装了一个更具体的类型。我们只是使用`next()`将找到的实际规则传递回`lval_read()`。

包含子元素的变体使用了一个助手，它跳过了括号，只是将实际的子元素添加到新的`Lval` :

```
fn read_to_lval(mut v: &mut Lval, parsed: Pair<Rule>) -> Result<()> {
    for child in parsed.into_inner() {
        if is_bracket_or_eoi(&child) {
            continue;
        }
        lval_add(&mut v, &*lval_read(child)?)?;
    }
    Ok(())
} 
```

Enter fullscreen mode Exit fullscreen mode

`lval_read()`的最终结果将是一个包含整个解析程序的单个`Lval`，保存在`lval_ptr`中。然后我们调用`lval_eval()`，在将这棵树简化到它最求值的形式后，它也将返回一个`BlisprResult`。如果评估成功，我们只打印结果，如果出现任何错误，我们打印错误。

## 环境

在我们深入了解`lval_eval()`的魔力之前，让我们停下来谈谈环境。这就是符号如何能够对应到函数和值——否则`"+"`将只是那个字符，但是我们需要明确地对应到加法函数。

我的想法是否正确，现在还没有定论，但我的处理方式也与书中不同。原始文本让您创建一个保存两个数组和一个计数器的`struct`,一个用于键，另一个用于值。要执行查找，您需要找到该键的索引，然后在 values 中返回该索引处的值。这个结构是在程序进入循环之前构建的，并被手动传递给每个被调用的函数。

相反，我选择了一个 [`HashMap`](https://doc.rust-lang.org/std/collections/struct.HashMap.html) 数据结构，而不是两个具有匹配索引的独立数组:

```
pub type LEnvLookup = HashMap<String, Box<Lval>>;

#[derive(Debug, PartialEq)]
pub struct Lenv<'a> {
    lookup: LEnvLookup,
    pub parent: Option<&'a Lenv<'a>>,
} 
```

Enter fullscreen mode Exit fullscreen mode

`Lenv`本身包含查找表和对父表的引用。

我有一些助手方法来获取、设置和枚举内容:

```
impl Lenv {

 // ..

 pub fn get(&self, k: &str) -> BlisprResult {
        match self.lookup.get(k) {
            Some(v) => Ok(v.clone()),
            None => {
                // if we didn't find it in self, check the parent
                // this will recur all the way up to the global scope
                match &self.parent {
                    None => Err(BlisprError::UnknownFunction(k.to_string())),
                    Some(p_env) => p_env.get(k),
                }
            }
        }
    }

    // Returns an Lval containing Symbols with each k,v pair in the local env
    pub fn list_all(&self) -> BlisprResult {
        let mut ret = lval_qexpr();
        for (k, v) in &self.lookup {
            lval_add(&mut ret, &lval_sym(&format!("{}:{}", k, v)))?;
        }
        Ok(ret)
    }

    // add a value to the local env
    pub fn put(&mut self, k: String, v: Box<Lval>) {
        let current = self.lookup.entry(k).or_insert_with(|| v.clone());
        if *v != **current {
            // if it already existed, overwrite it with v
            *current = v;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

从环境中获取一个值会返回一个全新的`Lval`和一个存储的副本，打印出内容也会返回一个包含与每个条目对应的`Symbol`的现成的`Lval::Qexpr`。在谈了一会儿评估之后，我们将回到初始化。

环境可以选择包含一个父环境，如果在这个环境中查找失败，它将尝试父环境。

## Eval

在`eval_str()`中调用的`lval_eval()`函数是真正的计算发生的地方。这将采用一个`Lval`(也就是一个 AST)，并递归地对其求值，得到最终的`Lval`。大多数类型的`Lval`已经被完全评估过了——但是任何被发现的`S-Expression`都需要被评估，任何`Symbol`都需要在环境中被查找。

在看铁锈之前，我们先用英语分解一下:

1.  检查 Lval 的类型:

    a.Fun | Num | Qexpr -我们完成了-按原样返回 lval。

    b.Symbol -用`Lenv::get()`做一个环境查找-例如，对于`Sym("+")`，看看我们是否有一个名字为`"+"`的函数指针。返回查找结果，该结果已经是一个`Lval`。

    c.评估 S 表达式。

2.  如果我们进行到这一步，我们正在使用 S 表达式。其他的都已经回来了。在进一步之前，用`lval_eval()`对所有孩子进行全面评估。

3.  检查 S 表达式的长度:

    a.0 -空 S 表达式-按原样返回

    b.1 -单个表达式-弹出表达式并返回对其调用`lval_eval()`结果

    c.多个表达式(函数调用)-弹出第一个表达式，并尝试将其用作其余子代的函数

这就是 Rust 中的样子:

```
// Fully evaluate an `Lval`
pub fn lval_eval(e: &mut Lenv, v: &mut Lval) -> BlisprResult {
    let child_count;
    let mut args_eval;
    match v {
        Lval::Blispr(forms) => {
            // If it's multiple, evaluate each and return the result of the last
            args_eval = eval_cells(e, forms)?;
            let forms_len = args_eval.len()?;
            return Ok(lval_pop(&mut args_eval, forms_len - 1)?);
        }
        Lval::Sym(s) => {
            // If it's a symbol, perform an environment lookup
            let result = e.get(&s)?;
            // The environment stores Lvals ready to go, we're done
            return Ok(result);
        }
        Lval::Sexpr(ref mut cells) => {
            // If it's a Sexpr, we're going to continue past this match
            // First recursively evaluate each child with lval_eval()
            // grab the length and evaluate the children
            child_count = cells.len();
            args_eval = eval_cells(e, cells)?;
        }
        // if it's not a sexpr, we're done, return as is
        _ => {
            return Ok(Box::new(v.clone()));
        }
    }
    if child_count == 0 {
        // It was a Sexpr, but it was empty.  We're done, return it
        Ok(Box::new(v.clone()))
    } else if child_count == 1 {
        // Single expression
        lval_eval(e, &mut *lval_pop(v, 0)?)
    } else {
        // Function call
        // We'll pop the first element off and attempt to call it on the rest of the elements
        let fp = lval_pop(&mut args_eval, 0)?;
        lval_call(e, *fp, &mut *args_eval)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在处理表达式本身之前，对 S 表达式的所有子表达式进行完全求值的步骤使用了一个助手:

```
// Given a slice of boxed Lvals, return a single evaluated sexpr
fn eval_cells(e: &mut Lenv, cells: &[Box<Lval>]) -> BlisprResult {
    cells.iter().fold(Ok(lval_sexpr()), |acc, c| {
        match acc {
            Ok(mut lval) => {
                lval_add(&mut lval, &*lval_eval(e, &mut c.clone())?)?;
                Ok(lval)
            }
            // it's just a Result so we can bubble errors out of the fold
            Err(_) => unreachable!(),
        }
    })
} 
```

Enter fullscreen mode Exit fullscreen mode

这被写成一个`fold`，使用一个空的`Lval::Sexpr`作为累加器，使用`lval_add`将每个新的结果添加到其中。

## 函数调用

这让我们几乎一路走到了那里——还缺少最后一步，那就是`lval_call()`。

这种语言有两种功能:内置的和用户自定义的。内置是在 Rust 中实现的，是可执行文件本身的一部分。这些在创建时就存储在环境中:

```
fn add_builtin(&mut self, name: &str, func: LBuiltin) {
    self.put(name.to_string(), lval_builtin(func, name))
}

pub fn new(lookup: Option<LEnvLookup>, parent: Option<&'a Lenv<'a>>) -> Self {
        let mut ret = Self {
            lookup: lookup.unwrap_or_default(),
            parent,
        };

        // Register builtins
        // The "stub" fns are dispatched separately - the function pointer stored is never called
        // these are the ones the modify the environment

        // Definiton
        ret.add_builtin("\\", builtin_lambda);
        ret.add_builtin("def", builtin_put_stub);

        // etc, lots and lots of builtins

        ret.add_builtin("max", builtin_max);

        ret
} 
```

Enter fullscreen mode Exit fullscreen mode

每个名字存储一个指向 Rust 函数的函数指针。这些函数直接操纵 lvals。例如，这是`builtin_head`，它返回一个`Lval::Qexpr`的第一个元素:

```
pub fn builtin_head(v: &mut Lval) -> BlisprResult {
    let mut qexpr = lval_pop(v, 0)?;
    match *qexpr {
        Lval::Qexpr(ref mut children) => {
            if children.is_empty() {
                return Err(BlisprError::EmptyList);
            }
            debug!("builtin_head: Returning the first element");
            Ok(children[0].clone())
        }
        _ => Err(BlisprError::WrongType(
            "qexpr".to_string(),
            format!("{:?}", qexpr),
        )),
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

数学运算都使用同一个函数。它们都接受任意长度的`Lval::Num` s 的列表，并将对运行结果和下一个数字连续应用二元运算，直到列表被消耗:

```
fn builtin_op(mut v: &mut Lval, func: &str) -> BlisprResult {
    let mut child_count;
    match *v {
        Lval::Sexpr(ref children) => {
            child_count = children.len();
        }
        _ => return Ok(Box::new(v.clone())),
    }

    let mut x = lval_pop(&mut v, 0)?;

    // If no args given and we're doing subtraction, perform unary negation
    if (func == "-" || func == "sub") && child_count == 1 {
        let x_num = x.as_num()?;
        return Ok(lval_num(-x_num));
    }

    // consume the children until empty
    // and operate on x
    while child_count > 1 {
        let y = lval_pop(&mut v, 0)?;
        child_count -= 1;
        match func {
            "+" | "add" => {
                apply_binop!(add, x, y)
            }
            "-" | "sub" => {
                apply_binop!(sub, x, y)
            }
            "*" | "mul" => {
                apply_binop!(mul, x, y)
            }
            "/" | "div" => {
                if y.as_num()? == 0 {
                    return Err(BlisprError::DivideByZero);
                } else {
                    apply_binop!(div, x, y)
                }
            }
            "%" | "rem" => {
                apply_binop!(rem, x, y)
            }
            "^" | "pow" => {
                let y_num = y.as_num()?;
                let x_num = x.as_num()?;
                let mut coll = 1;
                for _ in 0..y_num {
                    coll *= x_num;
                }
                x = lval_num(coll);
            }
            "min" => {
                let x_num = x.as_num()?;
                let y_num = y.as_num()?;
                if x_num < y_num {
                    x = lval_num(x_num);
                } else {
                    x = lval_num(y_num);
                };
            }
            "max" => {
                let x_num = x.as_num()?;
                let y_num = y.as_num()?;
                if x_num > y_num {
                    x = lval_num(x_num);
                } else {
                    x = lval_num(y_num);
                };
            }
            _ => unreachable!(),
        }
    }
    Ok(x)
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个很长的函数——但是如果没有我定义的宏，它会更长:

```
macro_rules! apply_binop {
    ( $op:ident, $x:ident, $y:ident ) => {
        match (*$x, *$y) {
            (Lval::Num(x_num), Lval::Num(y_num)) => {
                $x = lval_num(x_num.$op(y_num));
                continue;
            }
            _ => return Err(BlisprError::NotANumber),
        }
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

这使得一些 Lval 类型检查可以更快地输入！它处理确保两个参数都是`Lval::Num`，然后尝试用它们做一些数值上的事情，就像在`apply_binop!(add, x, y)`中一样。这是我第一次接触定义 Rust 宏，这对我很有帮助。

这些很容易调用。因为环境存储了这些 ans 函数指针，所以您可以简单地调用该函数。我的解决方案有点粗糙，因为一些内置函数需要访问一个环境，而内置函数没有——这些特殊情况是单独分派的，其他的都是用`fp()` :
调用的

```
LvalFun::Builtin(name, fp) => match name.as_str() {
        "eval" => builtin_eval(e, args),
        "def" => builtin_def(e, args),
        "printenv" => builtin_printenv(e),
        // Otherwise, just apply the actual stored function pointer
        _ => fp(args),
}, 
```

Enter fullscreen mode Exit fullscreen mode

调用一个`Lambda`有点棘手。我们需要构建一个新的环境，向其中添加任何本地绑定，然后要么调用新的函数，要么返回一个新的、部分应用的 lambda(如果没有给定所有本地的话)。这里的机制是冗长的——参见[这一行](https://github.com/deciduously/blispr/blob/2d8aa15cf7ba8cfc624cf9663fd024dda1df9f72/src/eval.rs#L436)中的代码。

这是我们所有的作品。有了这些,`lval_eval()`可以处理一大堆东西，这种语言实际上已经接近可用。这个语言实现并不完整，但是它是学习语言如何工作的一个很好的平台！
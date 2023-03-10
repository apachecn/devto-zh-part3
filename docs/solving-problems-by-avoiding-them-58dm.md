# 通过回避问题来解决问题

> 原文：<https://dev.to/deciduously/solving-problems-by-avoiding-them-58dm>

我只是通过做一些更容易的事情来避免自己的心痛。

我想。或者，我引入了一个奇怪而脆弱的变通方法，我会后悔的。你有什么直觉来辨别哪个是哪个吗？

我正致力于将(另一个) [orangeduck](http://theorangeduck.com/page/about) 的东西翻译成 Rust，这次是他的[构建你自己的 Lisp](http://www.buildyourownlisp.com/) 的书，这也是 C 语言的一个很好的速成课程。当然，C 和 Rust 在一些重要的方面是不同的，所以翻译有时很简单，有时很不简单。在我们到达[范围的环境](http://www.buildyourownlisp.com/chapter12_functions#parent_environment)之前，我或多或少顺利地跟随这本书。

这篇文章不是我翻译的教程或演练，但是完整的代码可以在[这里](https://github.com/deciduously/blispr)找到。

## 任务

理解我们需要编写的程序的高层次概述是有帮助的。这个程序将一个字符串作为输入，并试图评估结果。我们需要*将字符串解析*为语义标记的词汇记号树，*将这个解析记号树读取*为一个叫做[抽象语法树](https://en.wikipedia.org/wiki/Abstract_syntax_tree)的结构，然后*评估*这个 AST。为此，我们需要从语义上标记每个元素，以便我们的程序可以有条不紊地工作，理解每个部分是什么。

如果你不熟悉这些数据结构，它并没有听起来那么复杂(我把解析工作交给了库[)。举个具体的小例子，我们来看看输入字符串`+ 2 (* 3 4) 5`。为了处理这个输入，我们需要构建一个 AST 结构，如下所示:](https://pest.rs/) 

```
S-Expression(
    Symbol("+"),
    Number(2),
    S-Expression(
        Symbol("*"),
        Number(3),
        Number(4),
    ),
    Number(5),
) 
```

Enter fullscreen mode Exit fullscreen mode

整个程序被表示为一个 S 表达式。当我们的程序看到其中一个有多个元素时，它会尝试将它作为一个函数调用来执行，从第一个位置的符号开始查找函数。不过，首先，它将递归地评估它的所有子元素——因此，如果它们中的任何一个本身是 s 表达式，我们将首先把它们转换成我们可以处理的值。在本例中，将首先对内部 S 表达式`(* 3 4)`求值:

```
S-Expression(
    Symbol("*"),
    Number(3),
    Number(4),
) 
```

Enter fullscreen mode Exit fullscreen mode

这将被解释为函数调用，并计算为:

```
S-Expression(
    Symbol("+"),
    Number(2),
    Number(12),
    Number(5),
) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个操作作为 S 表达式的第一个元素，以及一些应用它的数字。当进行计算时，我们只剩下`Number(19)`，它可以作为计算结果显示给用户。

但是等等！少了一步。那个`Symbol("+")`本身并没有太多意义，它只是一个字符串。我们需要把它和加法函数联系起来。因此，我们添加了一个*环境*的概念，它只是一个将名称和值关联起来的数据结构。这些值可以内置在函数中，如`"+"`或用户定义的值和函数，它们都以相同的方式存储，并在评估过程中查找。

## 这个问题

如果只有一个环境，这是微不足道的。你只要做一个大`HashMap`。我正在翻译的书使用了两个具有匹配索引的数组——关键是，这不是一个复杂的问题。并且一个大的全局环境足以允许变量声明:

```
lisp> def {x} 100
()
lisp> def {y} 200
()
lisp> def {a b} 5 6
()
lisp> + a b x y
311 
```

Enter fullscreen mode Exit fullscreen mode

当我们开始拥有*用户自定义的 lambda*:
时，事情就变得棘手了

```
lisp> def {embiggen} (\ {x y} {^ (* x y) (+ x y)})
()
lisp> embiggen 2 3
7776
lisp> def {real-big} (embiggen 4)
()
lisp> real-big 2
262144 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了两件截然不同的事情。首先，我们现在知道我们需要作用域环境，因为`x`和`y`只应该在`embiggen`内部有意义。如果我们在全局范围之外，在环境中查找`x`应该会失败，如果我们在一个`embiggen`调用中被调用，它应该会找到在调用期间绑定的任何东西，在`embiggen 2 3`的情况下，这将是`2`。

这很好，我们可以通过在评估该调用时构建本地环境，添加这些值，并使用这个新的临时环境进行特定评估来处理这个问题。但是，如果我们希望能够有一个类似于`real-big`的值呢？这个 lambda 在环境中有`x`作为它的定义的一部分，但是在对它求值之后，它没有被存储在它的参数或者它的主体中:

```
lisp>real-big
(\ {y} {^ (* x y) (+ x y)}) 
```

Enter fullscreen mode Exit fullscreen mode

这是一个只有一个参数的函数，但是它也可以查找我们定义的`x`。仅仅用我们在评估时获得的信息构建环境已经不够了，`real-big`当您在环境中查找这些信息时，需要以某种方式保留这些信息。

## 解

在 C 语言中，这个解是一个指针。AST 中的每个函数值携带一个指向环境的指针，每个环境携带一个指向父环境的指针。你只要在适当的地方取消引用，一切都很好。生锈可不那么容易。你可以很好的保存指向父节点的指针:

```
#[derive(Debug, PartialEq)]
pub struct Lenv<'a> {
    lookup: HashMap<String, Box<Lval>>,
    pub parent: Option<&'a Lenv<'a>>,
} 
```

Enter fullscreen mode Exit fullscreen mode

但是啊哦，我们有明确的一生。事实证明，你不能随便把它说成是`Lval` :

```
type LvalChildren = Vec<Box<Lval>>;
pub type LBuiltin = fn(&mut Lval) -> BlisprResult;

#[derive(Clone)]
pub enum LvalFun {
    Builtin(String, LBuiltin), // (name, function pointer)
    Lambda(ENVIRONMENT TYPE?!, Box<Lval>, Box<Lval>), // (env, formals, body)
}

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

这种类型是递归的，在这一千行代码库中到处都有对这些婴儿的可变引用。我经历了*地狱*试图模仿准确的 C 代码。有过一段时间，有过竞技场类型的分配器，有过两次明确的生命周期，那是一片混乱。

然后，我就...没有。我用`HashMap<String, Box<Lval>>`填充了那个疯狂的`ENVIRONMENT TYPE?!`——与环境结构的内部相同，但是没有那个讨厌的引用和它所有的生命周期包袱。

它不会携带环境、父引用和所有的东西，而是只携带任何部分应用的表单。在`real-big`的例子中，这个`HashMap`只有一个条目:`x: 2`。然后由调用代码做一些额外的工作:

```
for (k, v) in env {
    local_env.put(k, v);
} 
```

Enter fullscreen mode Exit fullscreen mode

在大多数情况下，它实际上不会包含太多的值。所以，是的，这是一次减速，但这真的不是什么大不了的减速？它解决了这个问题，这个特性像承诺的那样工作。我没有必要对它有任何深入的了解——相反，我可能避免了更好地理解如何在 Rust 的一般情况下解决这类问题。

但是*事情做了事情*。至少现在，正如我所需要的那样。黑还是不黑？
# 在 Rust 中为你建立一个马尔可夫链(或者别的什么)

> 原文：<https://dev.to/deciduously/build-you-a-markov-chain-in-rust-or-whatever-54mo>

我发现了一个很好的方法来确保我已经完成了一件事，那就是用 Rust 把它写下来。本着这种精神，这篇文章涵盖了由 [orangeduck](http://theorangeduck.com/page/about) 翻译成 Rust 的[这篇文章](http://theorangeduck.com/page/17-line-markov-chain)中的程序，有一个小的不同和一些额外的解释，包括为什么 Rust 是这样写的。根据你的舒适程度，它可能是可节省的，特别是如果你已经有一些铁锈。只需要我们额外的 70 行！

一个[马尔可夫链](https://en.wikipedia.org/wiki/Markov_chain)可以被用来基于一个样本输入生成真实的(有点)听起来随机的文本。维基百科的文章有点不透明，因为维基百科可能倾向于这样，但它的核心是一个非常简单的概念，其中下一个单词完全基于当前的两个单词来选择。它出奇的简单(至少，我对它的简单程度感到惊讶),而且不费吹灰之力就生成了一些听起来很真实的文本。关于这个有趣的例子，请查看 subreddit[/r/subreddit simulator](https://www.reddit.com/r/SubredditSimulator/)。那里找到的所有帖子和评论都是使用马尔可夫链生成的，使用它们各自的子编辑作为输入数据。

# 各就各位

如果你只是为了马尔可夫链算法而不是 Rust，请跳到**马尔可夫中的“算法”!**一节。

本项目要求稳定[锈](https://rustup.rs/)。需要的话去那里拿，然后旋起来一个项目:

```
$ cargo new markov
$ cd markov/ 
```

Enter fullscreen mode Exit fullscreen mode

# 获取设置

在开始之前，一个快速的“n”dirty CLI 将很适合使用不同的输入。幸运的是，Rust 在 [structopt](https://github.com/TeXitoi/structopt) 中有一个很好的选项。从项目根目录:

我喜欢使用来自快速实验的`cargo-add`:来自任何目录的`$ cargo install cargo-add`来添加货物。

```
$ cargo add structopt 
```

Enter fullscreen mode Exit fullscreen mode

顾名思义，这个 crate 通过简单地定义一个 struct 就可以很容易地定义一个接口。它使用宏来处理所有需要的代码生成。将以下内容添加到`src/main.rs`的顶部:

```
use std::{error::Error, path::PathBuf, str::FromStr};
use structopt::StructOpt;

#[derive(StructOpt, Debug)]
#[structopt(name = "markov")]
struct Opt {
    /// Input text file
    #[structopt(short = "i", long = "input")]
    input: Option<PathBuf>,
    /// Output length
    #[structopt(short = "l", long = "length")]
    length: Option<u32>,
} 
```

Enter fullscreen mode Exit fullscreen mode

我们正在自动衍生两个[特征](https://doc.rust-lang.org/book/ch10-02-traits.html)、`StructOpt`和`Debug`。后者就像来自 Java 的`toString()`,它创建了结构的字符串表示，StructOpt one 将为我们提供类似`from_args()`的方法，以便从命令行参数自动实例化它。它还利用了一个特殊的定制标签`#[structopt]`，用于配置这个宏的行为。

带有三个斜线的 doc 注释最终会出现在这个箱子为我们生成的帮助字符串中。这种结构的一个示例格式类似于`./markov -i poetry.txt -l 500`。可以用`cargo run -- -i poetry.txt -l 500`直接用货。长名字用两个破折号，像`--length`。

每个字段都有一个类似于`Option<T>`的类型，这意味着如果在程序被调用时省略了任何一个，这个结构将只保存一个`None`。如果你不习惯这种语法，任何时候你看到一个大写字母，它代表一个通用类型。您使用的真实值将被明确地输入，例如`Option<String>`。

一个`PathBuf`是一个奇特的`String`，内置了[跨平台路径抽象](https://doc.rust-lang.org/std/path/index.html)。你可以`push()`到它们，并在 Rust 代码中以同样的方式遍历它们，不管它运行在哪个平台上。

否则，这只是一个普通的老 Rust 结构，它只是为我们生成了一个奇特的定制`impl StructOpt {}`块。

现在，将`main()`中给出的模板`println!`调用替换为:

```
fn main() {
    let opt = Opt::from_args();
    let filename = opt
        .input
        .unwrap_or_else(|| PathBuf::from_str("poetry.txt").unwrap());
    let length = opt.length.unwrap_or(350);

    if let Err(e) = run(filename, length) {
        eprintln!("Error: {}", e);
        ::std::process::exit(1);
    };
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你不是生锈的新手，这可能很好。如果是的话，我们来解开一点。

首先，我们从命令行传递的任何内容生成结构本身。在第`let opt = Opt::from_args()`行中，`Opt`是我们刚刚定义的结构，显示了它奇特的代码生成`from_args()`方法。如果这个程序作为上面的`cargo run -- -i poetry.txt -l 350`例子被调用，我们现在已经在变量`opt` :
中存储了

```
Opt(
    input: Some(PathBuf(inner: "poetry.txt")),
    length: Some(350u32),
) 
```

Enter fullscreen mode Exit fullscreen mode

所有内存中的数据结构都将呈现在 [RON](https://github.com/ron-rs/ron) 中。

注意`PathBuf`的内脏被省略了——如果你好奇的话，它是一个 [`OsString`](https://doc.rust-lang.org/std/ffi/struct.OsString.html) ，但是我们只是关心它是一个`PathBuf`。

首先要做的是从这些选项中获取一些更具体的东西，并传递给程序。使用`unwrap_or_else()`是一个很好的方法。如果值是`Some(thing)`，它返回`thing`，如果是`None`，它调用传递的闭包，它必须是这两个中的一个。如果你只需要一个默认值而不是函数调用，你可以使用`unwrap_or()`。

我们用来将默认的`"poetry.txt"` `&str`值转换成`PathBuf`值的`from_str`调用是`FromStr`特征的一部分，只有当该特征在范围内时才起作用。这是一个可能失败的操作——例如，路径不正确——所以它返回一个`Result<T, E>`。如果你熟悉的话，这个类型就像 Haskell 中的`Either`,它或者包含一个`Ok(something: T)`或者一个`Err(error: E)`值。如果你确定你将有一个成功的`Ok`返回值，你可以得到那些带有`unwrap()`的`T`。我们知道这个不会失败，因为我们刚刚自己输入，它不是一个格式错误的路径，只是一个带有扩展名的文件名。如果你没有一些有效的东西，这将导致混乱和崩溃。使用像`unwrap_or()`或[模式匹配](https://doc.rust-lang.org/book/ch06-02-match.html)这样的东西来干净利落地处理替代方案几乎总是更好！

接下来，我们将两者都传递给一个经过错误检查的函数。在你的程序中尽可能多地利用 Rust 的错误处理是一个很好的实践——这是一个强制的好方法！`if let`语法是捕捉任何错误的一种方式。我们这里的`run()`函数也将返回一个`Result<T, E>`——这有点像 Rust 中的主题。这有点像嵌入在类型系统中的大尝试/捕捉。当在一个`if let`中被调用时，如果它最终返回一个`Ok(_)`，什么都不会发生。如果里面的任何东西在任何时候返回任何类型的错误(稍后会详细介绍)，我们将执行这个 If 块中的代码路径。它使用了析构——这一行是说如果`run()`的返回值可以被析构成一个`Err(e)`，运行这段代码。唯一可供选择的变体是`Ok(val)`——在这种情况下，我们知道一切都很好，并且不需要采取任何行动。如果我们想做别的事情，我们也可以包含一个`else {}`块。该错误捕获将使用`eprintln!`显示在`stderr`上返回的特定错误信息，并以错误代码 1 结束程序，表明它没有成功。

当然，现在我们需要一个正确类型的`run()`函数。这里有一个存根，只是让我们编译:

```
fn run(input: PathBuf, length: u32) -> Result<(), Box<dyn Error>> {
    Ok(())
} 
```

Enter fullscreen mode Exit fullscreen mode

我们程序的核心期望具体的值，而不是`Option<T>`，并且像好的负责任的 Rustaceans 一样，我们返回一个`Result<T, E>`，特别是一个`Result<(), Box<dyn Error>`。我们的成功类型，`()`代表`unit`，它是空元组，类似于`void`。这个演示只是将我们的随机文本输出到`stdout`，没有值返回。如果您想存储生成的文本并将其传递给程序的另一部分，这可能看起来像`Result<String, Box<dynError>>`。我们用于错误类型的`Box<dyn Error>`类型值得多解释一下。

一个 [`Box<T>`](https://doc.rust-lang.org/std/boxed/index.html) 是一个装箱值——一个类型为`T`的基本堆分配值。确切地说，`Box`是一个指向它的指针，但是是一个知道所有权和借贷的非常聪明的指针。它有一个很大的名字，但它只是一个指针，没有别的。这很有用，因为`Box`在编译时有一个已知的大小，即使它所指向的值可能不知道。带有`dyn Trait`语法的盒子里的东西是一个 [*trait 对象*](https://doc.rust-lang.org/book/ch17-02-trait-objects.html) 。`Error` from `std::error`是许多不同类型的更具体的错误类型实现的特征。使用`dyn Error`,我们覆盖了任何实现`Error`特征的类型。这使得我们可以在一个函数中轻松地传递和捕捉所有不同类型的错误。

如果你是 Rust 的新手，这有点太轻松了，你将会在这篇文章的范围之外享受真正的待遇，但是不要担心——这部分对于理解下面的 Markov 比特是不必要的！这只是一些不需要太多设置就能进行干净愉快的错误处理的 Rust 样板文件。

让我们开始吧！查看帮助字符串是否使用:

```
$ cargo run -- -h
    Finished dev [unoptimized + debuginfo] target(s) in 0.04s
     Running `target/debug/markov -h`
markov 0.1.0
you <you@you.cool>

USAGE:
    markov [OPTIONS]

FLAGS:
    -h, --help       Prints help information
    -V, --version    Prints version information

OPTIONS:
    -i, --input <input>      Input text file
    -l, --length <length>    Output length 
```

Enter fullscreen mode Exit fullscreen mode

太棒了。谢谢，结构选项。别忘了:

```
$ git init
$ git add .
$ git commit -m "Initial commit" 
```

Enter fullscreen mode Exit fullscreen mode

# 马氏！

## 算法

这种文本生成方法的思想是完全基于当前的两个单词来选择下一个单词，只使用在源文本中出现在它们之后的单词。这种算法一次从不关心两个以上的单词——它只知道特定单词 duo 后面所有可能的选项。在我们开始吐出美丽的废话之前，我们需要对输入进行分类。

我们可以在 Rust 中用一个[散列表](https://doc.rust-lang.org/std/collections/struct.HashMap.html)作为查找表来实现这一点。但是，一个单词并不能给我们足够的上下文来进行真实的生成，所以这个散列表的键实际上是两个单词的组合。这些键可以是实现 [`Eq`](https://doc.rust-lang.org/std/cmp/trait.Eq.html) 和 [`Hash`](https://doc.rust-lang.org/std/hash/trait.Hash.html) 特征的任何类型，两个[字符串片段](https://doc.rust-lang.org/book/ch04-03-slices.html#string-slices) `(&str, &str)`的元组就可以了。然后，我们将存储源文本中这两个词组合之后的每个词的相应值。这样，我们可以根据文本中现有的两个单词来查找接下来可能出现的单词。

这里有一个具体的例子，如果我们的散列表是从源文本“熊很大，熊很毛茸茸，熊很强壮”构建的，它看起来会是什么样子:

```
{
    ("bears", "are"): ["big", "furry", "strong"],
    ("and", "bears"): ["are"],
    ("are", "big"): ["and"],
    ("are", "furry"): ["and"],
    ("big", "and"): ["bears"],
    ("furry", "and"): ["bears"],
} 
```

Enter fullscreen mode Exit fullscreen mode

这个源文本不会提供非常有趣的输出，但是它演示了如何在更大范围内工作。首先，你在源文本中随机选择一个点。我们选“熊是”(随便选，我保证)。经历几次迭代:

1.  输出:“熊是”。查`("bears", "are")`。从三个存储的选项中随机选择“furry ”,并将其附加到输出中。

2.  输出:“熊是毛茸茸的”。查`("are", "furry")`。存储的唯一选项是“和”。追加到输出。

3.  输出:“熊是毛茸茸的”。查`("big", "and")`，追加唯一选项“熊”。

4.  输出:“熊是毛茸茸的，是熊”。查`("and, bears")`，追加“是”。

5.  输出:“熊是毛茸茸的，熊是”。查`("bears", "are")`。从三个存储的选项中随机选择“强”。

6.  输出:“熊是毛茸茸的，熊是强壮的”。查`("are", "strong")`，追加“and”。

诸如此类。我们生成一个类似于源文本的任意长度的随机输出字符串。只要你输入的文本是有意义的，这些单词就会一个接一个地变得有意义，并且实际上会模仿这种风格。现在花一点时间回到 [orangeduck python post](http://theorangeduck.com/page/17-line-markov-chain) 去抓取他创作的诗集。挺大的(超过 180 万行！)并作为 zip 文件分发。将它解压到您的项目根目录中，名为`poetry.txt`。它很棒，因为它有几种不同的语言和几种诗歌风格，所以连续的运行通常会给你一些非常独特的东西。

我们随机生成的文本中的下一个单词将总是从这个单词查找表中取出，这个单词查找表跟随我们在真实文本中的当前两个单词，这将(经常)导致听起来真实的句子串在一起，即使每次循环都只知道它的确切位置，其他什么都不知道。在每一次迭代中，我们执行适当元组的查找，并随机选择存储在那里的选项之一。冲洗并重复所需的文本长度！嘣，废话。源文本越大，输出越有趣。

## 读进去

建立这种关系的第一步是阅读原文。首先，调整您的`std`导入:

```
use std::{
    collections::HashMap, error::Error, fs::OpenOptions, io::Read, path::PathBuf, str::FromStr,
}; 
```

Enter fullscreen mode Exit fullscreen mode

这个函数将接受一个`PathBuf`(我们已经从用户那里收集到了)并尝试以字符串的形式返回文件的内容:

```
fn read_file(filename: PathBuf) -> Result<String, Box<dyn Error>> {
    let mut file = OpenOptions::new().read(true).open(filename)?;
    let mut contents = String::new();
    file.read_to_string(&mut contents)?;
    Ok(contents)
} 
```

Enter fullscreen mode Exit fullscreen mode

这种返回类型是我们所熟悉的——例如，如果在指定的路径下没有文件，这个操作就会失败，所以我们将它包装在一个`Result<T, E>`中。这里的`T`是成功读取的`String`，我们的`E`是捕捉任何和所有可能抛出的错误类型的漂亮特征对象。

我们需要变异的任何变量都必须用`mut`明确地标记出来。默认情况下，任何改变存储在`let`绑定中的值的尝试都将无法编译。

在第一行中，它试图以只读权限打开传入路径中的文件。它的结尾有一个问号，这是一种简单的说法，如果返回值是一个`Ok(val)`就自动解包，并用`Err(the error returned)`提前返回这个函数。相当得心应手！扩展看起来会像:

```
let file = match OpenOptions::new().read(true).open(filename) {
    Ok(f) => f,
    Err(e) => return Err(e),
} 
```

Enter fullscreen mode Exit fullscreen mode

就语法糖而言，这是相当合理的行为。不过，它只在返回`Result<T, E>`的函数内部工作，这证明了所有这些喧嚣的合理性。例如，你不能在`main()`中使用`?`。

来自 [`Read`](https://doc.rust-lang.org/std/io/trait.Read.html) 特征的`read_to_string()`方法也使用`?`来捕捉任何可能发生的错误。如果一切都成功了，我们的源文本就隐藏在这个庞大的字符串中，所以我们可以用`Ok()`把它包装起来，然后开始工作。

说句题外话，我经常本能地伸手去拿 [`BufReader`](https://doc.rust-lang.org/std/io/struct.BufReader.html) 。这是一个用例，它不会帮助我们，实际上可能会减慢我们的速度。我们只是将这个非常大的文件一次性读取到一个`String`中，所以我们宁愿避免缓冲读取会增加的额外分配。

继续并将其插入`run()` :

```
fn run(input: PathBuf, length: u32) -> Result<(), Box<dyn Error>> {
    let file_str = read_file(input)?;
    println!("{}", file_str);
    Ok(())
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你已经准备好了`poetry.txt`，那么`cargo run`现在应该会显示全部内容，至少会显示到你厌倦并打断它的时候。

## 把他们分开

然而，我们不能只处理大量的`String`，我们必须把它分成单个的单词。我们希望为这个操作保留像换行符这样的东西。正如 orangeduck 指出的，在诗歌中，尤其是行尾是输出应该类似的结构的一部分。为此，我们将通过 regex crate 使用一个正则表达式:`$ cargo add regex`。

这里有一个函数将执行这个操作:

```
use regex::Regex;
//..
fn split_words(w: &str) -> Vec<&str> {
    let spaces_re = Regex::new(r" +").unwrap();
    spaces_re.split(w).collect::<Vec<&str>>()
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数将分配一个新的`Vec`，但是在里面我们只存储对原始文件字符串的引用。我们不需要改变输入，只需要看看它就可以建立这个向量。通过引用参数中的字符串，我们不会将输入的所有权从原始绑定中移走。构建这个列表不会导致超出`Vec`结构本身的新的复制或分配。

第一行定义了正则表达式——我没有将整个函数包装在一个`Result`中，而是向编译器(和您)保证`r" +"`构成了一个有效的`Regex`,并在`Regex::new()`返回的`Result`上使用一个普通的旧的`unwrap()`。如果传递一个无效的正则表达式，创建一个新的`Regex`将会返回一个错误，在这种情况下，我们的`unwrap()`调用将会崩溃。我们知道这不会引起恐慌，但是，它只会匹配一个或多个空格，忽略任何其他东西，如制表符和换行符。不同的输入可能需要不同的正则表达式来获得最佳输出。然后我们使用这个正则表达式返回调用`split()`方法的结果，然后`collect()`返回结果 [`Iterator`](https://doc.rust-lang.org/std/iter/index.html) 作为`Vec<&str>`。

## 变得有条理

为了构建查找表，我们希望一次查看三个单词。前两个将用于键，第三个将被追加到可能选项的列表中。也就是说，我们要看第一、第二、第三个单词，然后是第二、第三和第四个单词，然后是第三、第四和第五个单词，依此类推。为此构建一个方便的迭代器的最简洁的方法是在[`itertools`](https://docs.rs/itertools/0.8.0/itertools/)`$ cargo add itertools`中找到的`izip!()`宏。

```
#[macro_use]
extern crate itertools;
//..
fn build_table(words: Vec<&str>) -> HashMap<(&str, &str), Vec<&str>> {
    let mut ret = HashMap::new();
    for (w0, w1, w2) in izip!(&words, &words[1..], &words[2..]) {
        // add w2 to the key (w0, w1)
        let current = ret.entry((*w0, *w1)).or_insert_with(Vec::new);
        current.push(*w2);
    }
    ret
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们需要引入带有`#[macro_use]`标签的`izip!()`宏。然后，我们使用切片来构建具有适当偏移量的子列表。这个`for`循环将遍历源文本中的每一个三个单词的三元组。

在循环内部，我们使用 [`Entry API`](https://doc.rust-lang.org/std/collections/hash_map/struct.HashMap.html#method.entry) 从三元组`("bears", "are")`的前两个单词中查找密钥。如果没有找到这样的键，`or_insert_with()`调用将为我们创建一个空的`Vec`键，这样无论如何我们都可以在下一行把第三个词`push`到它上面。一旦这个循环完成，我们就已经构建了上面步骤中描述的数据结构。

如果你愿意，可以跳过 itertools 依赖项，但是代码看起来有点笨拙——内置的`zip`方法只能压缩两个迭代器，所以你必须调用它两次，然后自己组合所有的东西:

```
fn build_table_no_itertools(words: Vec<&str>) -> HashMap<(&str, &str), Vec<&str>> {
    let mut ret = HashMap::new();
    let all_words = &words[..];
    let offset_1 = &words[1..];
    let offset_2 = &words[2..];
    for (w0, w1, w2) in all_words
        .iter()
        .zip(offset_1.iter())
        .zip(offset_2.iter())
        .map(|((a, b), c)| (a, b, c))
    {
        // add w2 to the key (w0, w1)
        let current = ret.entry((*w0, *w1)).or_insert_with(Vec::new);
        current.push(*w2);
    }
    ret
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数作为一个替代函数工作，不需要外部依赖，但是你牺牲了可读性——在你理解它只是将三个迭代器压缩在一起之前，你需要花更多的时间来观察它。我更愿意加上依赖性，`izip!()`更好。

## 吐出来

现在一切都设置好了，我们可以执行指定数量的查找，并将所有内容连接在一起。我们需要一些东西来开始，所以首先我们将从源文本中选择一个随机的起点。我们还需要一个板条箱来完成这个任务。

```
use rand::{seq::SliceRandom, thread_rng, Rng};
//..
fn run(input: PathBuf, length: u32) -> Result<(), Box<dyn Error>> {
    let file_str = read_file(input)?;
    let words = split_words(&file_str);

    let mut rng = thread_rng();
    let i = rng.gen_range(0, words.len() - 3);

    let mut w0 = words[i];
    let mut w1 = words[i + 1];
    let mut w2 = words[i + 2];
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将在循环中重用这些变量。

不过，在这个实现中有一个问题。正如所写的，`build_table`正在取得我们的`words`向量的所有权。这意味着在我们调用它之后，我们不能再使用`words`。幸运的是，没有什么能阻止我们在造桌子之前选择我们的起始位置*。我们需要直接调用上面设置下面的函数:* 

```
let lookup = build_table(words); 
```

Enter fullscreen mode Exit fullscreen mode

现在生成循环的一切都准备好了:

```
 // each iteration, print current word and then a space, and update our words
    for _ in 0..length {
        // append to output
        print!("{} ", w2);

        // choose the next word
        w2 = &lookup[&(w0, w1)].choose(&mut rng).unwrap();
        w0 = w1;
        w1 = w2;
    } 
```

Enter fullscreen mode Exit fullscreen mode

我们只需打印出存储在`w2`中的内容，添加一个空格，然后使用`w0`和`w1`来查找下一个单词。一旦我们选择了 in，我们需要更新`w0`和`w1`，将光标移到下一个三元组。

这里的`unwrap()`调用也是安全的，因为我们永远不会有一个对应于零长度列表的键。每当我们创建一个新的密钥，我们立即把下面的单词推给它。循环的最后一次迭代覆盖了最后三个单词，所以我们总是能够这样做。

这就是整个程序——用`cargo run --release`启动它。我们提供了相同的默认参数值，所以除非您愿意，否则不需要使用我们定义的命令行选项。

这是迄今为止我最喜欢的诗歌集:

```
$ cargo run --release
   Compiling markov v0.1.0 (/home/ben/code/markov)
    Finished release [optimized] target(s) in 0.58s
     Running `target/release/markov`
An actor experiences
Other peoples lives
Through a metamorphosis of mind

Words sifted through a Forest, beneath the blowing gale,
The waves have now the year of 1897, and on like that.
I can't abear it. I killed last night.

I wonder, 'struth, I wonder if the listener please,
A most important thing;
But Fortune to a thousand times, but I
 Would have him with his prophetic bill.
The great Colosse, erect to Memory;
And what the royal feast!
See here the blue night, railway stations.

The water and fire his courage on despair
And utter dissolution, as the love of slaughter;
Many indeed are the men
With spears gathering at his feet: and my evening hours.

Last evening when it rests,
Leaves to be
Of work may be shared by not crossing the line,
Though that same morning officers and men.

Continues yet the dream 
```

Enter fullscreen mode Exit fullscreen mode

梦还在继续...

完整代码见[本回购](https://github.com/deciduously/markov)。
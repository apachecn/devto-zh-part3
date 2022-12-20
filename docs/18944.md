# 两种功能的故事

> 原文：<https://dev.to/deciduously/a-tale-of-two-functions-44h5>

# 本知道 1 是最孤独的数字

所以，作为一名开发人员，我最大的缺点是我的算法工具箱凭直觉唾手可得。这并不是说我不太熟悉基础知识，至少，但我仍然没有投入必要的时间来立即看到一个问题，并说“哦，这是另一个问题”。至少不是在早上 7 点，在我换班之前，从寒冷的爬山跋涉中热身。代码的出现愚弄了我们所有人。

这是一个关于我如何本能地伸手去拿愚蠢的东西*的故事，即使我知道它是愚蠢的*，而不是花一秒钟去想它，并因此浪费了宝贵的排行榜分数。人性。

这是一个初级的帖子，即使你对 F#/ML 不是很熟悉。

## 博览会

第五天我们比较了连续的字符。如果它们是由相同字母的一个小写字母和一个大写字母组成的*对*,则两者都从集合中删除，否则该过程继续。出了对子就完事了。

我们将以错误的方式开始。因为这是我在 F#中遇到的第五个问题，而且我已经有一段时间没有使用 ML 了，所以我想递归地解决它！万岁！我只是忘了这并不总是意味着同样的事情。

我也有一个交换周——我习惯了轻松的 90 分钟，然后我会关掉，整个早上都在做数字，直到吃午饭，但是这个星期我只有 60 分钟！时间在流逝，但我从第四天开始就兴奋了，我的第一次尝试或多或少地成功了，并且骄傲地去了。幸运的是，我知道如何递归地解决问题，我花了几天时间来消除我身后的语言的不熟悉的边缘，这个问题看起来像一块蛋糕。我第一次不是为了时尚，我是为了那个甜美的答案。

在这个关键的时刻，我忽略了一点(正如许多人很快注意到的)，这只需要一次传球就可以完成。一旦你交换了一对，你应该立即检查*如果你需要再次交换，继续这样做，直到你通过-这就是所有需要的！维奥拉，处理过了。这与[匹配括号](http://people.cs.ksu.edu/~rhowell/DataStructures/stacks-queues/paren.html)的问题没有什么不同——很明显这是想要的解决方案。顺便说一下，问题描述中给出的例子甚至在你面前做了那个操作。你不会错过的。*

不过，我没有这样神奇的飞跃。我错过了。在我的第一直觉中，我只是看到了一个需要做的操作，以及确保它完成的可靠方法。

我知道我想要在我们进行的过程中立刻比较两个元素，以检查它们是否有反应，并且我需要一种方法来告诉它*再次运行*，如果我们进行了更改，以查看是否有任何新的配对出现。毕竟，这种模式在第一天第二部分对我有效:

```
let rec addFreqWithState acc visited whole remaining =
    match remaining with
    | [] -> addFreqWithState acc visited whole whole
    | head::tail ->
      let newval = acc + head
      if Set.contains newval visited then
          newval
      else
          addFreqWithState newval (Set.add newval visited) whole tail 
```

Enter fullscreen mode Exit fullscreen mode

如果你现在还在摇头，很好。你应该害怕。见鬼，我是。我查看了输入字符串——它很大。这件事是要做一吨的工作，我只是知道它之前写任何代码，但我不认为它可能需要那么长时间，我只是回来后，找到一个更好的解决方案后，我得到了我的小快乐星-冬天，阿米利特？

我将只存储我需要的东西作为递归函数的参数——一个表示我们是否完成的布尔值和重新开始的原始字符串。事实上，我会把一个排到另一个里，然后把它们翻出来！多么简单，多么美好。几乎温暖而舒适，就像一杯可口的牛奶。

## 第一次去

我将从构建基本案例开始:

```
let rec reactString altered result input =
    match result with
    | [] -> if altered then reactString false "" (string result |> List.ofSeq) else result 
```

Enter fullscreen mode Exit fullscreen mode

如果它在这次运行中做了任何更改，那么再次重新设置一切，使用新的`result`来创建我们的字符输入列表。如果它没有，那么，你知道，它只是一路运行**,再次运行**,不做任何工作来确定这一点，它最终可以返回该死的结果字符串。

好吧。一个病例已经很疼了，但是时间就是金钱。让我们写另一部分，然后继续。

```
let rec reactString altered result input =
    match result with
    | [] -> if altered then reactString false "" (string result |> List.ofSeq) else result
    | head::next::tail ->
      if doesReact head next then
        reactString true result tail
      else
        reactString altered (result + string head) ([next] @ tail) 
```

Enter fullscreen mode Exit fullscreen mode

我通过析构`input`列表并将它们称为`head`和`next`来得到前两个。我检查他们是否有反应:

```
let doesReact first second =
    (System.Char.ToUpper first = System.Char.ToUpper second) && first <> second 
```

Enter fullscreen mode Exit fullscreen mode

使用 F#一开始就遇到的第一个问题是等式操作符——不是使用`==`和`!=`,而是使用`=`和`<>`。

如果它们有反应，那么我们要确保我们注意到在布尔函数中，我们传递并使用`tail` -我们刚刚检查过的两个之后的所有东西。

我们确实完全超越了我们在结果中创造的任何一双新鞋，但这很酷。下一轮我们会抓住他们的！(oof)。

如果他们没有反应，我们将再次通过`input`循环，但是将它“排干”到`result`中——添加`head`并保持`next`与下一次迭代的输入列表同步。

此时，编译器很好地提醒了我，有些列表只有一个元素，我必须面对这个现实。谢谢，编译时强制正确性！我真的不想去想它，所以我们也将“基本情况”——这是我们的最后一次迭代:

```
 let rec reactString altered result input =
    match input with
    | [] -> if altered then reactString false "" (string result |> List.ofSeq) else result
    | [a] -> if altered then reactString false "" (string result + string a |> List.ofSeq) else result + string a
    | head::next::tail ->
      if doesReact head next then
        reactString true result tail
      else
        reactString altered (result + string head) ([next] @ tail)
    |> Seq.length 
```

Enter fullscreen mode Exit fullscreen mode

它几乎和`[]`一样——它肯定不会反应，所以我们不检查——但是如果需要的话，我们要么把它传递回输入列表，要么把它添加到我们累积的`result`字符串中。

它不漂亮，但也可以。

第一次尝试就做到了，这是我最喜欢 F#的地方。确切地说，不是第一次尝试，但是第一次成功的编译通常会达到我的意思。获得实际问题的答案只需要运行一次，然后在不同的输入排列上运行多次，一次删除特定的字母，然后再试一次，所以这是真正工作发生的地方。它做了我要求它做的事情，我的答案是正确的。

不过，我真的变老了。我开始打开橱柜，我去了洗手间，我和另一个早起的人迈克在大厅聊天。没说完。我去拿邮件，过滤我的电子邮件-什么都没有。

我把笔记本电脑开着放在桌子上。这是台旧笔记本电脑 2011 年末的 Thinkpad。它已经尽力了。诅咒！

它结束了，离工作开始还有 12 分钟。我误解了这个问题——它不想要最优的字母，它想要得到的字符串长度。那次大规模计算的结果，那个微不足道的`'j'`正盯着我，嘲弄我。我不得不再次运行它*。时间一分一秒地过去，我仍然没有得到我需要的东西——尽管我确实有了“正确的答案”。*

 *幸运的是，在两分钟内完成了代码更改。又开始了。

无尽的分分秒秒过去了。早上 8 点来了。我开始工作，每隔几分钟就看一眼，让我的一天井然有序。电话开始响，我的同事们蜂拥而至，电子邮件也开始纷至沓来，直到一个痛苦的小时后，我才回过头来看，它就在那里，一如既往地自鸣得意——那该死的正确答案。哎哟。

## 实现

不到两秒钟。我打开了这个线程，从 [@aspittel](https://dev.to/aspittel) 到了最上面的帖子，并在函数
中加入了两行

```
def react(text):
    stack = [] 
```

Enter fullscreen mode Exit fullscreen mode

*Ohhhhh* 。哦对了。堆成一堆。一瞬间一切都变得如此清晰。但是，唉，时候到了。在我重新投入工作之前，我有一堆合同调整要做。

## 修罗

快进到午餐，我简单地翻译了她的:

[![aspittel profile image](img/4d12e0fc6f35c784894121465e104434.png) ](/aspittel) [ Ali Spittel ](/aspittel) • [<time datetime="2018-12-05T06:43:28Z"> Dec 5 '18 </time> • Edited on <time datetime="2018-12-05T06:51:55Z">Dec 5</time>](https://dev.to/aspittel/comment/7bid) 

愚蠢的行动顺序错误让我走到了这一步🙃从 12 点 20 分开始就错过了条件句的后半部分。这反映了经典的堆栈匹配括号问题。

不过，我的解决方案相当不错:

```
with open('input.txt', 'r') as f:
    text = ''
    for line in f:
        text += line.strip()

def react(text):
    stack = []
    for letter in text:
        last = stack[-1] if stack else None
        if letter != last and (last == letter.upper() or last == letter.lower()):
            stack.pop()
        else:
            stack.append(letter)
    return len(stack)

# A1 print(react(text))

# A2 possibilities = set(text.lower())
print(min(react(text.replace(p, '').replace(p.upper(), '')) for p in possibilities)) 
```

Enter fullscreen mode Exit fullscreen mode

我的看起来几乎一模一样，只是 ML 风格。我将折叠成一个`Array`，而不是 for 循环。这不需要很长时间——也许 5 分钟就能编译好:

```
let reactQuickly input =
    Seq.fold (fun s c ->
      let last = if Array.length s > 0 then Some (Array.last s) else None
      match last with
      | Some x ->
        if c <> x && (x = System.Char.ToUpper c || x = System.Char.ToLower c) then
          Array.sub s 0 (Array.length s - 1)
        else Array.append s [| c |]
      | None -> Array.append s [| c |]) [| |] input
        |> Array.length 
```

Enter fullscreen mode Exit fullscreen mode

虽然我总体上喜欢 ML 类型的语法，甚至超过了我尝试过的大多数其他语言，但相比之下，我不得不说她的 Python 版本看起来非常漂亮和干净。他们做同样的事情。

在每次迭代中，`s`是我们的结果数组——在她的实现中是`stack`。我使用`c`来表示我们正在查看的输入中的字符——有时我更喜欢用`el`来表达我们正在折叠的列表元素。

为了一次得到两个，我们不是向前看，而是向后看堆栈。我们可以在函数中访问它。如果它是空的，我们存储一个`None`,这样我们就知道在第一次迭代时只推送字符串开始的内容，否则我们检查堆栈顶部的当前字符。

我们只是返回累加器的一个子集，而不是`stack.pop`，这具有相同的效果。就这样了。

为了检查它是否有效，我所做的只是将单词`reactString`替换为`reactQuickly`。在那台旧笔记本电脑上，三秒钟就能得到相同的答案，而在我家里的台式机上，不到一秒钟就能得到。

结果是一次传球比多次传球少*。去想想。*

 *完整文件见[此处](https://github.com/deciduously/aoc2018/blob/master/src/Day5/Library.fs)。**
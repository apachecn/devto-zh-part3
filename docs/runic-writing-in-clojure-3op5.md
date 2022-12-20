# Clojure 的北欧古字书写

> 原文：<https://dev.to/vitorhead/runic-writing-in-clojure-3op5>

## 背景

作为一个北欧神话迷，我一直想知道是否有一种方法可以在浏览器上用北欧古字书写。我偶然发现许多翻译者会用符文打印你的文本图像，但很少有人会用选择和复制的可能性来写它，所以我决定尝试一下，尝试建立自己的。谈到古语言，我不是专业人士，但我知道有一些关于古北欧字母(如 elder futhark)翻译成英语的学派。一些人说这些符文有特定的含义，另一些人说每一个都有一个独特的匹配字母，这样就有机会与英语字母表一一匹配。

我需要两样东西:一门编程语言和一个能帮我研究符文的好资源。首先，我选择 Clojure:一种令人惊叹的数据驱动函数式编程语言，它基于 LISP。我不打算在这里介绍基础知识(比如 clojure 101 的帖子)，但是对于那些想要深入 clojure 世界的人来说，这是一本非常好的书。对于第二个，我选择谷歌。聪明的搜索栏把我带到了一个[网站，它有一个强大的工具](https://www.furorteutonicus.eu/germanic/runescribe/index.php):一个*的 run scriber*。

在这一点上，我有两个工具，我需要工艺翻译。事不宜迟，我们开始吧！

## 动起手来

Clojure 的结构非常简洁，有许多工具可以帮助您管理您的解决方案。我们将使用 [Leiningen](http://leiningen.org/) 来创建、运行和构建我们的项目。确保你已经安装了它(网站上有一个关于如何在你的操作系统上安装的指南)。要创建一个项目，请在终端键入以下命令:`lein new app runiclojure`。

在那之后，你会有一个看起来像这样的结构:

```
| .gitignore
| doc
| | intro.md
  | project.clj
| README.md
  | resources
| src
| | runiclojure
  | | | core.clj
  | test
| | runiclojure
| | | core_test.clj 
```

这是你的项目框架。您可以编辑文件`project.clj`,将一些细节(描述、存储库、url 等)添加到您的项目中。它也是添加依赖项和其他库的地方(但是我们现在不需要这么做！)

有了创建的结构和所有的东西，你现在可以打开`src/core.clj`，我们的主要名称空间，也是我们将在这篇文章的剩余部分工作的地方。

编码和组织我们项目的惯用方式是为每个功能创建一个模块(单独的文件)。为了保持简洁，从现在开始所有的东西都在同一个文件中(project.clj ),但是为了保持整洁有序，你可以随意创建其他文件。

## 代码！

首先我们可以定义*拉丁字母*和*符文*。

```
(def  runes  "ᚨᛒᚲᛞᛖᚠᚷᚺᛇᚲᛚᛗᚾᛟᛈᛩᚱᛋᛏᚢᚡᚹᛪᛃᛋᛎ")  (def  alphabet  "abcdefghijklmnopqrstuvwxyz") 
```

我们需要一个函数来查找符文图表中字母表中的每个字母。我确信有很多方法可以做到这一点(模式匹配、case 等)，但我选择的方法是创建一个字典。使用 clojure 的内置`zipmap`我可以创建一个字典，其中的键是拉丁字母，值是符文。

```
(def  runes  "ᚨᛒᚲᛞᛖᚠᚷᚺᛇᚲᛚᛗᚾᛟᛈᛩᚱᛋᛏᚢᚡᚹᛪᛃᛋᛎ")  (def  alphabet  "abcdefghijklmnopqrstuvwxyz")  (def  values  (zipmap  alphabet  runes)) 
```

有了`values`在手，就可以启动`lein repl`开始写一些翻译了。例如，如果我想翻译字母 *a* ，我可以求值`(values "a")`，输出将是 *ᚨ* 。从 clojure 的字典中获取值的一种方法是用键作为参数调用字典。考虑到这一点，我们可以编写一个函数:

```
(defn  translate  [s]  (values  s)) 
```

但是我们不想翻译单个字符，我们想翻译整个单词，对吗？！

把一个词想成一个`string`。一个字符串就是一个字符的集合(一个字符列表)。因此，我们可以使用著名的`map`函数:
来提升我们的表达式

```
(map  translate  "the almighty bread") 
```

在这个表达式中，`translate`函数将应用于字符串中的每个元素，处理回一个新的、翻译后的字符串。这正是我们想要的！

但是，翻译核心还没做。如果我们得到了一个不在我们之前定义的`alphabet`中的字符呢？在 Clojure 中，当我们试图使用一个不存在的键获取一个字典值时，它返回 nil。考虑到这一点，我们可以写:

```
(defn  translate-text  [text]  (map  #(or  (translate  %)  %)  text)) 
```

Clojure 的`or`从左到右求值，返回第一个逻辑真值。这样做，如果输入的文本有一个我们没有映射的字符，它不会尝试翻译，而是返回字符本身。

## 提示用户输入

我们的翻译已经准备好了。我们可以很容易地在一个`lein repl`中导入我们的名称空间，并使用表达式。但那就太简单了！

让我们试着写一个允许用户在终端上书写的函数。

Clojure 的`read-line`函数从流中读取下一行，并将其作为字符串返回。

```
user=>  (read-line)  hello  dev  community!  ;type text  "hello dev community!" 
```

我们可以很容易地获取这个字符串并应用我们的`translate`函数。然而，`read-line`只是提示用户输入一次。如果我们想要更多，我们将不得不用`repeatedly`和`loop` :
来编写我们的函数

```
(loop  [lines  (repeatedly  read-line)]  (let  [line  (first  lines)]  (println  line))) 
```

`repeatedly`接受一个零参数函数(通常有副作用),并返回它的一个无限(或 n)惰性序列。`lines`基本上是`read-line`函数的一个懒惰集合。结合`loop`和`let`结构，我们得到该集合的第一个元素，并提示用户输入。之后，我们只需打印到终端，看看是否一切正常。

```
(defn  -main  [&  args]  (println  "Runic translation!")  (loop  [lines  (repeatedly  read-line)]  (let  [line  (first  lines)]  (when  (validate  line)  (println  (map  translate  (clojure.string/lower-case  line)))  (recur  (next  lines)))))) 
```

这个功能副作用很大(抱歉！).大部分你可能在上一个例子中看到过。`validate`的目的是控制翻译者的生命周期。它只是检查用户发送的文本是否等于“exit ”,如果是，就中断循环。

## 使用翻译器

我们现在可以在项目的文件夹中使用`lein run`运行我们的代码，并在终端检查翻译。

```
vitorhead:~/workspace/runiclojure $ lein run
Runic translation!
hello dev community 
(ᚺ ᛖ ᛗ ᛗ ᛈ   ᛞ ᛖ ᚹ   ᚲ ᛈ ᚾ ᚾ ᚡ ᛟ ᛇ ᚢ ᛋ) 
```

## 最后的考虑

完整的来源可以在本要点中找到[。](https://gist.github.com/vitorhead/03f6045d655d3bc32a93c67cdf6cece1) [这里](https://github.com/vitorhead/runic-clojure)你可以查看我的知识库(它也有一个现场演示)关于这个用 ClojureScript 制作的小项目，它允许你在浏览器中翻译你的符文！

对我来说，编写翻译器是学习 Clojure 的一种简单而有趣的方式。这是一个非常基本的小程序，但是可以看出 Clojure 的实现是多么强大，我希望它向您展示了代码是多么简单和整洁！
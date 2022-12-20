# Java:推断固定宽度文本文件的列宽

> 原文：<https://dev.to/awwsmm/java-infer-column-widths-of-a-fixed-width-text-file-2hh0>

## 分隔文件和定宽文件

包含数据表的平面文本文件通常以两种方式组织:以**分隔的**文件，或者以**固定宽度的**文件。带分隔符的文件使用一个或多个连续字符来分隔每行的表格数据的列(换行符几乎总是用于分隔行)。一种常见的分隔文件格式是 CSV(逗号分隔值)格式:

```
287540,Smith,Jones,Accountant,"$55,000"
204878,Ross,Betsy,Senior Accountant,"$66,000"
208417,Arthur,Wilbur,CEO,"$123,000" 
```

Enter fullscreen mode Exit fullscreen mode

...分隔符有时会出现在一行的某个值中，当出现这种情况时，该值通常会被双引号括起来。引号也可以出现在值中，当它们出现时，会用双引号("")进行转义。 [RFC-4180](https://tools.ietf.org/html/rfc4180) 定义了标准的 CSV 格式。

另一方面，一个固定宽度的文件为每一列强制一个固定的列宽(尽管不是所有的列都必须有相同的宽度)并在左边或右边填充剩余的空间，通常用空格:

```
287540 Smith  Jones  Accountant         $55,000
204878 Ross   Betsy  Senior Accountant  $66,000
208417 Arthur Wilbur CEO               $123,000 
```

Enter fullscreen mode Exit fullscreen mode

这些方法各有利弊。除非值中嵌入了转义符和分隔符，否则带分隔符的文件可能更容易解析。带分隔符的文件也比固定宽度的文件占用更少的空间，因为它不会浪费字节来填充文件。如果你有一个好的正则表达式，解析 CSV 文件会很简单，但是解析一个固定宽度的文件会很困难。或者，用户必须事先知道列宽并将其传递给解析方法，或者该方法必须推断列宽。第二个，自动化程度更高一点，是我更喜欢的一个，所以让我们试着去做吧！

## 将一个文本文件读入一个`List<String>`

我们要做的第一件事是将固定宽度的文件放入一个`List<String>`中。为此，我们简单地为文件获取一个 [`java.io.Reader`](https://docs.oracle.com/javase/8/docs/api/java/io/Reader.html) 作为 [`BufferedReader`](https://docs.oracle.com/javase/8/docs/api/java/io/BufferedReader.html) ，然后反复使用`BufferedReader`的 [`readLine()`](https://docs.oracle.com/javase/8/docs/api/java/io/BufferedReader.html#readLine--) 方法，直到它返回`null`(如果成功读取一行，它返回一个`String`:

```
jshell> String fileName = "src/main/resources/example_sql_windows.txt"
fileName ==> "src/main/resources/example_sql_windows.txt"

jshell> BufferedReader reader = new BufferedReader(new FileReader(fileName))
reader ==> java.io.BufferedReader@2353b3e6

jshell> List<String> lines = new ArrayList<>()
lines ==> []

jshell> String line = null // for use in the loop below
line ==> null

jshell> while ((line = reader.readLine()) != null) lines.add(line)

jshell> int nLines = lines.size() // save this for later
nLines ==> 22 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！轻松点。注意，我们必须实例化一个 [`ArrayList`](https://docs.oracle.com/javase/8/docs/api/java/util/ArrayList.html) ，因为 [`List`](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) 只是一个接口，不能直接实例化。我们也可以使用菱形操作符`<>`来节省一些输入。除此之外，我希望以上代码的其余部分或多或少是简单明了的。现在我们可以通过它们在`lines`列表中的索引来访问文件的行。

## 统计每个字符列中非空白字符的数量

接下来，我们要计算每个字符列中非空白字符的数量(相对于数据列)。“字符列”是文件的单个字符宽的列，而数据列由一个或多个相邻的字符列组成。具有很少非空白字符的字符列很可能是分隔符列(分隔数据列)。为了清楚起见，我将在这里一步一步地解释代码。

首先，我们希望获取文件的每一行，并确定一个字符是否是空白字符。基本上，我们想把我们的`List<String>`转换成一个`List<List<Boolean>>`，其中如果在那一行那个位置的字符是*而不是*一个空白字符，那么内部`List`的每个元素就是`true`。为此，我们首先使用 [`String.toCharArray()`](https://docs.oracle.com/javase/8/docs/api/java/lang/String.html#toCharArray--) 将`String`分解成一个`char[]`数组。(首先，我将使用`lines` ( `lines.get(0)`)的第一行作为占位符，稍后我们将使用一个循环。)

```
jshell> lines.get(0).toCharArray()
$86 ==> char[771] { 'e', 'x', 'e', 'c', ... 
```

Enter fullscreen mode Exit fullscreen mode

此时，我们可以将这个`char[]`转换成一个 [`Stream<Character>`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html) ，用一个 [`CharBuffer.wrap()`](https://docs.oracle.com/javase/8/docs/api/java/nio/CharBuffer.html) 将上面的内容包围起来，然后在得到的`CharBuffer`上调用`chars()`，使用`mapToObj()`等等，但是有一个更有效的方法来实现同样的事情——一个好的、旧的`for`循环:

```
jshell> List<List<Boolean>> charsNonWS = new ArrayList<>() // String line => List<Boolean> line
charsNonWS ==> []

jshell> for (int ll = 0; ll < nLines; ++ll) { // loop over lines read from file
   ...>   charsNonWS.add(new ArrayList<Boolean>()); // add new empty array to List
   ...>   List<Boolean> temp = charsNonWS.get(ll); // save reference to use below
   ...>   for (char ch : lines.get(ll).toCharArray()) // loop over chars in this line
   ...>     temp.add(!Character.isWhitespace(ch)); // true if char is non-whitespace
   ...> }

jshell> charsNonWS
charsNonWS ==> [[true, true, true, true, true, ... 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们想计算每列中非空白字符*的数量，而不是每行的数量。所以在某种意义上，我们需要“旋转”我们的数据。为此，让我们首先找到每行的最大字符列数，然后创建一个该长度的数组。这里，我使用一个`Stream`来保存键入另一个大的`for`循环:* 

```
jshell> int nCharCols = charsNonWS.stream().mapToInt(e -> e.size()).max().orElse(0)
nCharCols ==> 771 
```

Enter fullscreen mode Exit fullscreen mode

`charsNonWS.stream()`将`charsNonWS`从`List<List<Boolean>>`转换为`Stream<List<Boolean>>`。换句话说，`Stream`的每个元素都是文件中的一行，其中的字符分别根据它们是否是空白字符被转换为`false` / `true`值。然后，我们用`mapToInt()`将每个`List<Boolean>`映射到一个单独的`Integer`值。该值是行的长度，以字符数表示，我们通过用`mapToInt(e -> e.size())`将每个`List<Boolean>`映射到它的大小来找到它。最后，我们用`max()`找到`Stream`(现在是`Stream<Integer>`)的最大值。`max()`返回一个 [`Optional`](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html) ，所以我们需要用一个`get()`或类似的东西提取那个值。我选择了一个`orElse(0)`，如果`Stream`出错，它将返回`0`作为最大行长度(以字符为单位)。

因此，文件中任何一行的最大字符数是`771`。现在，让我们创建一个`int[]`并计算每个`771`列中非空白字符的数量:

```
jshell> int[] counts = new int[nCharCols]
counts ==> int[771] { 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, ... , 0, 0, 0, 0, 0, 0, 0, 0 } 
```

Enter fullscreen mode Exit fullscreen mode

`int` s 被初始化为`0`，所以我们在开始使用它之前不需要清除数组。相反，让我们直接计算每列的非空白字符数。

```
jshell> for (List<Boolean> row : charsNonWS) // loop over each "row" ("line" / inner List<Boolean>)
   ...>   for (int cc = 0; cc < row.size(); ++cc) // loop over each "column" (char) in that "row" (line)
   ...>     if (row.get(cc)) ++counts[cc]; // if the char is non-whitespace (true), increment column

jshell> counts
counts ==> int[771] { 4, 4, 4, 2, 4, 4, 4, 4, 2, ... 
```

Enter fullscreen mode Exit fullscreen mode

所以`counts`现在保存了文本文件的每个字符列中非空白字符的数量。

## 推断“空”列

接下来，我们希望了解每列中非空白字符的数量。换句话说，是否有任何列没有非空白字符？还是有最小数量？本质上，我们想要做的是制作一个`counts`的*直方图*。最简单的方法可能是使用另一个`Stream` :

```
jshell> Map<Integer, Long> map = Arrays.stream(counts). // convert int[] to Stream of primitive ints
   ...>   mapToObj(i -> (Integer)i). // convert primitive ints to Integers
   ...>   collect(Collectors.groupingBy( // group the Integers according to...
   ...>     Function.identity(), // their identity (value)
   ...>     Collectors.counting() // and then count the number in each group
   ...>   ))
map ==> {16=10, 0=9, 1=549, 17=113, 18=31, 2=39, 19=7, 3=2, 4=11} 
```

Enter fullscreen mode Exit fullscreen mode

因此，有带有`0`非空白字符的`9`行，带有`1`非空白字符的`549`行，等等。似乎那些`9`“空”字符列分隔了数据列。让我们从上面的映射中以编程方式提取给定字符列中非空白字符的最小数量，用它来定义“空”列:

```
jshell> int emptyColDef = Collections.min(map.keySet())
emptyColDef ==> 0 
```

Enter fullscreen mode Exit fullscreen mode

对于这个应用程序来说，这似乎有点过头了，但是一般来说，像这样自动化是一个好主意。它使您的代码更加健壮，并可在未来的应用程序中重用。上面的代码只是简单地比较了`map`的键(每个字符列的非空白字符数)，找到了最小的一个。

## 查找分隔列

现在，我们可以找到定义(界定)数据列范围的字符列。这些通常是非空白字符最少的列(当空白字符用于填充固定宽度的数据列时)。我们需要这些字符列的索引，所以让我们得到一个`counts`的`Stream`，并将这些值与我们的`emptyColDef` :
进行比较

```
jshell> List<Boolean> emptyCols = Arrays.stream(counts). // convert int[] to Stream of primitive ints
   ...>   mapToObj(n -> n == emptyCol). // convert primitive ints to Booleans
   ...>   collect(Collectors.toList()) // collect in a List
emptyCols ==> [false, false, false, ... 
```

Enter fullscreen mode Exit fullscreen mode

空(定界)列是那些在`emptyCols`中有`true`值的列。为了找到索引，我们简单地循环`emptyCols` :

```
jshell> List<Integer> emptyIndices = new ArrayList<>()
emptyIndices ==> []

jshell> for (int cc = 0; cc < nCharCols; ++cc)
   ...>   if (emptyCols.get(cc)) emptyIndices.add(cc)

jshell> emptyIndices
emptyIndices ==> [38, 89, 120, 151, 352, 553, 592, 631, 670] 
```

Enter fullscreen mode Exit fullscreen mode

上面的`for`循环只是查看`emptyCols`中索引`cc`处的值是否为`true`。如果是，它会将该索引添加到`emptyIndices`中，后者现在保存字符列索引，这些索引在我们的固定宽度文件中对数据列进行定界！最后要做的事情是在`List`的开头添加一个`0`，因为我们将使用相邻的值作为每个数据列的“开始”和“结束”字符列，并且第一个数据列从第`0`个字符
开始

```
jshell> int nDataCols = emptyIndices.size()
nDataCols ==> 9

jshell> emptyIndices.add(0, 0) // add a value 0 at the 0th position in the List

jshell> emptyIndices
emptyIndices ==> [0, 38, 89, 120, 151, 352, 553, 592, 631, 670] 
```

Enter fullscreen mode Exit fullscreen mode

## 解析

最后，我们可以使用`emptyIndices`来解析我们的文件。我们可以在给定的字符索引处拆分每一行，然后做一个 [`String.trim()`](https://docs.oracle.com/javase/8/docs/api/java/lang/String.html#trim--) 来删除前导和/或尾随空白。注意，有些行可能比“标准”行长度(保存元数据或类似的东西)短，所以我们需要在将`String`行分割成子字符串:
之前进行边界检查

```
jshell> List<List<String>> tokens = new ArrayList<>(nLines) // pre-allocate space
tokens ==> []

jshell> for (int ll = 0; ll < nLines; ++ll) { // loop over all lines in file
   ...>   tokens.add(new ArrayList<String>()); // add new List<String> parsed tokens for line
   ...>   List<String> tokensList = tokens.get(ll); // get reference to List to use below
   ...>   String line = lines.get(ll); // get line as String
   ...>   int len = line.length(); // get length of line in characters
   ...>   for (int ii = 1; ii <= nDataCols; ++ii) { // loop over data columns
   ...>     if (len < emptyIndices.get(ii)) break; // check if line is long enough to have next token
   ...>     tokensList.add(line.substring(emptyIndices.get(ii-1), emptyIndices.get(ii)).trim()); // get token
   ...>   }
   ...> }

jshell> tokens
tokens ==> [[execBegan, SampleID, ExperimentID, ...

jshell> tokens.get(7) // for example
$142 ==> [2018-11-04 11:07:16.8570000, 0016M978, test, test, SP -> Gilson, Execution Completed, 2018-11-04 11:07:15.0000000, 2018-11-04 11:09:37.5330000, 2018-11-04 11:07:11.7870000] 
```

Enter fullscreen mode Exit fullscreen mode

漂亮！现在，我们有了一个`List<List<String>>`，它包含(在外层`List`中)分解成(在内层`List`中)`String`标记的文件行，并修剪了前导和尾随空白。我们推断了一个固定宽度的文本文件的列宽并解析了它的内容！下一步，我们可以尝试推断每个令牌中保存的数据类型，也许可以使用类似 my [`Typifier`](https://gist.github.com/awwsmm/56b8164410c89c719ebfca7b3d85870b) 的东西，推断 Java `String`中保存的数据类型

* * *

我希望这个演练是有益的和/或有趣的！如果你有任何意见或问题，请在下面的评论中告诉我。我已经将上面的代码编译成了一个类，并且[也将它发布到了 Gist](https://gist.github.com/awwsmm/dd6a7bd0355d81882fa665b2daf23035) 中。编码快乐！
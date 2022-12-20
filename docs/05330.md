# Java 日常编码问题#003

> 原文：<https://dev.to/awwsmm/java-daily-coding-problem-003-59ef>

[日常编码问题](https://dailycodingproblem.com/)是一个网站，它会每天给你的收件箱发送一个编程挑战。我想向初学者展示如何使用 Java 解决这些问题，所以这将是我的一系列解决方案中的一个。欢迎在评论中把它们挑出来！

### 问题

> 给定一个二叉树的根，实现`serialize(root)`，它将树序列化为一个字符串，以及`deserialize(s)`，它将字符串反序列化回树。
> 
> 例如，给定以下节点类
> 
> ```
> class Node:
>     def __init__(self, val, left=None, right=None):
>         self.val = val
>         self.left = left
>         self.right = right 
> ```
> 
> 以下测试应该通过:
> 
> ```
> node = Node('root', Node('left', Node('left.left')), Node('right'))
> assert deserialize(serialize(node)).left.left.val == 'left.left' 
> ```

### 策略

*剧透！*不要看下面，除非你想看我的解决方案！

* * *

好吧，这显然不是用 Java 写的，因为示例代码和测试是用 Python 给出的。我们应该做的第一件事是用 Java 重写上面的代码。

Python 中的`__init()__`行为类似于——[，但不是 Java 中对象构造器](https://stackoverflow.com/questions/6578487/init-as-a-constructor)的直接模拟。所以我们应该定义一个 Java `Node`类，它的构造函数将一个`val`(值)和两个`Node`作为子对象。

在 Python 示例中，子节点默认为`None`，这也是[的一种，不像 Java](https://stackoverflow.com/questions/19473185/what-is-a-none-value) 中的`null`值。所以我们应该允许`left`和/或`right`成为`null`。

Python [将显式的`self`传递给为对象](https://stackoverflow.com/questions/625083/python-init-and-self-what-do-they-do)定义的任何方法，这与大多数其他编程语言不同。这就是为什么在`__init__()`方法中有一个`self`(四个参数中的第一个)，但是在测试中只有三个参数被传递来创建`node`。在 Java 中，`this`被隐式传递给任何对象的方法，所以我们可以在 Java 版本中去掉它。

这个问题的关键是在我们的 Java `Node`类上请求一个`String Node::toString()`方法，该方法将`Node`转换成对象的序列化(`String`)表示。这个表示应该能够用一个`Node Node::fromString()`方法或者类似的方法直接转换成一个`Node`。这两种方法分别是序列化和反序列化方法。

### 代码

#### 建筑`Node`类

让我们从构建这个由提示:
定义的基本类`Node`开始

```
public class Node {

  private int val;
  private Node left;
  private Node right;

  public Node (int val, Node left, Node right) {

    this.val = val;
    this.left = left;
    this.right = right;

  }

} 
```

Enter fullscreen mode Exit fullscreen mode

这可以很容易地在`jshell` :
中实例化

```
jshell> /open Node.java

jshell> Node n = new Node(3, null, null)
n ==> Node@f6c48ac 
```

Enter fullscreen mode Exit fullscreen mode

我认为这是这个类最简单的实现，它满足了提示的要求。注意，`val`必须有某种类型，所以我在上面做了一个`int`。我们可以用
使节点通用

```
public class Node<T> {

  private T val;
  private Node<T> left;
  private Node<T> right;

  public Node (T val, Node<T> left, Node<T> right) {

    this.val = val;
    this.left = left;
    this.right = right;

  }

} 
```

Enter fullscreen mode Exit fullscreen mode

尽管现在我们必须明确说明`Node` :
中保存的数据类型

```
jshell> /open Node.java

jshell> Node<Integer> n = new Node<>(3, null, null)
n ==> Node@14bf9759 
```

Enter fullscreen mode Exit fullscreen mode

由于每个 Java 类都是`Object`的子类，如果负担太重，我们可以将所有的`Node`声明为`Node<Object>`。(但是如果我们要那样做，我们也可以只做`val` `Object`的类型，而放弃泛型。)

总之，回到手头的任务。让我们创建一个序列化方法，我们称之为`toString()`(Java 标准)。默认情况下，对一个对象调用的`toString()`返回[该对象的类型及其散列](https://stackoverflow.com/questions/4712139/why-does-the-default-object-tostring-include-the-hashcode) :

```
jshell> n.toString()
$13 ==> "Node@5f341870" 
```

Enter fullscreen mode Exit fullscreen mode

我们希望我们的序列化方法包含对`Node`的完整描述，以便在必要时可以从返回的`String`中重构。这里我们遇到了一点麻烦。

除非我们将自己限制在对象的某个子集(比如数字、字符和字符串)上，并且[编写一些代码，可以很容易地将这些对象从它们的`String`表示](https://gist.github.com/awwsmm/56b8164410c89c719ebfca7b3d85870b)中解析出来，否则以这种方式序列化我们的`Node`将会非常困难。

虽然提示中没有明确说明，但是测试中只使用字符串作为数据。我认为如果我们限制自己只使用`String val` s 是公平的。在这种情况下，我们可以再次重写我们的`Node` :

```
public class Node {

  private String val;
  private Node left;
  private Node right;

  public Node (String val, Node left, Node right) {

    this.val = val;
    this.left = left;
    this.right = right;

  }

} 
```

Enter fullscreen mode Exit fullscreen mode

在示例中，我们可以通过调用`left()`或`right()`方法轻松访问`Node`的`left`(或`right`)子节点。我们也可以用一个`val()`方法得到`val`。再来补充那些:

```
public class Node {

  private String val;
  private Node left;
  private Node right;

  public Node (String val, Node left, Node right) {

    this.val = val;
    this.left = left;
    this.right = right;

  }

  public Node left() { return this.left; }

  public Node right() { return this.right; }

  public String val() { return this.val; }

} 
```

Enter fullscreen mode Exit fullscreen mode

这与`jshell`中的预期一致...

```
jshell> Node n = new Node("3", null, new Node("right", null, null))
n ==> Node@10bdf5e5

jshell> n.left()
$19 ==> null

jshell> n.right()
$20 ==> Node@617faa95

jshell> n.val()
$21 ==> "3" 
```

Enter fullscreen mode Exit fullscreen mode

...但是我们仍然需要那些序列化/反序列化方法。在我们添加它们之前，还有最后一件事要做:虽然 Python 比 Java 更灵活，因为您可以使用命名参数以任何顺序[为方法提供参数，但是在上面的测试中，通过省略`right`子节点，创建了一个只有单个(`left`)子节点的节点。另一个创建时根本没有子节点。让我们添加一些有一个和零个子`Node`的可选构造函数来模拟这种行为:](https://linux.die.net/diveintopython/html/power_of_introspection/optional_arguments.html) 

```
public class Node {

  private String val;
  private Node left;
  private Node right;

  public Node (String val, Node left, Node right) {
    this.val = val;
    this.left = left;
    this.right = right;
  }

  public Node (String val, Node left) {
    this.val = val;
    this.left = left;
    this.right = null;
  }

  public Node (String val) {
    this.val = val;
    this.left = null;
    this.right = null;
  }

  public Node left() { return this.left; }

  public Node right() { return this.right; }

  public String val() { return this.val; }

} 
```

Enter fullscreen mode Exit fullscreen mode

为了避免重复，我们还可以做:

```
public class Node {

  private String val;
  private Node left;
  private Node right;

  public Node (String val, Node left, Node right) {
    this.val = val;
    this.left = left;
    this.right = right;
  }

  public Node (String val, Node left) {
    this(val, left, null);
  }

  public Node (String val) {
    this(val, null, null);
  }

  public Node left() { return this.left; }

  public Node right() { return this.right; }

  public String val() { return this.val; }

} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以创建与示例中相同的节点，使用几乎完全相同的语法:

```
jshell> /open Node.java

jshell> Node node = new Node("root", new Node("left", new Node("left.left")), new Node("right"))
node ==> Node@4e1d422d 
```

Enter fullscreen mode Exit fullscreen mode

#### 序列化

最后，我们可以创建我们的序列化和反序列化方法。先说序列化。我们需要写一个方法，在其中编码关于给定的`Node`、它的`val`以及它的所有孩子和他们的`val`的所有信息。这听起来相当复杂。

在最简单的情况下，我们有一个没有孩子的`Node`(其中`left`和`right`都是`null`)。让我们先解决这个问题。我们可以这样做:

```
 public String toString() {

    StringBuilder sb = new StringBuilder("Node(");

    if (val == null) {
      sb.append("null");

    } else {
      sb.append("\"");
      sb.append(val);
      sb.append("\"");
    }

    if (this.left == null) {
      sb.append(", null");
    }

    if (this.right == null) {
      sb.append(", null");
    }

    sb.append(")");

    return sb.toString();

  } 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我使用了`StringBuilder`而不是`String`，因为当我们将许多小的`String`连接在一起时，它更有性能。

当在`jshell`中创建一个对象时，它使用该对象的`toString()`方法将它打印到终端，所以让我们看看我们的方法如何为一个没有孩子的`Node` :
工作

```
jshell> /open Node.java

jshell> Node node = new Node("test", null, null);
node ==> Node("test", null, null) 
```

Enter fullscreen mode Exit fullscreen mode

看起来不错！现在，当一个`Node` *有*子节点时，我们需要递归地执行这些步骤。在上面的一个`null`的位置，应该印上另一个`Node(...)`。这很容易解决——如果`left`或`right`是非`null`，我们只需在`toString()`方法中调用`left.toString()`或`right.toString()`:

```
 public String toString() {

    StringBuilder sb = new StringBuilder("Node(");

    if (val == null) {
      sb.append("null");

    } else {
      sb.append("\"");
      sb.append(val);
      sb.append("\"");
    }

    if (this.left == null) {
      sb.append(", null");

    } else {
      sb.append(", ");
      sb.append(this.left.toString());
    }

    if (this.right == null) {
      sb.append(", null");

    } else {
      sb.append(", ");
      sb.append(this.right.toString());
    }

    sb.append(")");

    return sb.toString();

  }

} 
```

Enter fullscreen mode Exit fullscreen mode

这是如何工作的？

```
jshell> /open Node.java

jshell> Node node = new Node("test", null, new Node("right", null, null));
node ==> Node("test", null, Node("right", null, null))

jshell> Node node = new Node("root", new Node("left", new Node("left.left")), new Node("right"))
node ==> Node("root", Node("left", Node("left.left", null, ... Node("right", null, null)) 
```

Enter fullscreen mode Exit fullscreen mode

...很好！所以我们已经成功地实现了我们的序列化方法`toString()`。反序列化呢？这有点复杂。

要进行反序列化，我们需要注意一些事情:

*   所有的`val`都是用引号括起来的`null`或`String`
*   所有的`Node`不是`null`就是以`Node(`开头
*   可以有 0、1 或 2 个孩子

这很复杂。序列化方法旨在使输出可读，而不是其他。让我们看看是否可以调整它，使它更容易被反序列化方法解析:

```
 public String toString() {

    StringBuilder sb = new StringBuilder("Node: ");

    if (val == null) {
      sb.append("null");

    } else {
      sb.append("\"");
      sb.append(val);
      sb.append("\"");
    }

    if (this.left == null) {
      sb.append("\n  null");

//    } else {
//      sb.append(", ");
//      sb.append(this.left.toString());
    }

    if (this.right == null) {
      sb.append("\n  null");

//    } else {
//      sb.append(", ");
//      sb.append(this.right.toString());
    }

    sb.append("\n");

    return sb.toString();

  } 
```

Enter fullscreen mode Exit fullscreen mode

现在，输出如下:

```
jshell> /open Node.java

jshell> Node node = new Node("test");
node ==> Node: "test"
  null
  null

jshell> System.out.print(node.toString())
Node: "test"
  null
  null 
```

Enter fullscreen mode Exit fullscreen mode

在这个修订版中，`val`印在`Node:`之后，`left`和`right`子代印在`val`之下。`Node`有了几个孩子会是什么样子？直截了当的方法有用吗？

```
 public String toString() {

    StringBuilder sb = new StringBuilder("Node: ");

    if (val == null) {
      sb.append("null");

    } else {
      sb.append("\"");
      sb.append(val);
      sb.append("\"");
    }

    if (this.left == null) {
      sb.append("\n  null");

    } else {
      sb.append("\n  ");
      sb.append(this.left.toString());
    }

    if (this.right == null) {
      sb.append("\n  null");

    } else {
      sb.append("\n  ");
      sb.append(this.right.toString());
    }

    sb.append("\n");

    return sb.toString();

  } 
```

Enter fullscreen mode Exit fullscreen mode

```
jshell> /open Node.java

jshell> Node node = new Node("test", null, new Node("right"));
node ==> Node: "test"
  null
  Node: "right"
  null
  null

jshell> System.out.print(node.toString())
Node: "test"
  null
  Node: "right"
  null
  null 
```

Enter fullscreen mode Exit fullscreen mode

...哦，不完全是。我们在结尾有太多的空行，并且`right`的子元素没有像预期的那样缩进。我们可以创建第二个接受参数`indent`的`toString()`方法。这可以是子`Node`在输出中应该缩进的级别数。

经过一点调整，我得到了这样的结果:

```
 public String toString() {
    return toString(0);
  }

  public String toString (int indent) {

    String spacer = "  ";
    String bump = String.join("", Collections.nCopies(indent, spacer));

    StringBuilder sb = new StringBuilder(bump);
    sb.append("Node: ");

    bump = bump + spacer;

    if (val == null) {
      sb.append("null");

    } else {
      sb.append("\"");
      sb.append(val);
      sb.append("\"");
    }

    if (this.left == null) {
      sb.append("\n");
      sb.append(bump);
      sb.append("null");

    } else {
      sb.append("\n");
      sb.append(this.left.toString(indent+1));
    }

    if (this.right == null) {
      sb.append("\n");
      sb.append(bump);
      sb.append("null");

    } else {
      sb.append("\n");
      sb.append(this.right.toString(indent+1));
    }

    return sb.toString();

  } 
```

Enter fullscreen mode Exit fullscreen mode

...大概是这样的:

```
jshell> Node node = new Node("test", new Node("left"), new Node(null));
node ==> Node: "test"
  Node: "left"
    null
    null
  Node: null
    null
    null

jshell> System.out.print(node.toString())
Node: "test"
  Node: "left"
    null
    null
  Node: null
    null
    null 
```

Enter fullscreen mode Exit fullscreen mode

这是一个略有不同的序列化方法，有望使反序列化更容易一些。现在，为了反序列化，我们从左到右工作。最左边的`Node`永远是根`Node`。如果我们向右移动两个空格并上下查看该列，我们会发现(在顶部)的`left`子`Node`，它或者是`null`，或者是`Node`本身。在底部，我们找到了`right`子节点`Node`。节点持有的`val`总是在`Node:`标记之后，并且总是用引号括起来的`String`，除非它是`null`。

#### 反序列化

为了反序列化上面的输出，我们需要从左到右解析节点树。在`Node:`标记及其子标记(`left`和`right`)下方找到后，由`Node`持有的`val`出现。让我们再次从最简单的例子开始，一个没有孩子的`Node`:

```
jshell> Node node = new Node("test");
node ==> Node: "test"
  null
  null

jshell> System.out.print(node.toString())
Node: "test"
  null
  null 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们通过简单地查看“`Node:`”之后的所有内容来找到`val`。如果结果子串被引号括起来，我们就把它们去掉。否则，子串必须是`null` :

```
 public static Node fromString (String serialized) {

    String marker = "Node: ";
    int valStart = serialized.indexOf(marker) + marker.length();
    int valEnd   = serialized.indexOf("\n");

    String val = serialized.substring(valStart, valEnd);

    if (val.charAt(0) == '"')
      val = val.substring(1, val.length()-1);

    else
      val = null;

    System.out.println("val:");
    System.out.println(val);

    return null;

  } 
```

Enter fullscreen mode Exit fullscreen mode

这将打印:

```
jshell> /open Node.java

jshell> Node node = new Node("test", new Node("left"), new Node(null));
node ==> Node: "test"
  Node: "left"
    null
    null
  Node: null
    null
    null

jshell> node.toString()
$84 ==> "Node: \"test\"\n  Node: \"left\"\n    null\n    null\n  Node: null\n    null\n    null"

jshell> Node.fromString(node.toString())
val:
test
$85 ==> null 
```

Enter fullscreen mode Exit fullscreen mode

所以`val`解析正确！接下来就是解析子节点了。如果他们是`null`，我们添加一个`null`孩子。如果不是，我们递归调用它们的`fromString()`方法！但是他们可能是很远的下一代(例如，`left`可能有 17 代子孙)。那么我们如何找到`right`？

我们知道，在我们的序列化表示中，它正好缩进两个空格，所以我们可以将`String`分成几行，找到在`null`或`Node:`出现之前应该缩进两个空格的两行。

或者，由于第一行之后的所有行都以一个`\n`开头，后面跟有一些空格，我们可以用仅仅`\n`替换所有的`\n`加两个空格的实例，然后删除第一行，然后寻找*不以任何空格开头的两行*。

```
 public static Node fromString (String serialized) {

    String marker = "Node: ";
    int valStart = serialized.indexOf(marker) + marker.length();
    int valEnd   = serialized.indexOf("\n");

    String val = serialized.substring(valStart, valEnd);

    if (val.charAt(0) == '"')
      val = val.substring(1, val.length()-1);
    else val = null;

    String modified = serialized.replaceAll("\n  ", "\n");
    modified = modified.substring(valEnd+1);

    System.out.println(modified);

    return null;
  } 
```

Enter fullscreen mode Exit fullscreen mode

上述操作的结果是将整个序列化输出“降级”两个空格:

```
jshell> Node node = new Node("test", new Node("left"), new Node(null));
node ==> Node: "test"
  Node: "left"
    null
    null
  Node: null
    null
    null

jshell> System.out.print(node.toString())
Node: "test"
  Node: "left"
    null
    null
  Node: null
    null
    null
jshell> Node.fromString(node.toString())
Node: "left"
  null
  null
Node: null
  null
  null
$102 ==> null 
```

Enter fullscreen mode Exit fullscreen mode

现在，从第一行开始，我们有了`left` `Node`，从第一行之后的第一个非缩进行开始，我们有了`right` `Node`。我们需要处理`null`节点，因为我们现在编写的代码会为它们抛出一个错误:

```
 public static Node fromString (String serialized) {

    String marker = "Node: ";
    int valStart = serialized.indexOf(marker);

    if (valStart < 0) return null; 

    valStart += marker.length();
    int valEnd = serialized.indexOf("\n");

    String val = serialized.substring(valStart, valEnd);

    if (val.charAt(0) == '"')
      val = val.substring(1, val.length()-1);
    else val = null;

    String modified = serialized.replaceAll("\n  ", "\n");
    modified = modified.substring(valEnd+1);

    System.out.println(modified);

    return null;
  } 
```

Enter fullscreen mode Exit fullscreen mode

...然后我们需要找到两个非缩进的行，这样我们就可以在`left`和`right`子`Node`上再次运行这个过程。一个非缩进的行将是`null`或`Node`，所以这将是唯一一个`\n`换行符后面紧接着一个`n`或`N` :
的实例

```
jshell> /open Node.java

jshell> Node node = new Node("test", new Node("left"), new Node(null));
node ==> Node: "test"
  Node: "left"
    null
    null
  Node: null
    null
    null

jshell> System.out.print(node.toString())
Node: "test"
  Node: "left"
    null
    null
  Node: null
    null
    null
jshell> Node.fromString(node.toString())
Node: "left"
  null
  null
Node: null
  null
  null
27
$110 ==> null 
```

Enter fullscreen mode Exit fullscreen mode

...所以`right`从字符索引`27`开始。最后，我们可以将子节点分成`right`和`left`节点，并再次递归地对它们运行这个过程(也添加了注释):

```
 public static Node fromString (String serialized) {

    // is this a non-null Node?
    String marker = "Node: ";
    int valStart = serialized.indexOf(marker);

    // if null, return a null Node
    if (valStart < 0) return null;

    // otherwise, get the `val` of the Node
    valStart += marker.length();
    int valEnd = serialized.indexOf("\n");
    String val = serialized.substring(valStart, valEnd);

    // is `val` null?
    if (val.charAt(0) == '"')
      val = val.substring(1, val.length()-1);
    else val = null;

    // de-dent the serialized representation and look for children
    String modified = serialized.replaceAll("\n  ", "\n");
    modified = modified.substring(valEnd+1);

    // at what character does the `right` child start?
    int rightStart = Math.max(
        modified.indexOf("\nN"),
        modified.indexOf("\nn")
      ) + 1;

    // child node `left`
    Node left = null;

    // if `left` is not `null`
    if (modified.substring(0, 4) != "null") 
      left = fromString(modified.substring(0, rightStart));

    // child node `right`
    Node right = null;

    // if `right` is not `null`
    if (modified.substring(rightStart, rightStart+4) != "null")
      right = fromString(modified.substring(rightStart));

    return new Node(val, left, right);
  } 
```

Enter fullscreen mode Exit fullscreen mode

这里它运行在`jshell` :

```
jshell> /open Node.java

jshell> Node node = new Node("test", new Node("left"), new Node(null));
node ==> Node: "test"
  Node: "left"
    null
    null
  Node: null
    null
    null

jshell> System.out.print(node.toString())
Node: "test"
  Node: "left"
    null
    null
  Node: null
    null
    null
jshell> Node copy = Node.fromString(node.toString())
copy ==> Node: "test"
  Node: "left"
    null
    null
  Node: null
    null
    null

jshell> System.out.print(copy.toString())
Node: "test"
  Node: "left"
    null
    null
  Node: null
    null
    null 
```

Enter fullscreen mode Exit fullscreen mode

让我们检查提示中给出的测试示例:

```
jshell> Node node = new Node("root", new Node("left", new Node("left.left")), new Node("right"))
node ==> Node: "root"
  Node: "left"
    Node: "left.left" ...  "right"
    null
    null

jshell> Node.fromString(node.toString()).left().left().val()
$129 ==> "left.left"

jshell> Node.fromString(node.toString()).left().left().val().equals("left.left")
$130 ==> true 
```

Enter fullscreen mode Exit fullscreen mode

果然有效！

### 讨论

这比我最初想象的时间要长得多。我对序列化的第一次尝试导致了——我认为会是——一个非常复杂的反序列化。所以我重构了一个看起来不那么优雅的序列化，它有更容易的反序列化。

我的序列化和反序列化方法都依赖递归来完成工作，我认为这是最好的方法。(你不知道节点树会有多深。)

反序列化方法可能不是最佳的，因为它的编写方式使得[大量数据需要保存在堆栈](https://stackoverflow.com/questions/310974/what-is-tail-call-optimization)中。它不是像写的那样可以优化的尾调用。我认为，理想情况下，我们会希望首先找到嵌套最深的`Node` s，创建它们，然后沿着树向上移动，而不是从上到下移动。对我来说，现在还不清楚我们将如何着手做这件事。

[我在过去用更漂亮的`toString()`实现编写过节点树](https://gist.github.com/awwsmm/16d2357c8e929c327c5e2f57aa08b416)，尽管它们没有序列化，因为所有的数据都没有包含在`String`表示中。这是因为这些节点树通常允许本质上不严格属于 T2 的数据。

最后要注意的一件小事是——因为我使用了`\n`来分隔节点，如果`\n`出现在`val`中的任何地方，就会有问题。应该对输入进行清理，或者添加一个特殊的异常，以便`val`中的`\n`不会破坏反序列化。

* * *

我的日常编码问题解决方案的所有代码都可以在[github.com/awwsmm/daily](https://github.com/awwsmm/daily)获得。

建议？请在评论中告诉我。
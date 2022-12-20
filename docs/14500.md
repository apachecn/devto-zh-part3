# 在不到 5 分钟的时间内用 Java 构建一个流畅的界面

> 原文：<https://dev.to/awwsmm/build-a-fluent-interface-in-java-in-less-than-5-minutes-m7e>

[(跳到精彩部分！)](#good-part)

## 建筑基础类

在 Java 中构建 ***类很容易！*** 您所需要的只是一个文件中的类声明，该文件与类同名(但末尾有一个`.java`)。一个最小的例子就是

```
// MyClassName.java

// class must be public and created in a file called <classname>.java
public class MyClassName {
  // no other code needed!
} 
```

Enter fullscreen mode Exit fullscreen mode

你可以把上面的代码放到一个名为`MyClassName.java`的文件中，然后用`/open`命令:
把它加载到`jshell`中

```
jshell> /open MyClassName.java

jshell> MyClassName j = new MyClassName()
j ==> MyClassName@6c3708b3 
```

Enter fullscreen mode Exit fullscreen mode

...就是这样！因为`MyClassName`(像所有的类一样)扩展了`Object`，如果我们在`jshell`中键入`j.`并按下`tab`键:
，我们就会得到一些内置方法

```
jshell> j.
equals(       getClass()    hashCode()    notify()      notifyAll()   toString()    wait( 
```

Enter fullscreen mode Exit fullscreen mode

## 构建自定义构造函数

当然，这并不令人兴奋。通常，我们会创建自己的*构造函数*来赋予对象某种*状态*(实例变量等)。).让我们添加几个私有变量，我们可以通过构造函数来设置:

```
// MyClassName2.java

// class must be public and created in a file called <classname>.java
public class MyClassName2 {

  private int instanceInt;
  private double instanceDouble;
  private String instanceString;

  public MyClassName2 (int myInt, double myDouble, String myString) {
    this.instanceInt = myInt;
    this.instanceDouble = myDouble;
    this.instanceString = myString;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们试图使用默认的无参数构造函数在`jshell`中创建一个`MyClassName2`的实例，我们会得到一个错误:

```
jshell> /open MyClassName2.java

jshell> MyClassName2 j2 = new MyClassName2()
|  Error:
|  constructor MyClassName2 in class MyClassName2 cannot be applied to given types;
|    required: int,double,java.lang.String
|    found: no arguments
|    reason: actual and formal argument lists differ in length
|  MyClassName2 j2 = new MyClassName2();
|                    ^----------------^ 
```

Enter fullscreen mode Exit fullscreen mode

...这是因为如果没有定义其他构造函数，默认的零参数构造函数是 Java 提供的唯一的*。因为我们在上面定义了三参数(`int`、`double`、`String`)构造函数，所以这是我们目前唯一可以访问的构造函数。所以让我们创建一个`MyClassName2`对象:* 

```
jshell> MyClassName2 j2 = new MyClassName2(42, 3.14, "bonjour le monde!")
j2 ==> MyClassName2@6c3708b3 
```

Enter fullscreen mode Exit fullscreen mode

...太好了！成功了！

## getter 和 Setters

### 吸杂

然而，为了从对象中获取任何东西*，我们将需要一些 *getters* 。再补充那些:* 

```
// MyClassName3.java

// class must be public and created in a file called <classname>.java
public class MyClassName3 {

  private int instanceInt;
  private double instanceDouble;
  private String instanceString;

  public MyClassName3 (int myInt, double myDouble, String myString) {
    this.instanceInt = myInt;
    this.instanceDouble = myDouble;
    this.instanceString = myString;
  }

  // getters are methods, so they have a return type
  public int getMyInt() {
    return this.instanceInt;
  }

  // we use 'this' to refer to the object calling the method
  public double getMyDouble() {
    return this.instanceDouble;
  }

  // ('this' isn't strictly necessary here, but it can make the code clearer)
  public String getMyString() {
    return this.instanceString;
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以用一些参数创建一个对象，并在以后提取这些参数！

```
jshell> /open MyClassName3.java

jshell> MyClassName3 j3 = new MyClassName3(17, 1.618, "hola mundo!")
j3 ==> MyClassName3@335eadca

jshell> j3.getMyInt()
$5 ==> 17

jshell> j3.getMyDouble()
$6 ==> 1.618

jshell> j3.getMyString()
$7 ==> "hola mundo!" 
```

Enter fullscreen mode Exit fullscreen mode

### 设定者

*Setters* 让我们*改变一个对象的状态*。换句话说，它们让我们改变保存在对象实例变量中的值。Setters 只是接受一个参数并将一个实例变量设置为等于该参数的方法。再补充一些:

```
// class must be public and created in a file called <classname>.java
public class MyClassName4 {

  private int instanceInt;
  private double instanceDouble;
  private String instanceString;

  public MyClassName4 (int myInt, double myDouble, String myString) {
    this.instanceInt = myInt;
    this.instanceDouble = myDouble;
    this.instanceString = myString;
  }

  // getters are methods, so they have a return type
  public int getMyInt() {
    return this.instanceInt;
  }

  // we use 'this' to refer to the object calling the method
  public double getMyDouble() {
    return this.instanceDouble;
  }

  // ('this' isn't strictly necessary here, but it can make the code clearer)
  public String getMyString() {
    return this.instanceString;
  }

  public void setMyInt (int newInt) {
    this.instanceInt = newInt;
  }

  public boolean setMyDouble (double newDouble) {
    this.instanceDouble = newDouble;
    return true;
  }

  public String setMyString (String newString) {
    this.instanceString = newString;
    return this.instanceString;
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

Setters 应该(在方法名中)描述他们正在设置的实例变量，并且他们通常只接受一个参数(该参数将被分配给由方法名指示的实例变量)。

但是 setters 关于返回值的哲学分为几个阵营:

*   如果你的 setter *总是成功地让*设置值，有些人会从 setter 方法返回`void`。
*   如果您的 setter 有可能失败，您也可以返回`boolean`，用`true`表示实例变量被成功地赋予了所提供的值
*   或者，您可以在方法末尾返回有问题的实例变量。如果它被成功更改，返回值将反映这一点。

这三种哲学已经依次应用于上面给出的设定者。让我们看看他们的行动:

```
jshell> MyClassName4 j4 = new MyClassName4(19, 2.71828, "hello world!")
j4 ==> MyClassName4@72b6cbcc

jshell> j4.getMyInt(); j4.setMyInt(23); j4.getMyInt()
$10 ==> 19
$12 ==> 23

jshell> j4.getMyDouble(); j4.setMyDouble(1.2345); j4.getMyDouble()
$13 ==> 2.71828
$14 ==> true
$15 ==> 1.2345

jshell> j4.getMyString(); j4.setMyString("aloha honua"); j4.getMyString()
$16 ==> "hello world!"
$17 ==> "aloha honua"
$18 ==> "aloha honua" 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到(在不存在的步骤`$11`中)`setMyInt()`返回 void。`jshell`中没有显示数值。

当值被成功更改时，`setMyDouble()`返回`true`。我们可以看到，在之前(`$13`)和之后(`$15`)的步骤中。

最后，`setMyString()`在方法结束时返回`this.instanceString`的值。由于实例变量已成功更改，返回值反映了这一点。

### 流畅的界面

但是我们可以从一个方法中返回任何我们想要的值！我们没有理由不能从任何一个 setters 返回`-14`或`"r2349jp3giohtnr"`或`null`。我们可以返回的值的种类没有限制。

**那么如果我们返回`this`会发生什么？**

我们试试吧！让我们将我们的`setMyInt()`方法改为返回`this`，这是对调用该方法的对象的引用(在本例中是`setMyInt()`方法):

```
 public MyClassName5 setMyInt (int newInt) {
    this.instanceInt = newInt;
    return this;
  } 
```

Enter fullscreen mode Exit fullscreen mode

为了简洁起见，我删除了类的其余部分，但它应该和`MyClassName4`一样(到处都把`4`改成了`5`)。那么这个方法是怎么运作的呢？嗯，它返回调用该方法的对象，在本例中是一个`MyClassName5`对象，所以我们需要将它作为返回值。让我们试试这个方法，看看会发生什么:

```
jshell> MyClassName5 j5 = new MyClassName5(0, 0.0, "zero")
j5 ==> MyClassName5@5d76b067

jshell> j5.setMyInt(-1)
$21 ==> MyClassName5@5d76b067 
```

Enter fullscreen mode Exit fullscreen mode

看看`jshell`中这两条语句的返回值——它们是一样的！它们都返回对象`MyClassName5@5d76b067`。如果这是真的，我们不应该能够对从`setMyInt()`返回的值调用另一个方法吗？我们试试吧！

```
jshell> j5.getMyInt()
$22 ==> -1

jshell> j5.setMyInt(2).setMyDouble(2.2)
$23 ==> true

jshell> j5.getMyInt(); j5.getMyDouble()
$24 ==> 2
$25 ==> 2.2 
```

Enter fullscreen mode Exit fullscreen mode

看看那里发生了什么！链接方法成功了！但是返回值(在`$23`中)是`true`，因为`setMyDouble()`仍然返回一个`boolean`。如果我们改变所有的设置器来返回一个`MyClassName5`对象，我们应该能够以任何顺序将它们链接在一起！

让我们用返回`MyClassName6`对象的 setters 创建一个`MyClassName6`，类似于我们上面所做的。同样，为了节省空间，我只在这里写 setters:

```
 public MyClassName6 setMyInt (int newInt) {
    this.instanceInt = newInt;
    return this;
  }

  public MyClassName6 setMyDouble (double newDouble) {
    this.instanceDouble = newDouble;
    return this;
  }

  public MyClassName6 setMyString (String newString) {
    this.instanceString = newString;
    return this;
  } 
```

Enter fullscreen mode Exit fullscreen mode

我们试试吧！

```
jshell> MyClassName6 j6 = new MyClassName6(6, 6.6, "six")
j6 ==> MyClassName6@131276c2

jshell> j6.setMyString("seven").setMyDouble(77.77).setMyInt(777)
$28 ==> MyClassName6@131276c2

jshell> j6.getMyString(); j6.getMyDouble(); j6.getMyInt()
$29 ==> "seven"
$30 ==> 77.77
$31 ==> 777 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！为了能够将方法链接在一起，我们所要做的就是返回`this`。返回`this`让我们连续调用一个又一个方法，所有这些方法都将应用于同一个(原始)对象！

与 fluent 接口非常相似的一个想法是*构建器模式*，它的工作方式与上面的 setters 几乎相同，但这是另一篇文章的主题。

如果你有任何问题或批评，请务必在评论中告诉我！*
# 死简单 Python:类

> 原文：<https://dev.to/codemouse92/dead-simple-python-classes-42f7>

喜欢那些文章吗？买这本书！ [***死简单蟒*** 作者杰森·c·麦克唐纳可从无淀粉出版社购得。](https://nostarch.com/dead-simple-python)

* * *

类和对象:许多开发人员的面包和黄油。面向对象编程是现代编程的主要支柱之一，所以 Python 能够做到这一点并不奇怪。

但是如果你在使用 Python 之前已经用其他语言做过面向对象编程，我几乎可以保证你做错了。

坚持你的范式，伙计们，这将是一个颠簸的旅程。

# 正在上课

让我们开一个班，只是为了试试。大多数人都不会对此感到惊讶。

```
class Starship(object):

    sound = "Vrrrrrrrrrrrrrrrrrrrrr"

    def __init__(self):
        self.engines = False
        self.engine_speed = 0
        self.shields = True

    def engage(self):
        self.engines = True

    def warp(self, factor):
        self.engine_speed = 2
        self.engine_speed *= factor

    @classmethod
    def make_sound(cls):
        print(cls.sound) 
```

Enter fullscreen mode Exit fullscreen mode

一旦声明了，我们就可以从这个类中创建一个新的**实例**，或**对象**。所有的成员函数和变量都可以使用**点符号**来访问。

```
uss_enterprise = Starship()
uss_enterprise.warp(4)
uss_enterprise.engage()
uss_enterprise.engines
>>> True
uss_enterprise.engine_speed
>>> 8 
```

Enter fullscreen mode Exit fullscreen mode

> 哇，杰森，我知道这应该是“非常简单”，但我认为你只是让我睡着了。

没什么好惊讶的，对吧？但是再看，不是看什么*在*那里，而是看什么*不在*那里。

你看不出来？好吧，我们来分析一下。看你能否在我之前发现惊喜。

# 申报

我们从类本身的定义开始:

```
class Starship(object): 
```

Enter fullscreen mode Exit fullscreen mode

Python 可能被认为是真正面向对象的语言之一，因为它的设计原则是“一切都是对象”所有其他类都继承自那个`object`类。

当然，大多数 Python 爱好者真的讨厌样板文件，所以在 Python 3 中，我们也可以这么说，称之为好的:

```
class Starship: 
```

Enter fullscreen mode Exit fullscreen mode

就个人而言，考虑到 Python 关于“显式比隐式更好”这句话的禅意，我喜欢第一种方式。我们可以争论到母牛回家，真的，所以让我们弄清楚这两种方法在 Python 3 中做同样的事情，然后继续。

**遗留注释:**如果你想让你的代码在 Python 2 上工作，你*必须*说`(object)`。

# 方法

我要跳到这一行...

```
def warp(self, factor): 
```

Enter fullscreen mode Exit fullscreen mode

显然，那是一个**成员函数**或**方法**。在 Python 中，我们将`self`作为第一个参数传递给每个方法。之后，我们可以拥有任意多的参数，和其他函数一样。

我们实际上不必把第一个论点叫做`self`；不管怎样都一样。但是，出于风格的考虑，我们总是在那里使用名称`self`。不存在违反那条规则的正当理由。

> “但是，但是...你真的*刚刚*自己打破了规则！看到下一个函数了吗？”

```
@classmethod
def make_sound(cls): 
```

Enter fullscreen mode Exit fullscreen mode

您可能还记得，在面向对象编程中，**类方法**是在类(对象)的所有实例之间共享的方法。一个类方法从不涉及成员变量或常规方法。

如果你还没有注意到，我们总是通过点运算符来访问类中的成员变量:`self.`。所以，为了让它变得非常清楚，我们不能在类方法中这样做，我们把第一个参数叫做`cls`。事实上，当一个类方法被调用时，Python 将*类*传递给那个参数，而不是*对象*。

像以前一样，我们*可以*称呼`cls`任何我们想要的东西，但这并不意味着我们*应该*。

对于一个类方法，我们还必须将**装饰器** `@classmethod`放在函数声明的上一行。这告诉 Python 语言你正在创建一个类方法，并且你*不仅仅是用`self`参数的名字来创造*。

上面的那些方法会被叫做类似这样的东西...

```
uss_enterprise = Starship() # Create our object from the starship class 
# Note, we aren't passing anything to 'self'. Python does that implicitly. uss_enterprise.warp(4)

# We can call class functions on the object, or directly on the class. uss_enterprise.make_sound()
Starship.make_sound() 
```

Enter fullscreen mode Exit fullscreen mode

最后两行将以完全相同的方式打印出“vrrrrrrrrrrrrrrrrrrrrrrrrrrr”。(注意我在那个函数里引用了`cls.sound`。)

...

什么？

拜托，你知道当你还是个孩子的时候，你为你想象中的宇宙飞船做音效。不要评判我。

# 类 vs 静态方法

古老的格言是正确的:你不会停止学习，直到你死了。 [Kinyanjui Wangonya](https://dev.to/wangonya) 在评论中指出，不需要将`cls`传递给“静态方法”——我在本文的第一个版本中使用了这个短语。

事实证明，他是对的，我很困惑！

与许多其他语言不同， **Python 区分静态方法和类方法。从技术上来说，它们的工作方式是一样的，因为它们都在对象的所有实例中共享。只有一个关键的区别...**

**静态方法不访问任何类成员；它甚至不在乎它是类的一部分！**因为它不需要访问类的任何其他部分，所以它不需要`cls`参数。

让我们对比一下一个类方法和一个静态方法:

```
@classmethod
def make_sound(cls):
    print(cls.sound)

@staticmethod
def beep():
    print("Beep boop beep") 
```

Enter fullscreen mode Exit fullscreen mode

因为`beep()`不需要访问类，我们可以通过使用`@staticmethod`装饰器使它成为一个静态方法。Python 不会隐式地将类传递给第一个参数，不像它对类方法所做的那样(`make_sound()`)

尽管有这种不同，但两者的调用方式是一样的。

```
uss_enterprise = Starship()

uss_enterprise.make_sound()
>>> Vrrrrrrrrrrrrrrrrrrrrr
Starship.make_sound()
>>> Vrrrrrrrrrrrrrrrrrrrrr

uss_enterprise.beep()
>>> Beep boop beep
Starship.beep()
>>> Beep boop beep 
```

Enter fullscreen mode Exit fullscreen mode

# 初始化器和构造器

每个 Python 类都需要有一个，*只有*一个，`__init__(self)`个函数。这被称为**初始化器**。

```
def __init__(self):
    self.engine_speed = 1
    self.shields = True
    self.engines = False 
```

Enter fullscreen mode Exit fullscreen mode

如果你真的不需要一个初始化器，从技术上讲跳过定义它是有效的，但是这被普遍认为是不好的形式。至少，定义一个空的...

```
def __init__(self):
    pass 
```

Enter fullscreen mode Exit fullscreen mode

虽然我们倾向于像使用 C++和 Java 中的构造函数一样使用它，但是`__init__(self)`不是*而是*构造函数！初始化器负责初始化**实例变量**，稍后我们会详细讨论。

我们很少需要实际定义我们自己的**构造函数**。如果你真的知道你在做什么，你可以重新定义`__new__(cls)`函数...

```
def __new__(cls):
    return object.__new__(cls) 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一下，如果你在寻找**析构函数**，那就是`__del__(self)`函数。

# 变量

在 Python 中，我们的类可以有**实例变量**，它对于我们的对象(实例)是唯一的，还可以有**类变量**(又名**静态变量**)，它们属于类，并且在所有实例之间共享。

我有一个坦白:**我花了几年的时间在 Python 开发上做这个*绝对完全*错了！**来自其他面向对象的语言，我实际上认为我应该这样做:

```
class Starship(object):

    engines = False
    engine_speed = 0
    shields = True

    def __init__(self):
        self.engines = False
        self.engine_speed = 0
        self.shields = True

    def engage(self):
        self.engines = True

    def warp(self, factor):
        self.engine_speed = 2
        self.engine_speed *= factor 
```

Enter fullscreen mode Exit fullscreen mode

代码*工作*，那么这个图有什么问题？再读一遍，看看你是否能弄清楚发生了什么。

*最终危险音乐播放*

也许这会让它变得明显。

```
uss_enterprise = Starship()
uss_enterprise.warp(4)

print(uss_enterprise.engine_speed)
>>> 8
print(Starship.engine_speed)
>>> 0 
```

Enter fullscreen mode Exit fullscreen mode

你发现了吗？

类变量在所有函数之外声明，通常在顶部。另一方面，实例变量在`__init__(self)`函数中声明:例如，`self.engine_speed = 0`。

因此，在我们的小例子中，我们已经声明了一组类变量和一组实例变量，它们具有相同的名称。当访问对象上的一个变量时，实例变量**隐藏类变量**，使其行为如我们所料。然而，我们可以通过打印`Starship.engine_speed`看到，我们在类中有一个单独的类变量，只是占用了空间。说说多余的。

有人猜对了吗？斯隆做到了，并打赌...一万片天蚕叶。看来树懒领先了。令人惊讶的是。

顺便说一下，你*可以*第一次从任何实例方法中声明实例变量，而不是初始化器。然而...你猜对了:*不要*。惯例是总是在初始化器中声明所有的实例变量，只是为了防止奇怪的事情发生，比如一个函数试图访问一个尚不存在的变量。

# 范围:私有和公有

如果您来自另一种面向对象的语言，比如 Java 和 C++，您可能也习惯于考虑**范围**(私有、受保护、公共)及其传统假设:变量应该是私有的，函数应该(通常)是公共的。Getters 和 setters 统治一天！

我也是 C++面向对象编程的专家，我不得不说，我认为 Python 处理范围问题的方法远远优于典型的面向对象范围规则。一旦你掌握了如何用 Python 设计类，这些原则很可能会渗透到你用其他语言的标准实践中...我坚信这是一件好事。

准备好了吗？你的变量实际上不需要私有。

是的，我刚听到后面那个 Java 呆子的咯咯叫声。“但是...但是...我如何阻止开发人员篡改对象的任何实例变量？”

通常，这种担忧建立在三个有缺陷的假设之上。让我们先纠正这些错误:

*   使用你的类的开发人员几乎肯定没有直接修改成员变量的习惯，就像他们没有把叉子插在烤面包机上的习惯一样。

*   如果他们真的用叉子叉烤面包机，众所周知，后果是他们是白痴，而不是你。

*   正如我的 Freenode #python 朋友`grym`曾经说过的，“如果你知道*为什么*你不应该用金属物体来移除烤面包机上粘着的烤面包片，你就可以这么做。”

换句话说，使用您的类的开发人员可能比您更清楚他们是否应该旋转实例变量。

现在，解决了这个问题，我们来看看 Python 中的一个重要前提:**没有真正的“私有”范围**。我们不能只在变量前面贴一个花哨的小关键字就把它私有了。

我们*可以*做的就是在名字前面加一个下划线，就像这样:`self._engine`。

下划线并不神奇。对于任何使用你的类的人来说，这只是一个警告标签:“我建议你不要乱来。我正在用它做一些特别的事情。”

现在，在你把`_`放在所有实例变量名的开头之前，想想变量*实际上是什么*，以及你如何使用它。直接拧一下真的会出问题吗？在我们的示例类中，正如现在所写的，不。这实际上是完全可以接受的:

```
uss_enterprise.engine_speed = 6
uss_enterprise.engage() 
```

Enter fullscreen mode Exit fullscreen mode

还有，注意到它的美丽之处了吗？我们没有编写一个单独的 getter 或 setter！在任何语言中，如果 getter 或 setter 在功能上等同于直接修改变量，它们绝对是一种浪费。这种理念是 Python 成为如此干净的语言的原因之一。

对于不打算在类外使用的方法，也可以使用这种命名约定。

**旁注:**在你离开并避开 Java 和 C++代码中的`private`和`protected`之前，请理解作用域是有时间和地点的。下划线约定是 Python 开发者之间的一种社会契约，而大多数语言都没有类似的东西。因此，如果你使用的是一种有作用域的语言，在 Python 中你会在前面加下划线的变量上使用`private`或`protected`。

## 私人...有几分地

现在，在一个非常罕见的情况下，你可能有一个实例变量*绝对，肯定，永远，永远*应该在类外直接修改。在这种情况下，您可以在变量名称前面加上两个下划线(`__`)，而不是一个。

这实际上并没有使它成为私有的；相反，它执行一种叫做**名称篡改**的操作:它改变变量的名称，在前面添加一个下划线和类名。

以`class Starship`为例，如果我们把`self.shields`改成`self.__shields`，它的名字就会被改成`self._Starship__shields`。

所以，如果你知道这个名字是如何工作的，你仍然可以访问它...

```
uss_enterprise = Starship()
uss_enterprise._Starship__shields
>>> True 
```

Enter fullscreen mode Exit fullscreen mode

重要的是要注意，如果要这样做，你也不能有一个以上的尾随下划线。(`__foo`和`__foo_`会被撕裂，但`__foo__`不会)。但是， [PEP 8 通常不鼓励尾随下划线](https://www.python.org/dev/peps/pep-0008/#descriptive-naming-styles)，所以这是一个有点争议的问题。

顺便说一下，使用双下划线(`__`)名称 mangling 的目的实际上与**和**私有作用域无关；都是为了防止和一些技术场景的名称冲突。事实上，你可能会因为使用了`__`而遭到 Python 忍者的严厉批评，所以要谨慎使用。

# 属性

正如我前面所说的，getters 和 setters 通常是没有意义的。然而，有时他们有一个目的。在 Python 中，我们可以以这种方式使用**属性**,还可以实现一些非常漂亮的技巧！

简单地通过在方法前面加上`@property`来定义属性。

我最喜欢的属性技巧是让一个方法看起来像一个实例变量...

```
class Starship(object):

    def __init__(self):
        self.engines = True
        self.engine_speed = 0
        self.shields = True

    @property
    def engine_strain(self):
        if not self.engines:
            return 0
        elif self.shields:
            # Imagine shields double the engine strain
            return self.engine_speed * 2
        # Otherwise, the engine strain is the same as the speed
        return self.engine_speed 
```

Enter fullscreen mode Exit fullscreen mode

当我们使用这个类时，我们可以把`engine_strain`当作对象的一个实例变量。

```
uss_enterprise = Starship()
uss_enterprise.engine_strain
>>> 0 
```

Enter fullscreen mode Exit fullscreen mode

很美，不是吗？

幸运的是，我们不能用同样的方式用*修改* `engine_strain`。

```
uss_enterprise.engine_strain = 10
>>> Traceback (most recent call last):
>>>   File "<stdin>", line 1, in <module>
>>> AttributeError: can't set attribute 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，这实际上是有意义的，但它可能不是你想要的其他时间。只是为了好玩，让我们也为我们的属性定义一个 setter 至少比那个可怕的错误有更好的输出。

```
@engine_strain.setter
def engine_strain(self, value):
    print("I'm giving her all she's got, Captain!") 
```

Enter fullscreen mode Exit fullscreen mode

我们用装饰器`@NAME_OF_PROPERTY.setter`开始我们的方法。我们还必须接受一个单一的`value`论点(当然是在`self`之后)，除此之外肯定没有别的。您会注意到，在这种情况下，我们实际上并没有对`value`参数做任何事情，这对我们的例子来说很好。

```
uss_enterprise.engine_strain = 10
>>> I'm giving her all she's got, Captain! 
```

Enter fullscreen mode Exit fullscreen mode

那好多了。

正如我前面提到的，我们可以将它们用作实例变量的 getters 和 setters。下面是一个简单的例子:

```
class Starship:
    def __init__(self):
        # snip
        self._captain = "Jean-Luc Picard"

    @property
    def captain(self):
        return self._captain

    @captain.setter
    def captain(self, value):
        print("What do you think this is, " + value + ", the USS Pegasus? Back to work!") 
```

Enter fullscreen mode Exit fullscreen mode

我们只是在这些函数涉及的变量前面加了一个下划线，向其他人表明我们打算自己管理这个变量。这个 getter 非常简单明了，只需要提供预期的行为。设定者是事情有趣的地方:我们击倒任何企图叛乱。船长是不会改变的！

```
uss_enterprise = Starship()
uss_enterprise.captain
>>> 'Jean-Luc Picard'
uss_enterprise.captain = "Wesley"
>>> What do you think this is, Wesley, the USS Pegasus? Back to work! 
```

Enter fullscreen mode Exit fullscreen mode

如果你想创建*职业*属性，那需要你做一些黑客工作。有几个解决方案在网上流传，所以如果你需要这个，去研究它！

如果我不指出的话，一些 Python 爱好者会对我发火，还有另一种不使用 decorators 来创建属性的方法。所以，为了记录在案，这个也可以...

```
class Starship:
    def __init__(self):
        # snip
        self._captain = "Jean-Luc Picard"

    def get_captain(self):
        return self._captain

    def set_captain(self, value):
        print("What do you think this is, " + value + ", the USS Pegasus? Back to work!")

    captain = property(get_captain, set_captain) 
```

Enter fullscreen mode Exit fullscreen mode

(是的，最后一行存在于任何函数之外。)

像往常一样，关于属性的[文档有额外的信息，以及一些关于属性的更好的技巧。](https://docs.python.org/3/library/functions.html?highlight=property#property)

# 继承

最后，我们回到第一行再看一看。

```
 class Starship(object): 
```

Enter fullscreen mode Exit fullscreen mode

还记得为什么会有那个`(object)`吗？我们继承了 Python 的`object`类。啊，遗产！那是它的归属。

```
class USSDiscovery(Starship):

    def __init__(self):
        super().__init__()
        self.spore_drive = True
        self._captain = "Gabriel Lorca" 
```

Enter fullscreen mode Exit fullscreen mode

这里唯一真正神秘的是那条`super().__init__()`线。简而言之，`super()`指的是我们继承的类(在这里是`Starship`)，并调用它的初始化器。我们需要调用这个，所以`USSDiscovery`拥有与`Starship`相同的实例变量。

当然，我们可以定义新的实例变量(`self.spore_drive`)，并重新定义继承的变量(`self._captain`)。

我们实际上可以用`Starship.__init__()`调用那个初始化器，但是如果我们想改变我们继承的东西，我们也必须改变那一行。`super().__init__()`方法最终更干净、更易维护。

**遗留注释:**顺便说一下，如果你使用的是 Python 2，那一行有点难看:`super(USSDiscovery, self).__init__()`。

问之前:可以，可以用`class C(A, B):`做多重继承。它实际上比在大多数语言中工作得更好！不管怎样，但是你可以指望一个令人头疼的[副作用](https://stackoverflow.com/q/3277367/472647)，尤其是在使用`super()`的时候。

# 扶着跟班！

如您所见，Python 类与其他语言略有不同，但是一旦您习惯了它们，它们实际上更容易使用。

但是，如果你已经用 C++或 Java 这样的重类语言编写过代码，并且假设你需要 Python 中的类，我有一个惊喜给你。你真的根本不需要使用类！

在 Python 中，类和对象只有一个目的:**数据封装**。如果您需要将数据和操作数据的函数放在一个方便的单元中，那么类是一个不错的选择。否则，不要打扰！完全由函数组成的模块绝对没有任何问题。

# 回顾

咻！你还和我在一起吗？关于 Python 中的类，你猜到了多少？

让我们回顾一下...

*   `__init__(self)`函数是**初始化器**，那是我们进行所有变量初始化的地方。

*   **方法**(成员函数)必须将`self`作为它们的第一个参数。

*   **类方法**必须将`cls`作为它们的第一个参数，并且在函数定义的上一行有装饰器`@classmethod`。他们可以访问类变量，但不能访问实例变量。

*   **静态方法**类似于类方法，除了它们*没有*将`cls`作为它们的第一个参数，并且在它们前面有装饰器`@staticmethod`。他们不能访问任何类或实例变量或函数。他们甚至不知道自己是这个班级的一员。

*   **实例变量**(成员变量)要先在里面声明**。不像大多数其他面向对象语言，我们不在构造函数之外声明它们。**

*   **类变量**或**静态变量**在任何函数之外声明，并在类的所有实例之间共享。

*   Python 中没有私有成员！在成员变量或方法名前加下划线(`_`)，告诉开发人员不要乱用。

*   如果你在一个成员变量或方法名前面加了两个下划线(`__`)，Python 会用*名称篡改*来改变它的名称。这更多的是为了防止名称冲突，而不是隐藏事情。

*   你可以把任何方法变成一个属性(看起来像一个成员变量)，方法是把装饰器`@property`放在声明的上面。这也可以用来制造*吸气剂*。

*   您可以通过将装饰器`@foo.setter`放在函数`foo`之上，为属性(例如`foo`)创建一个*设置器*。

*   一个类(例如`Dog`)可以以这种方式从另一个类(例如`Animal`)继承:`class Dog(Animal):`。当你这样做的时候，你也应该用行`super().__init__()`开始你的初始化器来调用基类的初始化器。

*   多重遗传是可能的，但它可能会给你带来噩梦。用钳子处理。

像往常一样，我建议您阅读文档以了解更多信息:

*   [Python 教程:类](https://docs.python.org/3/tutorial/classes.html)
*   [Python 参考:内置函数- @classmethod](https://docs.python.org/3/library/functions.html?highlight=classmethod#classmethod)
*   [Python 参考:内置函数- @staticmethod](https://docs.python.org/3/library/functions.html?highlight=staticmethod#staticmethod)
*   [Python 引用:内置函数- @property](https://docs.python.org/3/library/functions.html?highlight=property#property)

准备好写一些 Python 类了吗？就这样吧！

* * *

*感谢`deniska`、`grym` (Freenode IRC `#python`)、 [@wangonya](https://dev.to/wangonya) (Dev)的修改建议。*
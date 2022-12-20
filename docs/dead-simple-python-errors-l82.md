# 非常简单的 Python:错误

> 原文：<https://dev.to/codemouse92/dead-simple-python-errors-l82>

喜欢那些文章吗？买这本书！ [***死简单蟒*** 作者杰森·c·麦克唐纳可从无淀粉出版社购得。](https://nostarch.com/dead-simple-python)

* * *

例外情况。

许多程序员的死敌之一。在许多语言中，我们被训练将**异常**与某种程度的失败联系起来；某处的某样东西被不当使用了。

如果我告诉你你不必害怕例外会怎么样？他们想成为你的朋友，帮你写更好的代码？

Python 提供了许多熟悉的错误处理工具，但是我们使用它们的方式可能与你习惯的方式大不相同，它能帮助你做的不仅仅是清理混乱。你甚至可以说，Python 中的错误处理在内部更强大。

杰罗尼莫！

# 玩接球

以防你对异常不熟悉，让我们从一般的定义开始...

> 异常:(计算)正常处理中的中断，通常由错误条件引起，可由程序的另一部分处理。([维基百科](https://en.wiktionary.org/wiki/exception))

让我们先看一个简单的例子:

```
def initiate_security_protocol(code):
    if code == 1:
        print("Returning onboard companion to home location...")
    if code == 712:
        print("Dematerializing to preset location...")

code = int(input("Enter security protocol code: "))
initiate_security_protocol(code) 
```

Enter fullscreen mode Exit fullscreen mode

```
>>> Enter security protocol code: 712
Dematerializing to preset location...
>>> Enter security protocol code: seven one two
Traceback (most recent call last):
  File "/home/jason/Code/FiringRanges/PyFiringRange/Sandbox/security_protocols.py", line 7, in <module>
    code = int(input("Enter security protocol code: "))
ValueError: invalid literal for int() with base 10: 'seven one two' 
```

Enter fullscreen mode Exit fullscreen mode

显然，这是一个问题。我们不希望我们的程序因为用户输入了一些奇怪的东西而突然崩溃。正如笑话所说...

> 一名质量保证工程师走进一家酒吧。他点了一杯啤酒。他点了五瓶啤酒。他点了 1 瓶啤酒。他点了一只蜥蜴。

我们想防止奇怪的输入。在这种情况下，只有一个重要故障点:函数`int()`。它期望收到一些可以转换成整数的东西，如果没有收到，就会引发一个`ValueError`异常。为了正确处理这一点，我们将代码包装成*可能会在`try...except`块中*失败。

```
try:
    code = int(input("Enter security protocol code: "))
except ValueError:
    code = 0
initiate_security_protocol(code) 
```

Enter fullscreen mode Exit fullscreen mode

当我们再次测试我们的代码时，我们不会再遇到这种失败。如果我们不能从用户那里获得我们需要的信息，我们将使用代码`0`来代替。自然，我们可以重写我们的`initiate_security_protocol()`函数，以不同的方式处理`0`的代码，尽管为了节省时间，我不会在这里展示。

**Gotcha Alert:** 不管什么原因，作为一个多语言程序员，我经常忘记在 Python 中使用`except`，而不是大多数其他语言使用的`catch`语句。我在这篇文章中已经打错了三次(然后马上就改正了。)这只是一个背诵点。谢天谢地，Python *没有*关键字`catch`，这使得语法错误更加明显。如果你懂多种语言，*当*你搞混这些的时候，不要慌。是`except`，不是`catch`。

# 阅读回溯

在我们深入了解`try...except`语句的一些更深层次的细节之前，让我们再次回顾一下那个错误语句。毕竟，如果我们不谈论错误消息，那么一篇关于错误处理的文章又有什么用呢？在 Python 中，我们称之为**回溯**，因为它从涉及的第一行代码到最后一行代码追溯错误的根源。在许多其他语言中，这被称为*堆栈跟踪*。

```
Traceback (most recent call last):
  File "/home/jason/Code/FiringRanges/PyFiringRange/Sandbox/security_protocols.py", line 7, in <module>
    code = int(input("Enter security protocol code: "))
ValueError: invalid literal for int() with base 10: 'seven one two' 
```

Enter fullscreen mode Exit fullscreen mode

我有自下而上阅读这些信息的习惯，因为这有助于我首先获得最重要的信息。如果您查看最后一行，您会看到`ValueError`，这是已经引发的特定异常。具体细节如下:在这种情况下，不可能用`int()`将字符串`'seven one two'`转换成整数。我们还了解到它试图转换成一个基数为 10 的整数，这在其他场景中可能是有用的信息。想象一下，例如，如果那句台词说...

```
ValueError: invalid literal for int() with base 10: '5bff' 
```

Enter fullscreen mode Exit fullscreen mode

如果我们忘记指定基数为 16(如在`int('5bff', 16)`中),而不是缺省值(基数为 10 ),这是完全可能的。简而言之，**您应该始终彻底阅读并理解错误消息的最后一行！**有太多次，我读了一半的信息，花了半个小时追踪错误的 bug，结果发现我忘记了一个参数或者用错了函数。

错误消息的上方是产生错误的代码行(`code = int(input("Enter security protocol code: "))`)。上面是文件的绝对路径(`security_protocols.py`)和行号`7`。语句`in <module>`意味着代码在任何函数之外。在这个例子中，回调只有一个步骤，所以让我们看一些稍微复杂一点的东西。我已经修改并扩展了之前的代码。

```
Traceback (most recent call last):
  File "/home/jason/Code/FiringRanges/PyFiringRange/Sandbox/databank.py", line 6, in <module>
    decode_message("Bad Wolf")
  File "/home/jason/Code/FiringRanges/PyFiringRange/Sandbox/databank.py", line 4, in decode_message
    initiate_security_protocol(message)
  File "/home/jason/Code/FiringRanges/PyFiringRange/Sandbox/security_protocols.py", line 2, in initiate_security_protocol
    code = int(code)
ValueError: invalid literal for int() with base 10: 'Bad Wolf' 
```

Enter fullscreen mode Exit fullscreen mode

我们得到了一个和以前类似的错误——我们试图将一个字符串转换成一个整数，但是没有成功。倒数第二行向我们展示了失败的代码；果然，有一个调用`int()`这是提高这一点。根据上面那一行，这个有问题的代码在`security_protocols.py`的第 2 行，在`initiate_security_protocol()`函数内部。太好了！我们可以停在那里，用一个`try...except`把它包起来。明白为什么自下而上阅读节省时间了吗？

然而，让我们想象它不是那么简单。也许我们没有修改`security_protocols.py`的选项，所以我们需要在执行那个模块之前防止*出现问题。如果我们看接下来的两行，我们会看到在第 4 行的`databank.py`上，在`decode_message()`函数内部，我们正在调用有问题的`initiate_security_protocol()`函数。这个函数在`databank.py`的第 6 行被调用，在任何函数之外，这就是我们传递参数`"Bad Wolf"`给它的地方。*

数据输入不是问题，因为我们想解码信息“坏狼”但是，为什么我们要把我们试图解码的信息传递给安全协议呢？也许我们需要重写这个函数(或者在其他的变化中添加？).如你所见，**回溯**对于理解错误的来源非常重要。养成通读的习惯；许多有用的信息可能隐藏在意想不到的地方。

顺便说一下，第一行每次都是一样的，但是如果你忘记如何阅读这些信息，它会非常有用。**最近执行的代码列在最后。因此，正如我之前说过的，你应该从下往上读。**

# 你的朋友，例外

> "请求原谅比获得许可更容易。"少将·格蕾丝·赫柏

这句话原本是关于采取主动的；如果你相信一个想法，就去冒险，而不是等待别人的许可去追求它。然而，在这种情况下，它很好地描述了 Python 的错误处理哲学:**如果某个东西经常以一种或多种特定的方式失败，通常最好使用`try...except`语句来处理这些情况。**

这种哲学被正式命名为**“请求原谅比允许容易”**，或者**[【EAFP】](https://docs.python.org/3/glossary.html#term-eafp)**。

这有点抽象，所以让我们考虑另一个例子。假设我们希望能够在字典中查找信息。

```
datafile_index = {
    # Omitted for brevity.
    # Just assume there's a lot of data in here. }

def get_datafile_id(subject):
    id = datafile_index[subject]
    print(f"See datafile {id}.")

get_datafile_id("Clara Oswald")
get_datafile_id("Ashildir") 
```

Enter fullscreen mode Exit fullscreen mode

```
See datafile 6035215751266852927.

Traceback (most recent call last):
  File "/home/jason/Code/FiringRanges/PyFiringRange/Sandbox/databank.py", line 30, in <module>
    get_datafile_id("Ashildir")
  File "/home/jason/Code/FiringRanges/PyFiringRange/Sandbox/databank.py", line 26, in get_datafile_id
    id = datafile_index[subject]
KeyError: 'Ashildir' 
```

Enter fullscreen mode Exit fullscreen mode

第一个函数调用工作正常。我们在字典`database_index`中搜索存在的键`"Clara Oswald"`，因此我们返回与之相关的值(`6035215751266852927`)，并在我们可爱的格式化的`print()`语句中打印出该数据。然而，第二个函数调用失败了。引发了异常`KeyError`，因为`"Ashildir"`在字典中不是一个键。

**技术提示:** Python 提供了`collections.defaultdict`作为这个确切问题的另一种解决方案；试图访问一个不存在的键将在字典中创建键/值对，使用一些默认值。然而，因为这是一个演示错误处理的例子，所以我不使用它。

由于我们不可能知道或记住字典中的所有键，特别是在现实世界中，我们需要某种方法来处理试图访问不存在的键的常见情况。您的第一反应可能是在尝试访问字典键之前检查它...

```
def get_datafile_id(subject):
    if subject in datafile_index:
        id = datafile_index[subject]
        print(f"See datafile {id}.")
    else:
        print(f"Datafile not found on {subject}) 
```

Enter fullscreen mode Exit fullscreen mode

在 Python 文化中，这种方法被称为[“三思而后行”【LBYL】](https://docs.python.org/3/glossary.html#term-lbyl)。

但这不是最有效的方法！“原谅，而不是允许”在这里发挥了作用:**我们使用`try...except`** 而不是首先测试。

```
def get_datafile_id(subject):
    try:
        id = datafile_index[subject]
        print(f"See datafile {id}.")
    except KeyError:
        print(f"Datafile not found on {subject}") 
```

Enter fullscreen mode Exit fullscreen mode

这背后的逻辑很简单:我们只访问它一次，而不是访问键两次(“permission”方法)，并使用实际的*异常*作为我们逻辑分支的手段。

**在 Python 中，我们不认为异常是应该避免的事情。**事实上，`try...except`是很多 Python 设计模式和算法的常规部分。不要害怕引发和捕捉异常！事实上，甚至键盘中断也是这样处理的，通过`KeyboardInterrupt`异常。

**Gotcha Alert:** `try...except`是一个强大的工具，但不是万能的。例如，从函数返回`None`通常被认为比引发异常更好。**只有当真正的*错误*发生时才引发异常，最好由调用者来处理。**

# 当心纸尿裤反图案

迟早，每个 Python 开发人员都会发现这一点:

```
try:
    someScaryFunction()
except:
    print("An error occured. Moving on!") 
```

Enter fullscreen mode Exit fullscreen mode

一个裸露的`except`允许你在一个*中捕捉所有的*异常。在他的书[《如何在 Python 中犯错》](https://www.oreilly.com/programming/free/how-to-make-mistakes-in-python.csp)【奥赖利，2018】中，迈克·皮尔纳特称之为**尿布模式**，这是一个非常、*非常*糟糕的想法。我允许他总结一下...

> ...实际错误的所有珍贵背景都被困在尿布中，永远见不到阳光或问题跟踪器的内部。当稍后发生“井喷”异常时，堆栈跟踪指向发生次要错误的位置，而不是 try 块中的实际故障。

长话短说，**您应该总是显式地捕捉特定的异常类型**。任何你无法预见的失败都可能与一些需要解决的错误有关；例如，当你的超级复杂的搜索函数突然开始产生一个`OSError`，而不是预期的`KeyError`或`TypeError`。

和往常一样，Python 的[禅对此有话要说...](https://www.python.org/dev/peps/pep-0020/)

> 错误永远不会无声无息地过去。
> 除非明确沉默。

换句话说，这不是口袋妖怪——你不应该把它们都抓住！

您可以在文章[最邪恶的 Python 反模式](https://realpython.com/the-most-diabolical-python-antipattern/)中详细了解为什么尿布模式是如此糟糕的想法。

# 除了，别的，最后

> 很好，这样我就不会一下子捕捉到所有的异常。那么，我如何处理多种可能的失败呢？

你会很高兴地知道 Python 的`try...except`有比它第一次展示的更多的工具。

```
class SonicScrewdriver:

    def __init__(self):
        self.memory = 0

    def perform_division(self, lhs, rhs):
        try:
            result = float(lhs)/float(rhs)
        except ZeroDivisionError:
            print("Wibbly wobbly, timey wimey.")
            result = "Infinity"
        except (ValueError, UnicodeError):
            print("Oy! Don't diss the sonic!")
            result = "Cannot Calculate"
        else:
            self.memory = result
        finally:
            print(f"Calculation Result: {result}\n")

sonic = SonicScrewdriver()

sonic.perform_division(8, 4)
sonic.perform_division(4, 0)
sonic.perform_division(4, "zero")

print(f"Memory Is: {sonic.memory}") 
```

Enter fullscreen mode Exit fullscreen mode

在向您展示输出之前，请仔细看看代码。你认为三个`sonic.perform_division()`函数调用中的每一个会打印出什么？最终存储在`sonic.memory`里的是什么？看你能不能搞清楚。

你认为你得到了吗？让我们看看你是不是对的。

```
Calculation Result: 2.0

Wibbly wobbly, timey wimey.
Calculation Result: Infinity

Oy! Don't diss the sonic!
Calculation Result: Cannot Calculate

Memory Is: 2.0 
```

Enter fullscreen mode Exit fullscreen mode

你感到惊讶吗，还是你猜对了？我们来分析一下。

当然，是我们试图运行的代码，它可能会也可能不会引发异常。

当我们试图被零除时，就会发生这种情况。在这种情况下，我们说值`"Infinity"`是计算的结果，并打印出一个关于时空连续体性质的恰当信息。

当这两个异常中的一个*异常出现时`except (ValueError, UnicodeError):`发生。每当我们传递的任何参数不能被`float()`转换时，就会发生`ValueError`，而当 Unicode 编码或解码有问题时，就会发生`UnicodeError`。实际上，第二个只是为了说明一个观点；对于所有可信的场景来说,`ValueError`就足够了，在这些场景中，论点不能变成浮点。无论哪种情况，我们都使用值`"Cannot Calculate"`作为结果，并提醒用户不要对硬件提出不合理的要求。*

这就是事情变得有趣的地方。`else:`仅在没有出现异常的情况下运行*。在这种情况下，如果我们的除法计算有一个有效的数值结果，我们实际上想把它存储在内存中；相反，如果我们得到的结果是“无穷大”或“无法计算”，我们会*而不是*存储它。*

 *无论如何，`finally:`段都会运行*。在这种情况下，我们打印出我们的计算结果。*

顺序很重要。我们*必须*遵循模式`try...except...else...finally`。如果存在的话，`else`必须在所有`except`语句之后。`finally`总是最后一个。

最初很容易混淆`else`和`finally`，所以一定要理解它们的区别。 **`else`只有在没有异常的情况下才运行；`finally`每次都运行。**

## `finally`最终如何？

您希望下面的代码做什么？

```
class SonicScrewdriver:

    def __init__(self):
        self.memory = 0

    def perform_division(self, lhs, rhs):
        try:
            result = float(lhs)/float(rhs)
        except ZeroDivisionError:
            print("Wibbly wobbly, timey wimey.")
            result = "Infinity"
        except (ValueError, UnicodeError):
            print("Oy! Don't diss the sonic!")
            result = "Cannot Calculate"
        else:
            self.memory = result
            return result
        finally:
            print(f"Calculation Result: {result}\n")
            result = -1

sonic = SonicScrewdriver()

print(sonic.perform_division(8, 4)) 
```

Enter fullscreen mode Exit fullscreen mode

`else`下的那句`return`应该就是事情的结局了吧？其实不是！如果我们运行这个代码...

```
Calculation Result: 2.0

2.0 
```

Enter fullscreen mode Exit fullscreen mode

从中可以得出两个重要的观察结果:

1.  `finally`正在运行，即使在我们的`return`语句之后。该函数不会像正常情况那样退出。

2.  在`finally`块执行之前，`return`语句确实在运行*。我们知道这一点，因为结果输出是`2.0`，而不是我们在`finally`语句中分配给`result`的`-1`。*

**`finally`每次都会运行，即使你在`try...except`结构的其他地方有一个`return`。**

不过我也是用一个`os.abort()`而不是`return result`来测试上面的，在这种情况下`finally`块从来没有运行过；这个项目彻底中止了。你可以在任何地方直接停止程序的执行，Python 会放弃正在做的事情并退出。这条规则是不变的，即使是不寻常的`finally`行为。

# 被打赏

因此，我们可以用`try...except`捕捉执行。但是如果我们真的想扔一个呢？

在 Python 术语中，我们说我们**引发了一个异常**，就像这种语言中的大多数事情一样，实现这一点是显而易见的:只需使用`raise`关键字:

```
class Tardis:

    def __init__(self):
        pass

    def camouflage(self):
        raise NotImplementedError('Chameleon circuits are stuck.')

tardis = Tardis()
tardis.camouflage() 
```

Enter fullscreen mode Exit fullscreen mode

当我们执行该代码时，我们会看到引发的异常。

```
Traceback (most recent call last):
  File "/home/jason/Code/FiringRanges/PyFiringRange/Sandbox/tardis.py", line 10, in <module>
    tardis.camoflague()
  File "/home/jason/Code/FiringRanges/PyFiringRange/Sandbox/tardis.py", line 7, in camoflague
    raise NotImplementedError('Chameleon circuits are stuck.')
NotImplementedError: Chameleon circuits are stuck. 
```

Enter fullscreen mode Exit fullscreen mode

啊，好吧，我想我们还是被警察的表格困住了。至少这样更容易记住我们停车的地方。

**Gotcha Alert:**[`NotImplementedError`异常](https://docs.python.org/3.7/library/exceptions.html#NotImplementedError)是 Python 中内置的异常之一，有时用来表示一个函数还不应该使用，因为它还没有完成(但总有一天会完成)。与 [`NotImplemented`值](https://docs.python.org/3.7/library/constants.html#NotImplemented)不可互换的是*。请参阅文档以了解何时使用每种方法。*

显然，关键代码是`raise NotImplementedError('Chameleon circuits are stuck.')`。在关键字`raise`之后，我们给出要引发的异常对象的名称。在大多数情况下，我们从一个异常类中创建一个新的对象，你可以从括号的使用中看到这一点。所有异常都接受字符串作为消息的第一个参数。一些异常接受或需要更多的参数，所以参见[文档](https://docs.python.org/3.7/library/exceptions.html)。

# 使用异常

有时候我们需要在捕捉到异常*之后做一些事情。我们有一些非常简单的方法来做到这一点。*

最明显的是打印异常的消息。为此，我们需要能够处理我们捕获的异常对象。让我们将`except`语句改为`except NotImplementedError as e:`，其中`e`是我们“绑定”到异常对象的名称。然后，我们可以直接用`e`做宾语。

```
tardis = Tardis()

try:
    tardis.camouflage()
except NotImplementedError as e:
    print(e) 
```

Enter fullscreen mode Exit fullscreen mode

异常类已经定义了它的`__str__()`函数来返回异常消息，所以如果我们将它转换成一个字符串(`str()`)，这就是我们将得到的结果。您可能记得在以前的文章中，`print()`自动将其参数转换为字符串。当我们运行这段代码时，我们得到...

```
Chameleon circuits are stuck. 
```

Enter fullscreen mode Exit fullscreen mode

太好了，这太简单了！

## 冒泡起来

现在，如果我们想再次引发异常*呢？*

 *> 等等，*什么*？我们刚抓到那东西。为什么又提出来了？

一个例子是，如果您需要在后台做一些清理工作，但最终仍然希望调用者必须处理异常。这里有一个例子...

```
class Byzantium:

    def __init__(self):
        self.power = 0

    def gravity_field(self):
        if self.power <= 0:
        raise SystemError("Gravity Failing")

def grab_handle():
    pass

byzantium = Byzantium()

try:
    byzantium.gravity_field()
except SystemError:
    grab_handle()
    print("Night night")
    raise 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我们只是想抓住一些实实在在的东西(`grab_handle()`)并打印一条额外的消息，然后用`raise`处理这个异常。当我们重新提出一个异常时，我们说它**冒泡**。

```
Night night
Traceback (most recent call last):
  File "/home/jason/Code/FiringRanges/PyFiringRange/Sandbox/byzantium.py", line 18, in <module>
    byzantium.gravity_field()
  File "/home/jason/Code/FiringRanges/PyFiringRange/Sandbox/byzantium.py", line 8, in gravity_field
    raise SystemError("Gravity Failing")
SystemError: Gravity Failing 
```

Enter fullscreen mode Exit fullscreen mode

**Gotcha Alert:** 你可能认为我们需要说`except SystemError as e:`和`raise e`之类的话，但这有点过头了。**对于冒泡的异常，我们*只有*需要自己调用`raise`。**

现在，如果我们想在冒泡异常时添加一些额外的信息呢？您的第一个猜测可能是引发一个新的异常，但是这会带来一些问题。为了演示，我将向执行顺序添加另一个层。注意，当我处理那个`SystemError`的时候，我会抛出一个新的`RuntimeError`来代替。我正在第二个`try...except`块中捕捉新的异常。

```
byzantium = Byzantium()

def test():
    try:
        byzantium.gravity_field()
    except SystemError:
        grab_handle()
        raise RuntimeError("Night night")

try:
    test()
except RuntimeError as e:
    print(e)
    print(e.__cause__) 
```

Enter fullscreen mode Exit fullscreen mode

当我们运行它时，我们得到如下输出。

```
Night night
None 
```

Enter fullscreen mode Exit fullscreen mode

当我们捕捉到新的异常时，我们完全不知道是什么导致了这个异常。为了解决这个问题，Python 3 在 [PEP 3134](https://www.python.org/dev/peps/pep-3134) 中引入了**显式异常链接**。实施起来很容易。看一下我们新的`test()`函数，这是与上一个例子相比我唯一改变的部分。

```
byzantium = Byzantium()

def test():
    try:
        byzantium.gravity_field()
    except SystemError as e:
        grab_handle()
        raise RuntimeError("Night night") from e

try:
    test()
except RuntimeError as e:
    print(e)
    print(e.__cause__) 
```

Enter fullscreen mode Exit fullscreen mode

你知道我在做什么吗？在`except`语句中，我将名称`e`绑定到我们正在捕捉的原始异常。然后，当引发新的`RuntimeError`异常时，我**用`from e`将**链接到前一个异常。我们现在的产量是...

```
Night night
Gravity Failing 
```

Enter fullscreen mode Exit fullscreen mode

当我们运行它时，我们的新异常会记住它来自哪里——之前的异常存储在它的`__cause__`属性中(打印在输出的第二行)。这对于日志记录特别有用。

你可以用异常类做很多其他的技巧，特别是 PEP 3134 的引入。和往常一样，我建议您阅读文档，我在文章的末尾提供了链接。

# 自定义异常

Python 有一大堆异常，它们的用法[有很好的记录](https://docs.python.org/3.7/library/exceptions.html)。当我为工作选择合适的人选时，我经常参考这个例外列表。然而，有时我们只是需要更多的东西...习俗。

所有错误类型的异常都是从`Exception`类派生的，而这个类又是从`BaseException`类派生的。这种双重层次结构的原因是这样你就可以捕捉所有的错误`Exceptions`，而不用对特殊的、非系统退出的异常做出反应，比如`KeyboardInterrupt`。当然，这在实践中对你没多大关系，因为`except Exception`实际上总是我前面提到的尿布反模式的另一种形式。而且无论如何，是*而不是*建议你直接从`BaseException`派生——只要知道它的存在就好。

当定制一个异常时，你可以从*中派生出任何你喜欢的*异常类。有时候，最好是从与你正在做的事情目的最接近的例外中推导出来。然而，如果你不知所措，你可以直接从`Exception`开始。

我们做一个吧，好吗？

```
class SpacetimeError(Exception):
    def __init__(self, message):
        super().__init__(message)

class Tardis():

    def __init__(self):
        self._destination = ""
        self._timestream = []

    def cloister_bell(self):
        print("(Ominous bell tolling)")

    def dematerialize(self):
        self._timestream.append(self._destination)
        print("(Nifty whirring sound)")

    def set_destination(self, dest):
        if dest in self._timestream:
            self.cloister_bell()
        self._destination = dest

    def engage(self):
        if self._destination in self._timestream:
            raise SpacetimeError("You should not cross your own timestream!")
        else:
            self.dematerialize()

tardis = Tardis()

# Should be fine tardis.set_destination("7775/349x10,012/acorn")
tardis.engage()

# Also fine tardis.set_destination("5136/161x298,58/delta")
tardis.engage()

# The TARDIS is not going to like this... tardis.set_destination("7775/349x10,012/acorn")
tardis.engage() 
```

Enter fullscreen mode Exit fullscreen mode

显然，最后一个将导致我们的`SpacetimeError`异常被引发。

让我们再看一下那个异常类声明。

```
class SpacetimeError(Exception):
    def __init__(self, message):
        super().__init__(message) 
```

Enter fullscreen mode Exit fullscreen mode

那其实超级好写。如果您还记得我们之前对类的探索，`super().__init__()`正在基类上调用初始化器，在本例中是`Exception`。我们把消息传递给`SpacetimeError`异常构造器，并把它交给基类初始化器。

事实上，如果我正在做的唯一一件事是将`message`传递给`super()`类，我可以让它变得更简单:

```
class SpacetimeError(Exception):
    pass 
```

Enter fullscreen mode Exit fullscreen mode

Python 自己处理基础。

这就是我们*需要*做的一切，尽管像往常一样，我们可以用这个做更多的把戏。自定义异常不仅仅是一个好听的名字；我们可以用它们来处理各种不寻常的错误场景，尽管这显然超出了本指南的范围。

# 回顾

好吧，你已经通过了我们对 Python 错误的探索，没有被蒸发、删除、升级或意外地落在错误的县，所以为你欢呼三声！让我们回顾一下要点:

*   不要害怕 Python 中的异常！我们可以用它们来使我们的代码更加整洁。
*   使用`try...except`块捕捉异常。(是`except`，不是`catch`！)
*   永远不要使用尿布反模式，这是一个简单的`except:`陈述，或者(通常)是一个`except Exception:`
*   `else`块可以包含在最后一个`except`之后，并且只有在`try`块中的代码没有引发异常时才运行。
*   `finally`块可以包含在`try...except`语句的末尾，并且*总是*运行，即使我们`return`在`except`或`else`块的中间。
*   我们通过**经由`raise WhateverError("Our message")`引发**它来“抛出”一个异常
*   在一个`except`块中，我们可以**用一个空的`raise`冒泡**(再加注)一个异常。
*   我们可以通过从`Exception`类或者它的许多子类中的一个派生来创建定制的异常类。

和往常一样，文档揭示了更多。我强烈推荐你去看看:

*   [Python 教程:错误和异常](https://docs.python.org/3/tutorial/errors.html)
*   [Python 参考:内置异常](https://docs.python.org/3/library/exceptions.html)
*   [Python 参考:内置异常- `NotImplementedError`](https://docs.python.org/3/library/exceptions.html#NotImplementedError)
*   [Python 引用:内置常量- `NotImplemented`](https://docs.python.org/3/library/constants.html#NotImplemented)
*   [PEP 3134:异常链接和嵌入式回溯](https://www.python.org/dev/peps/pep-3134/)
*   [最邪恶的 Python 反模式](https://realpython.com/the-most-diabolical-python-antipattern/)

* * *

*感谢`deniska`和`grym` (Freenode IRC `#python`)的修改建议。***
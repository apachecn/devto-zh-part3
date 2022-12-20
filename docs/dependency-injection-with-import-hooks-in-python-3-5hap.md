# Python 3 中带有导入挂钩的依赖注入

> 原文：<https://dev.to/dangerontheranger/dependency-injection-with-import-hooks-in-python-3-5hap>

`sys.meta_path`是 Python 标准库中保守得最好的秘密之一，尽管它位于每个 Python 程序员都熟悉的模块中。不过，有些人可能会说保密是有充分理由的；使用可能会颠覆程序员期望的工具和函数通常不是一个好主意，这个奇怪命名的列表可能会这样。但是“不做”不是比“不知道”更好吗？因此，在快速入门依赖注入之前，让我们先检查一下`sys.meta_path`的一些细节。

每当你使用`import`语句时，比如说`import os`，它在功能上等同于下面的代码:

```
os = __import__("os") 
```

这是一种轻微的过度简化，因为像子模块这样的东西需要更多的工作(实际上，我们将在后面处理)，但这里要指出的要点是，内置的`__import__`在 Python 中为模块导入做了大量的工作，现在我们知道它存在，我们可以检查它的控制流。首先，它检查`sys.modules`，这是一个包含已经加载的模块的字典，以防我们之前已经导入了请求的模块。接下来，它检查`sys.path`——一个包含文件系统路径的列表，供 Python 搜索潜在的模块；你可能已经知道这个列表了。然而，如果`sys.meta_path`列表不为空，那么在查看`sys.path`之前的*，将在检查文件系统之前查询`sys.meta_path`中的每个元导入器挂钩，这给了我们一个简单的方法来拦截模块导入，并做各种奇妙的事情，比如依赖注入。*

那么什么是依赖注入呢？它大致是[依赖倒置原则](https://en.wikipedia.org/wiki/Dependency_inversion_principle)的应用版本，通常用于模块大小的东西。假设您的应用程序有几个不同的前端，可能针对不同的平台或 UI 工具包。只要你有一个通用的接口，依赖注入就可以让你做类似于
的事情

```
user_info = info_getter.get_user_info()
frontend = dependency_injector.get("frontend")
frontend.display_user_info(user_info) 
```

“那又怎么样？我已经可以用一个外观或其他东西做到这一点，也许是一个配置开关，”我可以听到你说。对于非常简单的用例，这实际上可能更好——简单比复杂好，记得吗？然而，通过依赖注入，我们可以很容易地提供模块级的重用，并显著减少样板文件。如果我告诉你，在这篇文章结束时，你可以这样写:

```
import myapp.dependency.frontend as frontend
popup = frontend.Popup(popup_style=frontend.ERROR)
popup.display() 
```

不管我们是使用 GTK+，Qt，ncurses，还是您的程序需要运行的任何东西，一个导入就能工作。在这一点上，你可能会有一些非常阴暗的想法。这是真的，所有的黑魔法都可以根据我们进口的钩子的细节来表演。但是可读性很重要，所以我们不要偏离常规太远是很重要的。因此，在编写实现之前，让我们为依赖注入框架设置一些要求:

*   所有依赖注入的导入路径都以`myapp.virtual`开始
*   提供的依赖关系是预先已知的，并用一个`provide`方法注册

所有的魔法——没有那么多——几乎完全依赖于创建一个元路径查找器并将其添加到`sys.meta_path`中。元路径查找器是一个非常简单的对象，只有一个公共方法，`find_spec` :

```
import importlib.abc
import importlib.machinery

class DependencyInjectorFinder(importlib.abc.MetaPathFinder):
    def __init__(self, loader):
        # we'll write the loader in a minute, hang tight
        self._loader = loader
    def find_spec(self, fullname, path, target=None):
        """Attempt to locate the requested module
        fullname is the fully-qualified name of the module,
        path is set to __path__ for sub-modules/packages, or None otherwise.
        target can be a module object, but is unused in this example.
        """
        if self._loader.provides(fullname):
            return self._gen_spec(fullname)
    def _gen_spec(self, fullname):
        spec = importlib.machinery.ModuleSpec(fullname, self._loader)
        return spec
# we'll also add it to sys.meta_path later 
```

如果元路径查找器提供了所请求的模块，那么它应该返回一个`importlib.machinery.ModuleSpec`类的实例，这是一件相当简单的事情，只需要少量的属性就可以让 Python 的导入机制知道它需要知道什么，以便在导入用户所请求的模块时采取后续步骤。出于我们的目的，我们对两个属性感兴趣(唯一必需的):`ModuleSpec.name`，它是所请求模块的名称，以及`ModuleSpec.loader`，它是 Python 应该用来实际加载模块的加载器对象——您还会注意到上面引用加载器对象的`self._loader`行。loader 对象是一个非常简单的类，在现代 Python (3.4 及以后版本)中有两个必需的方法:`create_module`，它将一个`ModuleSpec`作为其唯一的参数，并返回一个 Python 将认为是新模块的对象，以及`exec_module`，它将新模块作为其唯一的参数并执行它。因此，一个无操作的准系统加载程序看起来像这样:

```
class Loader(importlib.abc.Loader):
    def create_module(self, spec):
        raise NotImplementedError
    def exec_module(self, module):
        raise NotImplementedError 
```

除了这两个方法之外，我们还应该实现我们之前讨论过的`provide`方法，以表示提供了某种依赖关系，以及我们的 finder 之前引用的`provides`方法，以表示所请求的模块是否是我们的依赖注入框架的一部分。下面是实现:

```
import importlib.abc
import sys
import types

class DependencyInjectorLoader(importlib.abc.Loader):
    _COMMON_PREFIX = "myapp.virtual."
    def __init__(self):
        self._services = {}
        # create a dummy module to return when Python attempts to import
        # myapp and myapp.virtual, the :-1 removes the last "." for
        # aesthetic reasons :) 
        self._dummy_module = types.ModuleType(self._COMMON_PREFIX[:-1])
        # set __path__ so Python believes our dummy module is a package
        # this is important, since otherwise Python will believe our
        # dummy module can have no submodules
        self._dummy_module.__path__ = []
    def provide(self, service_name, module):
        """Register a service as provided via the given module
        A service is any Python object in this context - an imported module,
        a class, etc."""
        self._services[service_name] = module
    def provides(self, fullname):
        if self._truncate_name(fullname) in self._services:
            return True
        else:
            # this checks if we should return the dummy module,
            # since this evaluates to True when importing myapp and
            # myapp.virtual
            return self._COMMON_PREFIX.startswith(fullname)
    def create_module(self, spec):
        """Create the given module from the supplied module spec
        Under the hood, this module returns a service or a dummy module,
        depending on whether Python is still importing one of the names listed
        in _COMMON_PREFIX.
        """
        service_name = self._truncate_name(spec.name)
        if service_name not in self._services:
            # return our dummy module since at this point we're loading
            # *something* along the lines of "myapp.virtual" that's not
            # a service
            return self._dummy_module
        module = self._services[service_name]
        return module
    def exec_module(self, module):
        """Execute the given module in its own namespace
        This method is required to be present by importlib.abc.Loader,
        but since we know our module object is already fully-formed,
        this method merely no-ops.
        """
        pass
    def _truncate_name(self, fullname):
        """Strip off _COMMON_PREFIX from the given module name
        Convenience method when checking if a service is provided.
        """
        return fullname[len(self._COMMON_PREFIX):] 
```

乍一看，这里似乎有很多代码(尽管大部分都是注释！)，所以让我们浏览一下这个类的方法。首先是我们之前谈到的`provide`和`provides`方法；两者都没有多少魔力。如果我们试图导入`myapp`或`myapp.virtual`，T2 会返回一个所谓的虚拟模块，这是有原因的。假设我们有下面一行代码:

```
import myapp.virtual.frontend 
```

这在 Python 的导入机制中生成了三个不同的搜索:一个是针对`myapp`，另一个是针对`myapp.virtual`，最后一个是针对`myapp.virtual.frontend`。因为显然`myapp`和`myapp.virtual`在系统中实际上不存在，但是如果它们没有被加载，Python 会抱怨，我们声称我们提供了两者——注意如果查询`myapp`和`myapp.virtual`时`provides`将如何返回`True`,并返回一个空的哑模块来安抚 Python 的导入机制。只有当我们遇到`myapp.virtual.frontend`时，我们才检查所提供的依赖注入服务的列表。

至此，我们实际上已经编写了所有需要的管道，所以让我们总结一下，看看如何在实践中使用这些类:

```
import sys

class DependencyInjector:
    """
    Convenience wrapper for DependencyInjectorLoader and DependencyInjectorFinder.
    """
    def __init__(self):
        self._loader = DependencyInjectorLoader()
        self._finder = DependencyInjectorFinder(self._loader)
    def install(self):
        sys.meta_path.append(self._finder)
    def provide(self, service_name, module):
        self._loader.provide(service_name, module)

class FrontendModule:
    class Popup:
        def __init__(self, message):
            self._message = message
        def display(self):
            print("Popup:", self._message)

if __name__ == "__main__":
    injector = DependencyInjector()
    # we could install() and then provide() if we wanted, order isn't
    # important for the below two calls
    injector.provide("frontend", FrontendModule())
    injector.install()
    # note that these last three lines could exist in any other source file,
    # as long as injector.install() was called somewhere first
    import myapp.virtual.frontend as frontend
    popup = frontend.Popup("Hello World!")
    popup.display() 
```

在这一点上，值得后退一步，从高层次上审视一下幕后发生的事情:

1.  对`injector.install`的调用将我们的`DependencyInjectorFinder`添加到`sys.meta_path`列表中，因此 Python 将在未来的导入中使用它。
2.  行`import myapp.virtual.frontend as frontend`触发三个模块搜索，如前所述——一个是针对`myapp`，然后是`myapp.virtual`，然后是`myapp.virtual.frontend`:
    *   Python 在`sys.meta_path`中搜索，寻找元路径查找器。如果你的系统 Python 和我的一样，我们的`DependencyInjectorFinder`将是唯一一个没有被`_frozen_importlib`定义的，它是 Python 本身的一部分。
    *   对于每个找到的元路径查找器，Python 查询查找器的`find_spec`方法，查看查找器是否提供给定的模块。
    *   显然`myapp.virtual.frontend`在文件系统上不存在，所以由我们的元路径查找器来处理它。在所有这三种情况下，我们返回一个`ModuleSpec`实例，其中`name`被设置为 Python 询问我们是否能找到的相同名称，而`loader`被设置为我们的自定义`DependencyInjectorLoader`。
    *   接下来，Python 将调用我们的加载器上的`create_module`方法来调用有问题的`ModuleSpec`。对于`myapp`和`myapp.virtual`，我们的加载器识别出这些是伪模块，并为两者返回相同的伪模块(欺骗 Python 使其相信模块已被加载)，但是在被要求加载`myapp.virtual.frontend`时，返回我们用`injector.provide()`给它的`FrontendModule`的实例。Python 允许任何有效的对象作为一个模块，所以一个普通的旧类完全可以返回。
    *   Python 将最终调用我们的加载器上的`exec_module`方法，将我们从`create_module`返回的对象传递给它。Python 要求`exec_module`方法存在，但并不真正关心它的行为；通常，这个方法会在一个新的名称空间中执行模块的代码，但是因为我们已经准备好了完整的模块——无论是我们预先制作的虚拟模块还是我们的`FrontendModule`实例——我们在`exec_module`中什么也不做。
    *   每次连续模块搜索后，清洗、冲洗并重复。
3.  毕竟，`frontend.Popup`在功能上和`FrontendModule.Popup`是一回事，剩下的，就像他们说的，都是历史了。

正如我在本文前面提到的，重要的是不要像这样滥用语言特性——毕竟，能力越大，责任越大——对于元编程来说尤其如此。不过，凡事都有时间和地点，包括进口挂钩；但是请负责任地使用它们。

最后，如果你想下载并仔细阅读，完整的代码示例将作为 GitHub 上的[要点。钩钩快乐！](https://gist.github.com/DangerOnTheRanger/027fc0af3bd010e8265e552c5b3ab4f1)
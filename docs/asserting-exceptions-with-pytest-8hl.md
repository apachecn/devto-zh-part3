# 用 Pytest 断言异常

> 原文：<https://dev.to/wangonya/asserting-exceptions-with-pytest-8hl>

我第一次让人审查我的拉取请求时，她对测试相当严格。当然，如果测试失败，我就不能合并。但是，如果覆盖率下降了哪怕 1%，我也不能合并。TDD 对我来说仍然是新的，所以保持覆盖范围是一个挑战，因为我只测试了我能测试的最低限度。我必须找出如何使我的测试更加健壮，并确保尽可能多的代码得到测试。我不确定如何测试的一个领域是我编写的自定义异常。这里有一个例子:

```
# login.py 
def check_email_format(email):
    """check that the entered email format is correct"""
    pass

def test_email_exception():
    """test that exception is raised for invalid emails"""
    pass 
```

Enter fullscreen mode Exit fullscreen mode

如果您正在实现带有电子邮件认证的系统，这可能是您想要做的事情。这个例子过于简单，但是它很好地满足了本文的目的。

为了测试引发的异常，pytest 提供了一个简便的方法:`pytest.raises`。让我们看看如何在我们的例子中使用它:

```
import re
import pytest

def check_email_format(email):
    """check that the entered email format is correct"""
    if not re.match(r"(^[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$)", email):
        raise Exception("Invalid email format")
    else:
        return "Email format is ok"

def test_email_exception():
    """test that exception is raised for invalid emails"""
    with pytest.raises(Exception):
        assert check_email_format("good@email.com") 
```

Enter fullscreen mode Exit fullscreen mode

`check_email_format`方法接收一封电子邮件并检查它是否匹配给定的正则表达式模式。如果是，它返回`"Email format is ok"`，否则，引发一个异常。

使用`with`块中的`pytest.raises`作为上下文管理器，我们可以检查如果给出一个无效的电子邮件，是否真的引发了异常。对上面的代码运行测试应该会失败:

```
collected 1 item                                                                                                                                                                                       
login.py F                [100%]

==================== FAILURES ========================

    def test_email_exception():
        """test that exception is raised for invalid emails"""
        with pytest.raises(Exception):
>           assert check_email_format("good@email.com")
E           Failed: DID NOT RAISE <class 'Exception'>

login.py:16: Failed 
```

Enter fullscreen mode Exit fullscreen mode

注意上面写着`Failed: DID NOT RAISE <class 'Exception'>`。如果没有引发异常，测试失败。我觉得这很棒。我们传递了一个有效的电子邮件格式(根据我们这里的标准),所以测试按预期工作。现在我们可以让它过去了。

```
import re
import pytest

def check_email_format(email):
    """check that the entered email format is correct"""
    if not re.match(r"(^[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$)", email):
        raise Exception("Invalid email format")
    else:
        return "Email format is ok"

def test_email_exception():
    """test that exception is raised for invalid emails"""
    with pytest.raises(Exception):
        assert check_email_format("bademail.com") # invalid email format to raise exception 
```

Enter fullscreen mode Exit fullscreen mode

运行您的测试:`pytest login.py` :

```
collected 1 item                         

login.py .              [100%]

====================== 1 passed in 0.05 seconds ====================== 
```

Enter fullscreen mode Exit fullscreen mode

您还可以为异常消息添加额外的检查:

```
import re
import pytest

def check_email_format(email):
    """check that the entered email format is correct"""
    if not re.match(r"(^[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$)", email):
        raise Exception("Invalid email format")
    else:
        return "Email format is ok"

def test_email_exception():
    """test that exception is raised for invalid emails"""
    with pytest.raises(Exception) as e:
        assert check_email_format("bademail.com")
    assert str(e.value) == "Invalid email format" 
```

Enter fullscreen mode Exit fullscreen mode

[![gif](img/9cc5c28e9bd23cd9ab693bdca0b1209f.png)](https://i.giphy.com/media/xTiTnnLkYTDWSOWSHK/giphy.gif)
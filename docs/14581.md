# 跳过测试

> 原文：<https://dev.to/wangonya/skipping-tests-2p6o>

有时，出于某种原因，您可能希望在执行其他测试时跳过某个特定的测试。可能数据库人员还没有完成设置，这个特定的测试需要一个数据库连接。不需要等待，您可以直接编写测试并指示 pytest 跳过它，给出适当的原因，这样看起来就不会像您只是跳过了一个失败的测试来保持您的测试套件绿色。

有几种方法可以做到这一点。最简单的是像这样使用`@pytest.mark.skip`装饰器:

```
import pytest

def test_stuff(): # this test will be executed
    pass

@pytest.mark.skip(reason="just testing if skip works")
def test_other_stuff(): # this one will be skipped
    pass 
```

Enter fullscreen mode Exit fullscreen mode

运行您的测试应该会产生下面的输出:

```
collected 2 items
skip.py .s                           [100%]

===================== 1 passed, 1 skipped in 0.05 seconds =============== 
```

Enter fullscreen mode Exit fullscreen mode

注意那个小`s`。它显示被跳过的测试。Pytest 还告诉我们`1 passed, 1 skipped`。如果需要更详细的输出，可以使用`-rs`标志`pytest skip.py -rs` :

```
collected 2 items
skip.py .s                    [100%]
==================== short test summary info ===============
SKIP [1] skip.py:14: just testing if skip works

=================== 1 passed, 1 skipped in 0.02 seconds ========== 
```

Enter fullscreen mode Exit fullscreen mode

上面的测试甚至在开始之前就被跳过了。这并不总是理想的。通过使用`pytest.skip(reason)`函数:
，您可以更好地控制如何跳过测试

```
import pytest

def setup_stuff():
    return False

def test_stuff(): # this test will be executed
    pass

def test_other_stuff(): # this one will be skipped if setup_stuff() returns false
    if not setup_stuff():
        pytest.skip("setup failed")
    else:
        pass 
```

Enter fullscreen mode Exit fullscreen mode

```
collected 2 items
skip.py .s                  [100%]
=================== short test summary info =============================
SKIP [1] skip.py:12: setup failed

==================== 1 passed, 1 skipped in 0.05 seconds ================= 
```

Enter fullscreen mode Exit fullscreen mode

如果您希望在测试开始前检查条件是否满足，那么您可以使用`skipif` :

```
import pytest

def setup_stuff():
    return False

def test_stuff(): # this test will be executed
    pass

@pytest.mark.skipif(not setup_stuff(), reason="setup failed")
def test_other_stuff(): # this one will be skipped if setup_stuff() returns false
    pass 
```

Enter fullscreen mode Exit fullscreen mode

```
collected 2 items
skip.py .s                  [100%]
=================== short test summary info =============================
SKIP [1] skip.py:12: setup failed

==================== 1 passed, 1 skipped in 0.05 seconds ================= 
```

Enter fullscreen mode Exit fullscreen mode

正如在[文档](https://docs.pytest.org/en/latest/skipping.html)中所解释的，根据特定的条件，有许多其他的方法可以定制你要跳过的测试。
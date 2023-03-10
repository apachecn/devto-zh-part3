# 调试时捕获打印语句

> 原文：<https://dev.to/wangonya/capturing-print-statements-while-debugging-19n2>

调试时，您可能需要在测试中捕获打印语句。这可能只是为了帮助您调试，或者出于其他原因。除了其他测试之外，我主要在测试数据库连接时做这种事情，我希望确保看到“数据库连接成功”消息。

让我们用一个简单的例子来演示:

```
def add_numbers(num1, num2):
    print("Add function started...")
    result = num1 + num2
    print("Numbers added. Returning result...")
    return result

def test_add_numbers():
    assert add_numbers(2, 3) == 3 
```

Enter fullscreen mode Exit fullscreen mode

运行上面的测试失败，返回给定的回溯:

```
collected 1 item
print.py F                 [100%]

============================ FAILURES ============================

    def test_add_numbers():
>       assert add_numbers(2, 3) == 3
E       assert 5 == 3
E        +  where 5 = add_numbers(2, 3)

print.py:9: AssertionError
---------------------- Captured stdout call ----------------------------
Add function started...
Numbers added. Returning result...
===================== 1 failed in 0.11 seconds ==================== 
```

Enter fullscreen mode Exit fullscreen mode

注意`Captured stdout call`部分。它捕捉到了两个打印语句。在测试执行期间，任何发送到 stdout 和 stderr 的输出都会被捕获。默认情况下，仅当测试失败时，才会显示捕获的输出。将我们的`assert add_numbers(2, 3) == 3`更改为`assert add_numbers(2, 3) == 5`并再次运行测试会得到以下输出:

```
collected 1 item
print.py .                       [100%]

========================= 1 passed in 0.05 seconds =========================== 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过使用`readouterr()` :
来访问您在测试中捕获的输出

```
def add_numbers(num1, num2):
    print("Add function started...")
    result = num1 + num2
    print("Numbers added. Returning result...")
    return result

def test_add_numbers(capsys):
    result = add_numbers(2, 3)
    captured = capsys.readouterr()
    assert "Hello" in captured.out
    assert result == 3 
```

Enter fullscreen mode Exit fullscreen mode

```
collected 1 item
print.py F                             [100%]

====================== FAILURES =======================

capsys = <_pytest.capture.CaptureFixture object at 0x10ba25d30>

    def test_add_numbers(capsys):
        result = add_numbers(2, 3)
        captured = capsys.readouterr()
>       assert "Hello" in captured.out
E       AssertionError: assert 'Hello' in 'Add function started...\nNumbers added. Returning result...\n'
E        +  where 'Add function started...\nNumbers added. Returning result...\n' = CaptureResult(out='Add function started...\nNumbers added. Returning result...\n', err='').out

print.py:11: AssertionError
======================== 1 failed in 0.09 seconds ======================= 
```

Enter fullscreen mode Exit fullscreen mode

值得注意的是，每个`readouterr()`调用都会对到目前为止的输出进行快照，所以我们可以重复调用它，一步一步地检查我们测试的输出。

```
def add_numbers(num1, num2):
    print("Add function started...")
    result = num1 + num2
    print("Numbers added. Returning result...")
    return result

def test_add_numbers(capsys):
    result = add_numbers(2, 3)
    captured = capsys.readouterr()
    assert "Add function started" in captured.out
    assert "Numbers added. Returning result" in captured.out
    # thus far, readouterr() out only has the two print statements from the add_numbers function
    assert result == 5
    print("thank you, next")
    captured = capsys.readouterr()
    # here, readouterr() out doesn't have the two outputs from above
    # it now contains the new value, "thank you, next"
    assert captured.out == "thank you, next\n" 
```

Enter fullscreen mode Exit fullscreen mode

```
collected 1 item
print.py .                       [100%]

========================= 1 passed in 0.05 seconds =========================== 
```

Enter fullscreen mode Exit fullscreen mode
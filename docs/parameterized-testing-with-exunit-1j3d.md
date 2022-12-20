# 使用 ExUnit 进行参数化测试

> 原文：<https://dev.to/niku/parameterized-testing-with-exunit-1j3d>

更新:hauleth 在 [ElixirForum](https://elixirforum.com/t/parameterized-testing-with-exunit/18935/2) 中提到了一个更好的解决方案。所以，我更新了这篇文章。

我发现了一种方法，就是用 ExUnit 进行参数化测试。

```
ExUnit.start()

defmodule ParameterizedTest do
  use ExUnit.Case, async: true

  for {lhs, rhs} <- [{"one", 1}, {"two", 2}, {"three", 3}] do
    test "#{lhs} convert to #{rhs}" do
      assert unquote(lhs) === unquote(rhs)
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
 1) test one convert to 1 (ParameterizedTest)
     parameterized_test.exs:7
     Assertion with === failed
     code:  assert "one" === 1
     left:  "one"
     right: 1
     stacktrace:
       parameterized_test.exs:8: (test)

  2) test three convert to 3 (ParameterizedTest)
     parameterized_test.exs:7
     Assertion with === failed
     code:  assert "three" === 3
     left:  "three"
     right: 3
     stacktrace:
       parameterized_test.exs:8: (test)

  3) test two convert to 2 (ParameterizedTest)
     parameterized_test.exs:7
     Assertion with === failed
     code:  assert "two" === 2
     left:  "two"
     right: 2
     stacktrace:
       parameterized_test.exs:8: (test)

Finished in 0.03 seconds (0.03s on load, 0.00s on tests)
3 tests, 3 failures

Randomized with seed 169786 
```

Enter fullscreen mode Exit fullscreen mode

# 问题

我们不能将变量从外部直接传递到测试块中。

```
ExUnit.start()

defmodule ParameterizedTest do
  use ExUnit.Case, async: true

  for {lhs, rhs} <- [{"one", 1}, {"two", 2}, {"three", 3}] do
    test "#{lhs} convert to #{rhs}" do
      assert lhs === rhs # We can't use their variables here.
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
** (CompileError) parameterized_test.exs:8: undefined function lhs/0
    (stdlib) lists.erl:1338: :lists.foreach/2 
```

Enter fullscreen mode Exit fullscreen mode

# 其他方案

## 用断言代替测试

断言和测试工作良好。但是，ExUnit 每次测试只显示一条消息。所以，如果你想展示测试大纲。它不太相配。

```
ExUnit.start()

defmodule ParameterizedTest do
  use ExUnit.Case, async: true

  test "convert" do
    for {lhs, rhs} <- [{"one", 1}, {"two", 2}, {"three", 3}] do
      assert lhs === rhs
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
 1) test convert (ParameterizedTest)
     parameterized_test.exs:6
     Assertion with === failed
     code:  assert lhs === rhs
     left:  "one"
     right: 1
     stacktrace:
       parameterized_test.exs:8: anonymous fn/2 in ParameterizedTest."test convert"/1
       (elixir) lib/enum.ex:1925: Enum."-reduce/3-lists^foldl/2-0-"/3
       parameterized_test.exs:7: (test)

Finished in 0.03 seconds (0.03s on load, 0.00s on tests)
1 test, 1 failure

Randomized with seed 303559 
```

Enter fullscreen mode Exit fullscreen mode

## 使用模块属性

测试运行良好。但是，模块属性超出了范围。所以，在使用模块属性之后，你必须记住它。

```
ExUnit.start()

defmodule ParameterizedTest do
  use ExUnit.Case, async: true

  for {lhs, rhs} <- [{"one", 1}, {"two", 2}, {"three", 3}] do
    @pair {lhs, rhs}

    test "#{lhs} convert to #{rhs}" do
      {l, r} = @pair
      assert l === r
    end
  end

  test "pair should not have any value" do
    assert nil === @pair # We don't expect getting any values
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
 1) test pair should not have any value (ParameterizedTest)
     parameterized_test.exs:15
     Assertion with === failed
     code:  assert nil === @pair
     left:  nil
     right: {"three", 3}
     stacktrace:
       parameterized_test.exs:16: (test)

  2) test three convert to 3 (ParameterizedTest)
     parameterized_test.exs:9
     Assertion with === failed
     code:  assert l === r
     left:  "three"
     right: 3
     stacktrace:
       parameterized_test.exs:11: (test)

  3) test two convert to 2 (ParameterizedTest)
     parameterized_test.exs:9
     Assertion with === failed
     code:  assert l === r
     left:  "two"
     right: 2
     stacktrace:
       parameterized_test.exs:11: (test)

  4) test one convert to 1 (ParameterizedTest)
     parameterized_test.exs:9
     Assertion with === failed
     code:  assert l === r
     left:  "one"
     right: 1
     stacktrace:
       parameterized_test.exs:11: (test)

Finished in 0.04 seconds (0.04s on load, 0.00s on tests)
4 tests, 4 failures

Randomized with seed 936081 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 ex _ parameterized

[https://github.com/KazuCocoa/ex_parameterized](https://github.com/KazuCocoa/ex_parameterized)允许参数化测试如下:

```
defmodule MyExampleTest do
  use ExUnit.Case, async: true
  use ExUnit.Parameterized        # Required

  test_with_params "add params",  # description
    fn (a, b, expected) ->        # test case
      assert a + b == expected
    end do
      [
        {1, 2, 3},                 # parameters
        "description": {1, 4, 5},  # parameters with description
      ]
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢这个主意。但是这一次，我想只用标准库来编写 ExUnit 测试用例。

## 用 ExUnit。Case.register_test/4

[ExUnit。Case.register_test/4](https://hexdocs.pm/ex_unit/1.7/ExUnit.Case.html#register_test/4) 。

> 注册一个要在 ExUnit 期间使用的新属性。案例测试。
> 
> 属性值将在 context.registered 中作为键/值对提供。键/值对将在类似于@tag 的每个 ExUnit.Case.test/3 之后被清除。

```
ExUnit.start()

defmodule ParameterizedTest do
  use ExUnit.Case, async: true

  ExUnit.Case.register_attribute __ENV__, :pair

  for {lhs, rhs} <- [{"one", 1}, {"two", 2}, {"three", 3}] do
    @pair {lhs, rhs}

    test "#{lhs} convert to #{rhs}", context do
      {l, r} = context.registered.pair
      assert l === r
    end
  end

  test "pair should not have any value" do
    assert nil === @pair
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
warning: undefined module attribute @pair, please remove access to @pair or explicitly set it before access
  parameterized_test.exs:18: ParameterizedTest (module)

.

  1) test two convert to 2 (ParameterizedTest)
     parameterized_test.exs:11
     Assertion with === failed
     code:  assert l === r
     left:  "two"
     right: 2
     stacktrace:
       parameterized_test.exs:13: (test)

  2) test one convert to 1 (ParameterizedTest)
     parameterized_test.exs:11
     Assertion with === failed
     code:  assert l === r
     left:  "one"
     right: 1
     stacktrace:
       parameterized_test.exs:13: (test)

  3) test three convert to 3 (ParameterizedTest)
     parameterized_test.exs:11
     Assertion with === failed
     code:  assert l === r
     left:  "three"
     right: 3
     stacktrace:
       parameterized_test.exs:13: (test)

Finished in 0.05 seconds (0.05s on load, 0.00s on tests)
4 tests, 3 failures

Randomized with seed 385498 
```

Enter fullscreen mode Exit fullscreen mode

它工作得很好，测试大纲，变量范围和只使用标准库。其实这个解决方案在我更新之前就写在这篇文章的上面了。但是我觉得代码有点冗长。比如:`{l, r} = context.registered.pair`。
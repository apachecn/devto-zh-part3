# Ruby 条件返回值:使用它们！

> 原文：<https://dev.to/burdettelamar/ruby-conditionals-return-values-use-them-n4h>

Ruby 条件返回值。也就是说，Ruby 条件(如`if`或`case`)是一个返回值的表达式。

您可以使用这些值来简化代码。

下面的例子使用了交互式 Ruby Shell，`irb`。

#### 如果-否则

假设这些被定义:

```
def some_condition; true; end
def an_expression_that_returns_a_value; 0; end
def another_expression_that_returns_a_value; 1; end 
```

这:

```
if some_condition
  q = an_expression_that_returns_a_value
else
  q = another_expression_that_returns_a_value
end
# => 0
q
# => 0 
```

可以用返回值变成这个:

```
q = if some_condition
      an_expression_that_returns_a_value
    else
      another_expression_that_returns_a_value
    end
# => 0
q
# => 0 
```

当然，在这种情况下，三元运算符就足够了，但是它可能更难理解:

```
q = some_condition ?
      an_expression_that_returns_a_value :
      another_expression_that_returns_a_value
# => 0
q
# => 0 
```

#### If-Elsif-Else

现在进一步假设这些也被定义:

```
def some_other_condition; false; end
def yet_another_expression_that_returns_a_value; 2; end 
```

这:

```
if some_condition
  q = an_expression_that_returns_a_value
elsif some_other_condition
  q = another_expression_that_returns_a_value
else
  q = yet_another_expression_that_returns_a_value
end
# => 0
q
# => 0 
```

可以用返回值变成这个:

```
q = if some_condition
      an_expression_that_returns_a_value
    elsif some_other_condition
      another_expression_that_returns_a_value
    else
      yet_another_expression_that_returns_a_value
    end
# => 0
q
# => 0 
```

#### 情况

这:

```
case
  when some_condition
    q = an_expression_that_returns_a_value
  when some_other_condition
    q = another_expression_that_returns_a_value
  else
    q = yet_another_expression_that_returns_a_value
  end
# => 0
q
# => 0 
```

可以用返回值变成这个:

```
q = case
    when some_condition
      an_expression_that_returns_a_value
    when some_other_condition
      another_expression_that_returns_a_value
    else
      yet_another_expression_that_returns_a_value
    end
# => 0
q
# => 0 
```
# 代码提示:避免不必要的 else 语句

> 原文：<https://dev.to/georgehanson/code-tip-avoid-unnecessary-else-statements-2jie>

写代码时，很容易以逻辑的方式思考。对于条件句，人们通常按照他们的想法来写。如果该条件为真，则返回该值，否则返回不同的值。

然而，有一个人们经常会陷入的陷阱，一个简单的重构可以使代码更具可读性。如果在返回值的方法或函数中有这样的条件，通常可以删除 else 条件，因为如果条件为真，它将永远不会在第一时间命中它。

考虑下面的代码:

```
public function getName()
{
    if ($this->firstName && $this->lastName) {
        return $this->firstName.' '.$this->lastName;
    } else {
        return $this->firstName;
    }
} 
```

这是重构的一个完美例子。

您会注意到，如果用户有名字和姓氏，我们会返回它。否则我们只返回他们的名字。

然而，else 条件是多余的，因为如果条件评估为 true，我们已经返回了一个值。

因此，我们可以重构这个方法来删除 else 语句。

```
public function getName()
{
    if ($this->firstName && $this->lastName) {
        return $this->firstName.' '.$this->lastName;
    }

    return $this->firstName;
} 
```

通过这样做，它消除了一定程度的缩进，使代码更具可读性。这是一个需要记住的有用的提示，我发现它适用于所有地方。

最初发布于[乔治·汉森](https://www.georgehanson.co.uk/code-tips/code-tip-avoid-unnecessary-else-statements)
# WTF 是 Lambda？

> 原文：<https://dev.to/jonhilt/wtf-is-a-lambda-2dg2>

如果你刚刚开始学习 C#，你可能遇到过类似的事情，并想知道奇怪的`x=>x`是怎么回事...

```
things.SingleOrDefault(x=>x.Name == "Bob"); 
```

为了理解发生了什么，让我们探索一个具体的例子。

假设你有一群人。

这里有一个类来代表一个人...

```
public class Person
{
    public string Name { get; set; }
    public int Age { get; set; }
} 
```

然后在你申请的某个地方，一个像这样的人的名单...

```
private List<Person> People = new List<Person>
{
    new Person { Name = "Bob", Age = 37 },
    new Person { Name = "Brian", Age = 21 },
    new Person { Name = "Amy", Age = 58 }
}; 
```

名单上的三个人。列表中的每个对象都是我们的
`Person`类的一个实例。

现在假设您想要查找“Amy”的记录并显示她的年龄。

你可以手动循环每个人，直到找到相关的人...

```
public void Exists()
{
    Person result = null;

    foreach (var person in People)
    {
        if (person.Name == "Amy")
        {
            result = person;
            break;
        }
    }

    if (result != null)
        Console.WriteLine("we found Amy!, she's " + result.Age);
} 
```

结果将是 Amy 的“Person”实例，我们可以将她的年龄写入控制台。

或者，你可以使用一些内置于 C#列表中的方法来节省你的击键次数，而不是自己去敲无尽的循环。

```
public void Exists()
{
    var amy = People.SingleOrDefault(IsAmy);
} 
```

在幕后，它将遍历“人物”列表，对于每个
人物，它将调用`IsAmy`来确定这是否是我们想要的人物。

我听到你问我看起来像什么。

```
private bool IsAmy(Person arg)
{
    return arg.Name == "Amy";
} 
```

`SingleOrDefault`期望一个函数，该函数接受一个`Person`并返回一个`bool`。

在这里我们可以放任何我们想要的逻辑。

如果`IsAmy`返回`true`，那么我们说`Person`的这个实例就是我们想要的
。如果我们返回`false`，我们说它不是。

我们可以像
一样“内联”我们的`IsAmy`函数(以避免单独声明它)...

```
public void Exists()
{
    var amy = People.SingleOrDefault((Person person) => 
                                        { return person.Name == "Amy" });
} 
```

这与上一个例子完全相同，除了我们没有声明一个
单独的函数，而是声明它是“内联的”。

你可以看到我们仍然接受一个`Person`参数，然后返回一个
`true`或`false`(如果“艾米”为真，否则为假)。

最后，为了更简洁，我们可以简化为...

```
public void Exists()
{
    var amy = People.SingleOrDefault(person => person.Name == "Amy");
} 
```

这是声明同一事物的另一种方式。

这里，我们仍然使用`Person`，将我们的条件应用于它，然后
返回结果(真或假)，但是使用了更少的击键次数！

因此= >的左边是参数(`Person`)，右边是返回布尔值的
表达式。

本质上，lambdas 实际上只是语法糖和声明函数的一种速记方式。

你会看到它们经常用于列表/其他集合，因此与数据库查询等相关。
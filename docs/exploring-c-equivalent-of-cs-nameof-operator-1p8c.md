# 探索 C#运算符名称的 C++等价形式

> 原文：<https://dev.to/tomoyukiaota/exploring-c-equivalent-of-cs-nameof-operator-1p8c>

###### (此处有日文翻译[。)](https://qiita.com/TomoyukiAota/items/b7791307f33ea88cd369)

C#中有 [`nameof`](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/nameof) 运算符，可以用来获取变量、类型或成员的字符串名称。
一个有用的例子是用方法名记录一条消息。在这种情况下，如果我们使用方法名的字符串文字，IDE 的重命名功能不会自动重命名该字符串文字，因为它不会被识别为标识符。
如果我们用`nameof`，会是这样:

```
void f(int i) {
   Log(nameof(f), "method entry");  
} 
```

如果我们像这样使用`nameof`，`nameof(f)`中的`f`会被当作一个标识符，所以在重命名方法的时候也会被重命名。

在这篇文章中，我将探讨 C++中类似于 C#的`nameof`操作符的资源管理器特性。

# `__func__`

C++有`__func__`，可以用在函数体中。`__func__`是一个预定义的标识符，用于保存函数名。
一个例子是这样的:

```
void f()
{
  std::cout << "This function is " << __func__ << std::endl;
} 
```

当调用`f`时，将显示`This function is f`。

`__func__`可以在函数体中检索函数名，但是 C#的`nameof`更加通用，因为它可以用于任何方法、函数和变量，只要标识符可用。

# 定义一个宏

在类似函数的宏中，当`#`正好放在参数之前时，将检索参数值的字符串文字。例如，这段代码显示`5+5 = 10`。

```
#define PRINT(int) printf(#int " = %d\n",int) PRINT(5+5); 
```

因此，下面的`NAMEOF`宏的行为将类似于 C#的`nameof`。

```
#define NAMEOF(name) #name 
```

用法会是这样:

```
struct MyStruct
{
    int myMemberVariable = 0;
};

class MyClass
{
public:
    int myMemberFunction() { return 0; }
};

int myGlobalVariable = 0;

void myFunction()
{
}

int main()
{
    std::cout << NAMEOF(MyStruct) << std::endl;
    std::cout << NAMEOF(MyStruct{}.myMemberVariable) << std::endl;
    std::cout << NAMEOF(MyClass{}.myMemberFunction) << std::endl;
    std::cout << NAMEOF(myGlobalVariable) << std::endl;
    std::cout << NAMEOF(myFunction) << std::endl;
} 
```

上面的代码将显示以下内容:

```
MyStruct
MyStruct{}.myMemberVariable
MyClass{}.myMemberFunction
myGlobalVariable
myFunction 
```

如果我对`myFunction`使用重命名功能，`NAMEOF(myFunction)`中的`myFunction`会被重命名。(我用的是 ReSharper 的重命名功能。)

然而，我说“`NAMEOF`宏的行为*类似于 C#的`nameof`”。`NAMEOF`宏和 C#的`nameof`运算符不完全一样。*

如果我们传递一个未定义的标识符`myNonexistentWhatever`，像这样:

```
 std::cout << NAMEOF(myNonexistentWhatever) << std::endl; 
```

构建成功，显示`myNonexistentWhatever`。这是因为宏展开就像编辑纯文本一样，这意味着`myNonexistentWhatever`不会被视为标识符。宏展开完成后，求值结果是`"myNonexistentWhatever"`(一个字符串文字)，所以会是一个有效的 C++代码。

所以我们首先需要确认使用`NAMEOF`宏时没有错别字。

也就是说，使用`NAMEOF`宏比简单地编写字符串文字要好。

有一个库， [bravikov/nameof](https://github.com/bravikov/nameof) ，它增强了功能，更类似于 C#的`nameof`。比如我们为`enum Foo {Bar}`写`nameof(Foo::Bar)`的时候，它会被求值为`"Bar"`，前面没有`Foo`。

# 最后

我谷歌了这个话题，但找不到很多信息。`NAMEOF`本文中的宏摘自[对 StackOverflow](https://stackoverflow.com/questions/38558180/does-qts-moc-c11-have-an-equivalent-of-cs-nameof-operator#comment64507414_38558180) 的一个评论，本文就是通过对它的阐述而产生的。因此，如果能分享一些关于这个 topc 的惯例/技巧/任何东西，我会很高兴。

# 引用

*   [https://stack overflow . com/questions/14351971/what-x-inside-a-c-macro-mean](https://stackoverflow.com/questions/14351971/what-does-x-inside-a-c-macro-mean)
*   [https://www.cprogramming.com/tutorial/cpreprocessor.html](https://www.cprogramming.com/tutorial/cpreprocessor.html)
*   [https://wandbox.org/permlink/w2tGPxN8UHknzNuT](https://wandbox.org/permlink/w2tGPxN8UHknzNuT)
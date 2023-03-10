# 为 xUnit 测试从 json 文件加载测试数据

> 原文：<https://dev.to/ankursheel/load-test-data-from-a-json-file-for-xunit-tests-3hlk>

## xUnit 101

xUnit 是一个单元测试工具。Net 框架。如果你是使用 xUnit 测试的新手，我建议阅读入门[文档。](https://xunit.github.io/docs/getting-started-dotnet-core.html)

xUnit 支持无参数和参数化测试。有 3 种不同的方式向参数化测试提供数据

*   当方法参数不变时，内联数据是好的，但是当你有很多测试用例时，它会变得很难处理。当数据不是常数时，它也不能被使用。
*   **类数据**通过将数据移动到一个单独的类中来消除测试文件的混乱。它还允许您将非常数数据传递给测试。缺点是你必须创建一个新的类。
*   **成员数据**类似于类数据，但是使用类型的静态属性或方法，而不是*类*。

* * *

## 问题

以上三种方法都有一个缺点，那就是每次你想添加新数据进行测试时，你都需要重新编译。如果有大量数据，这些类/方法也会变得很大。例如，[这个](https://github.com/AnkurSheel/AdventOfCode2018/blob/84f0174ce6d35bef70e45b1713d2e23272dc5a29/AdventOfCode2018.Tests/Day1/testData.json#L37-L1054)是《2018 年的[降临中谜题的样本输入。](https://adventofcode.com/2018)

* * *

## 第一关

如果我们可以从文件中加载测试数据，这两个问题就会消失。Andrew Lock 有一篇很棒的[文章](https://andrewlock.net/creating-a-custom-xunit-theory-test-dataattribute-to-load-data-from-json-files/)，展示了如何创建一个定制属性来从 JSON 文件中加载数据。因为这篇文章很大程度上依赖于他的方法，所以我建议先阅读它。

去吧，我等着。

太好了，你回来了。所以你可能会想，如果安德鲁已经写了这篇文章，我为什么要写这篇文章，更重要的是，你为什么要花宝贵的时间来读这篇文章？

好吧。我发现他的解决方案对于带有少量参数的测试用例非常有效。然而，使用大量参数会变得非常麻烦。对于代码测试输入的出现，我们必须有很多参数。我们可以将所需的参数数量减少到一个，因为这只是一个相同类型的列表(虽然很大)。然而，我不知道如何构建我的 JSON，以便它可以被容易地解析。

* * *

## 改进

让我们从创建一个新的泛型类开始，它采用两种不同的底层类型——一种用于数据，一种用于结果类。这个类将用于反序列化 JSON 数据

```
class TestObject<T1, T2>
{
    public List<T1> Data { get; set; }
    public T2 Result { get; set; }
} 
```

现在，让我们修改我们的属性类。为了简洁起见，我在这里只展示相关的代码。完整文件请看[此处](https://github.com/AnkurSheel/AdventOfCode2018/blob/master/AdventOfCode2018.Tests/JsonFileDataAttribute.cs)T3。

```
public class JsonFileDataAttribute : DataAttribute
{
    public override IEnumerable<object[]> GetData(MethodInfo testMethod)
    {
        // fileData is the raw file data
        // \_dataType and \_resultType are set in the constructor and are the types for the input data and the result
        var specific = typeof(TestObject<,>).MakeGenericType(\_dataType, \_resultType);
        var generic = typeof(List<>).MakeGenericType(specific);
        var jsonData = JObject.Parse(fileData);

        dynamic datalist = JsonConvert.DeserializeObject(jsonData, generic);
        var objectList = new List<object[]>();
        foreach (var data in datalist)
        {
            objectList.Add(new object[] {data.Data, data.Result});
        }
        return objectList;
    }
} 
```

我们到底在这里做什么？

1.  使用 *[MakeGenericType](https://docs.microsoft.com/en-us/dotnet/api/system.type.makegenerictype?f1url=https%3A%2F%2Fmsdn.microsoft.com%2Fquery%2Fdev15.query%3FappId%3DDev15IDEF1%26l%3DEN-US%26k%3Dk(System.Type.MakeGenericType);k(DevLang-csharp)%26rd%3Dtrue&view=netframework-4.7.2)* 将泛型类型参数替换为测试中指定的实际参数，得到*测试对象*的类型。
2.  再次使用 *MakeGenericType* 得到一个新类型，它是新构造的*测试对象*的*列表*
3.  将文件数据解析为 JSON
4.  将 JSON 数据反序列化为 *genericType* 并存储在 *[动态](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/dynamic?f1url=https%3A%2F%2Fmsdn.microsoft.com%2Fquery%2Fdev15.query%3FappId%3DDev15IDEF1%26l%3DEN-US%26k%3Dk(dynamic_CSharpKeyword)%3Bk(DevLang-csharp)%26rd%3Dtrue)* 类型中。我们需要在这里使用 dynamic，因为我们不知道在编译时传递给 *TestObject* 的类型，并且它们可以在每次测试中改变。
5.  将所有数据添加到一个对象列表中并返回它

* * *

这允许我们以下面的方式编写我们的测试

```
[Theory]
[JsonFileData("testData.json", "Part1", typeof(string), typeof(int))]
public void Test(List<string> data, int expectedResult)
{
    var result = TestThisMethod(data);
    Assert.Equal(expectedResult, result);
} 
```

* * *

## 参考文献

*   [xuit 文献资料](https://xunit.github.io/docs/getting-started-dotnet-core.html)
*   [代码的出现](https://adventofcode.com)
*   [创建一个定制的 xUnit 理论测试数据属性来从 JSON 文件中加载数据](https://andrewlock.net/creating-a-custom-xunit-theory-test-dataattribute-to-load-data-from-json-files/)
*   [MakeGenericType](https://docs.microsoft.com/en-us/dotnet/api/system.type.makegenerictype?f1url=https%3A%2F%2Fmsdn.microsoft.com%2Fquery%2Fdev15.query%3FappId%3DDev15IDEF1%26l%3DEN-US%26k%3Dk(System.Type.MakeGenericType);k(DevLang-csharp)%26rd%3Dtrue&view=netframework-4.7.2)
*   [动态](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/dynamic?f1url=https%3A%2F%2Fmsdn.microsoft.com%2Fquery%2Fdev15.query%3FappId%3DDev15IDEF1%26l%3DEN-US%26k%3Dk(dynamic_CSharpKeyword)%3Bk(DevLang-csharp)%26rd%3Dtrue)

## 结论

在本文中，我们构建了 Andrew 的自定义 JSON 数据源的基本实现，使我们更容易处理更大的数据集以及更复杂的数据。

这篇文章首先出现在 [Ankur Sheel 上——游戏程序员、旅行者和美食家](https://ankursheel.com)继续在[阅读来自 xUnit 测试 json 文件的负载测试数据](https://ankursheel.com/blog/load-test-data-from-a-json-file-for-xunit-tests/)
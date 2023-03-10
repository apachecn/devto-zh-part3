# 进行优雅的单元测试的过程

> 原文：<https://dev.to/thaianhduc/the-process-of-making-elegant-unit-tests-8po>

*原文发布于[我的博客](https://www.thaianhduc.com/2019/04/the-process-of-making-elegant-unit-tests/)2019 年 4 月 12 日*

单元测试是开发人员在构建软件时所做工作的一部分。一些开发人员可能不会编写单元测试。但是，在我看来，大多数人都这样认为。如果你是其中之一，你如何看待单元测试代码与产品代码的对比？

*   你会考虑可维护性吗？
*   你会*重构*测试以使其更好吗？注意:我使用了马丁·福勒的《重构》一书中的术语*重构*。
*   你利用了测试框架提供的优势了吗？

老实说，我没想过那么多。在我职业生涯的开始，我按照项目的当前结构编写测试。我没问那么多。随着时间的推移，我开始感到痛苦，所以我对单元测试进行了修改，从痛苦到快乐，这在我的项目中发挥了很好的作用。

## 写测试

最近，我有机会参与另一个项目。我的任务是编写单元测试(和集成测试),以适应这个系统，并能够使用多个凭证(也就是登录用户)运行测试。

这是一个测试，当然不是真正的测试。但想法是一样的。我想用不同的凭据运行测试。用户名和密码必须传递给参数。当你看测试报告时，这是非常有用的。通过参数化，报告将显示传递给测试的值。

```
[TestFixture]
internal class MultiCredentialsTest
{
    [Test]
    [TestCase("read_user", "P@ssword")]
    [TestCase("write_user", "P@ssword")]
    public void RunWithDifferentCredentials(string username, string password)
    {
        // The test body goes here.
    }
} 
```

而且会有很多。它像预期的那样工作。但是也有潜在的问题。你能猜出问题吗？

1.  当一个测试用户的用户名或密码被更改时会发生什么？
2.  如果我们想在测试套件中添加更多的测试用户，该怎么办？

想想他们有几百甚至几千人的情况。这将是一种痛苦。我需要一个集中测试数据的解决方案。我的过程已经开始了。

## 让他们更好管理

是时候看看 NUnit 提供了什么。NUnit 已经支持 [TestCaseSource](https://github.com/nunit/docs/wiki/TestCaseSource-Attribute) 。如果你还不知道，你应该先检查一下。简而言之，它允许开发人员以可管理的方式集中测试数据。那正是我一直在寻找的。

我创建了一个 TestCredentialsSource 来产生相同的测试数据。我更喜欢名称 TestCredentialsFactory，但是似乎“source”更适合单元测试上下文。

```
internal class TestCredentialsSource
{
    public static  IEnumerable<object> ReadWriteUsers = new IEnumerable<object>{
        new object[]{"read_user", "P@ssword"},
        new object[]{"write_user", "P@ssword"}
    }
} 
```

测试是用 V1 版本重写的。有两个版本可供比较。

```
[TestFixture]
internal class MultiCredentialsTest
{
    [Test]
    [TestCase("read_user", "P@ssword")]
    [TestCase("write_user", "P@ssword")]
    public void RunWithDifferentCredentials(string username, string password)
    {
        // The test body goes here.
    }

    [Test]
    [TestCaseSource(typeof(TestCredentialsSource), "ReadWriteUsers")]
    public void RunWithDifferentCredentials_V1(string username, string password)
    {
        // The test body goes here.
    }
} 
```

在测试中，我不需要处理测试值。测试数据封装在带有 ReadWriteUsers 静态字段的 TestCredentialsSource 中。

## 让它们变得更好——重用和复制

对于已知的特定用户来说，这很好。有一些特定的测试想要和特定的用户一起运行。使用*TestCredentialsSource*
中的另一个属性应该相当容易

```
internal class TestCredentialsSource
{
    public static  IEnumerable<object> ReadWriteUsers = new IEnumerable<object>{
        new object[]{"read_user", "P@ssword"},
        new object[]{"write_user", "P@ssword"}
    }

    public static   IEnumerable<object> SpecificUser = new IEnumerable<object>{
        new object[] {"special_user", "P@ss12345"}
    }
} 
```

如果我想只使用“read_user”进行测试呢？如果我想将“read_user”和“special_user”结合起来进行另一个测试，该怎么办？一种选择是在 TestCredentialsSource 中定义它们。这仍然很好，因为它仍然可以在单个文件中管理。但是很尴尬。

有更好的选择吗？

是的，有。让我们将数据封装在一个类中。欢迎来到 TestCredentials 课堂。

```
internal class TestCredentials
{
    public string Username { get; }
    public string Password { get; }
    public TestCredentials(string username, string password)
    {
        Username = username;
        Password = password;
    }
    ///<summary>
    /// Convert the object into an array of properties object which can be used by the TestDataSource
    ///</summary>
    public object[] ToTestSource() => new object[] { Username, Password };

    public static TestCredentials ReadUser = new TestCredentials("read_user", "P@ssword");
    public static TestCredentials WriteUser = new TestCredentials("write_user", "P@ssword");
    public static TestCredentials SpecialUser = new TestCredentials("special_user", "P@ss12345");
} 
```

该类提供了 3 个工厂方法来构造所需的凭据。这是唯一一个没有重复提供数据的地方。
测试证书来源变得干净多了

```
internal class TestCredentialsSource
{
    public static  IEnumerable<object> ReadWriteUsers = new IEnumerable<object>{
        TestCredentials.ReadUser.ToTestSource(),
        TestCredentials.WriteUser.ToTestSource()
    }

    public static   IEnumerable<object> SpecificUser = new IEnumerable<object>{
        TestCredentials.SpecialUser.ToTestSource()
    }
} 
```

酷！数据已经脱离了源定义。但是仍然有一件事我不太喜欢——TestCredentialsSource 中“SpecificUser”的设置。拥有一个单一值的来源对我来说不太合适。

有一个解决方案——将 *TestCredentials* 转换成 NUnit 可以理解的来源。实现`IEnumerable<TestCaseData>`。 *TestCaseData* 由 NUnit 框架
定义

```
internal class TestCredentials : IEnumerable<TestCaseData>
{
    public string Username { get; }
    public string Password { get; }
    public TestCredentials(string username, string password)
    {
        Username = username;
        Password = password;
    }
    ///<summary>
    /// Convert the object into an array of properties object which can be used by the TestDataSource
    ///</summary>
    public object[] ToTestSource() => new object[] { Username, Password };

    public static TestCredentials ReadUser = new TestCredentials("read_user", "P@ssword");
    public static TestCredentials WriteUser = new TestCredentials("write_user", "P@ssword");
    public static TestCredentials SpecialUser = new TestCredentials("special_user", "P@ss12345");

    public IEnumerator<TestCaseData> GetEnumerator()
    {
        return new List<TestCaseData>
            {
                new TestCaseData(Username, Password)
            }.GetEnumerator();
    }

    IEnumerator IEnumerable.GetEnumerator()
    {
        return GetEnumerator();
    }
} 
```

有了，我可以编写下面的 2 个测试。我可以对测试凭证进行任意组合。

```
[Test]
[TestCaseSource(typeof(TestCredentials), "SpecialUser")]
public void RunWithDifferentCredentials_SpecialUser(string username, string password)
{
    // The test body goes here.
}

[Test]
[TestCaseSource(typeof(TestCredentials), "SpecialUser")]
[TestCaseSource(typeof(TestCredentials), "WriteUser")]
public void RunWithDifferentCredentials_CombinedUsers(string username, string password)
{
    // The test body goes here.
} 
```

## 结束

好的结果并非偶然。我可以在这个过程的任何一步停下来。通过更进一步，通过问正确的问题，最终的结果超出了我的预期。如果你正在编写测试代码，你应该再看一遍并提出问题吗？试一试，看看你能走多远。
# 当读者阅读我的文章时，读者会写出更好的测试

> 原文：<https://dev.to/jamoyjamie/given-my-article-when-a-reader-reads-it-then-the-reader-writes-better-tests-16df>

# 给一个讨厌写测试的开发者

我不擅长测试，我最常用的副业项目只有大约 5%的测试覆盖率就是证明。我不能让自己辛苦地完成并编写我正在编写的代码的每个测试用例，尤其是当我没有太多的时间去做兼职项目的时候。

# 放在喜欢测试的软件屋

然而，在我目前的工作中，我被期望拥有 80%的代码覆盖率，这是称之为“稳定”的最低要求的一部分——因此我实际上花费了大部分工作时间来编写单元、集成和系统测试。

# 他必须在工作中编写大量的测试

测试是我们公司非常擅长的事情。我们有数十万行代码，分布在数十个不同项目的数百个不同的存储库中，在任何一天，我都可以在其中的任何部分工作。

事实是，这些测试让我们省去了无数头疼的事情，并且让疯狂的上下文切换变得简单多了。由于测试覆盖率高，我知道我可以修改代码，甚至可以执行大规模的重构，如果我愿意的话，在我暴跳如雷的最后，我可以运行测试，并在几分钟内知道代码的行为是否有任何变化。

我只是不需要在编辑代码时担心代码被破坏。

# 然后他在测试中学会了一些巧妙的技巧

这就是我要告诉你行为驱动测试的奇妙世界的地方。

我们有一个惯例，用 GIVEN-WHEN-THEN 结构编写每个测试。这是我的副业项目的一个例子:

```
[TestMethod]
public void TestReadStringDeserializesTheDataCorrectly()
{
    // GIVEN a buffer of serialized data
    mockMessageBuffer.Setup(m => m.Buffer).Returns(new byte[] { 0, 0, 0, 6, 65, 0, 66, 0, 67, 0 });
    mockMessageBuffer.Setup(m => m.Offset).Returns(0);
    mockMessageBuffer.Setup(m => m.Count).Returns(10);

    // WHEN I read a string from the reader
    string result = reader.ReadString();

    // THEN the value is as expected
    Assert.AreEqual("ABC", result);
} 
```

Enter fullscreen mode Exit fullscreen mode

我看到这个测试，立即知道在哪里可以找到我需要的一切:

*   在给定的部分中，我们设置我们需要的任何模拟，如果需要的话，从磁盘加载任何测试数据，并做我们必须做的任何准备。

*   在 WHEN 部分，我们运行被测试的代码(并且尽我们所能，*仅*被测试的代码)并获得结果。

*   在 THEN 部分，我们断言代码的行为与我们预期的一样。

有了这个简单的结构，理解一个测试做什么就变得简单了。我可以浏览开发和代码审查中的数百个测试，并在几秒钟内立即掌握测试正在做什么，因为我们公司的每个测试都有相同的行为驱动格式。

我们还发现这种风格也蔓延到了我们测试的其他部分:

```
[TestInitialize]
public void Initialize()
{
    // GIVEN the object cache is disabled
    ObjectCache.Initialize(ObjectCacheSettings.DontUseCache);

    // AND a DarkRiftReader under test
    reader = DarkRiftReader.Create(mockMessageBuffer.Object);
} 
```

Enter fullscreen mode Exit fullscreen mode

您也可以在这里开始看到我们如何构建更复杂的测试。如果你在任何部分做了多件事，建议你用 AND 部分来记录它们:

```
[TestMethod]
public void TestExtraLargeMemoryBlocksArePooledCorrectly()
{
    // GIVEN a memory pool with no previously pooled memory

    // WHEN I return an extra large memory block
    byte[] oldBlock = new byte[5000];
    memoryPool.ReturnInstance(oldBlock);

    // AND I request a new extra large memory block
    byte[] newBlock = memoryPool.GetInstance(4000);

    // THEN my memory block is the same
    Assert.AreSame(oldBlock, newBlock);
} 
```

Enter fullscreen mode Exit fullscreen mode

(我在这里走了一条捷径，我可能应该把`oldBlock`赋值给一个给定的变量，但是这也是可以理解的)

# 并且可以在任何地方应用它们

写测试的时候也有帮助！

用英语思考行为要容易得多——这就是伪代码和橡皮鸭调试之类的东西存在的原因——那么为什么不用你的母语编写测试，然后用它作为模板呢？

```
[TestMethod]
public void TestGetInstanceUsesPoolWhenHasInstances()
{
    // GIVEN a pool with a single element

    // WHEN I get an instance from the pool

    // THEN a new instance is not generated

    // AND the returned instance is the pooled object
} 
```

Enter fullscreen mode Exit fullscreen mode

```
[TestMethod]
public void TestGetInstanceUsesPoolWhenHasInstances()
{
    // GIVEN a pool with a single element
    object pooledObject = new object();
    objectPool.ReturnInstance(pooledObject);

    // WHEN I get an instance from the pool
    object result = objectPool.GetInstance();

    // THEN a new instance is not generated
    Assert.IsNull(newInstanceCaptor);

    // AND the returned instance is the pooled object
    Assert.IsNotNull(result);
    Assert.AreSame(pooledObject, result);
} 
```

Enter fullscreen mode Exit fullscreen mode

GIVEN-WHEN-THEN 的确切顺序也无关紧要，我们经常发现，在最复杂的测试中，我们需要在多个地方断言行为(THEN)，或者在继续设置(GIVEN)之前对测试中的类执行某个步骤(WHEN)

```
@free
Scenario: I can disconnect from a server
    Given I have a running server
    And 1 client connected
    Then all clients should be connected
    And the server should have 1 client
    When I disconnect client 0
    Then 0 clients should be connected
    And 1 client should be disconnected
    And the server should have 0 clients
    And there should be no recycling warnings 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

你不知道我的副业是做什么的，我从来没有提到过它，但是你仍然希望能够理解这些测试做了什么，尽管它们涵盖了我必须添加到它的一些最复杂和古怪的优化！

对我来说，采用这种行为会大大减少测试的麻烦。对于和我一起工作的开发人员来说，这使得理解我的测试更快更容易。在合并审查中，我们可以使用注释审查测试行为，然后对照注释检查代码，以使大量测试的审查更易于管理。

我希望这篇文章能让你思考，也许在你写下一个测试之前，你会写一个快速的给定时间🙂
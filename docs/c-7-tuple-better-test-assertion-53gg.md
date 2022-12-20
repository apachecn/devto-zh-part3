# C# 7 元组更好的测试断言

> 原文：<https://dev.to/thaianhduc/c-7-tuple-better-test-assertion-53gg>

*原文发布于[我的博客](https://www.thaianhduc.com/2019/04/the-process-of-making-elegant-unit-tests/)2019 年 4 月 18 日*

最近我在深度 4.0 中阅读了 [C#，在那里我在](https://www.manning.com/books/c-sharp-in-depth-fourth-edition) [C# 7](https://devblogs.microsoft.com/dotnet/new-features-in-c-7-0/) 中遇到了新的元组设计。这真的是一个很酷的功能。除了语法之外，它还提供了开发人员可以利用的能力。

在阅读它的时候，我的工作任务是编写单元测试。触发了我关于[语义对比与相似度](https://github.com/AutoFixture/SemanticComparison)的记忆。语义比较的主要思想是比较具有某些属性的两个对象。它允许开发者定义平等的含义。默认情况下，元组支持相等。因此，也许我应该能够使用元组来完成与相似性相同的事情。

在这篇文章中，我将编写一个简单的单元测试，没有相似性或元组，然后用相似性重构它，最后使用元组。让我们探索一些代码。

```
public class Product
{
    public Guid Id { get; set;}
    public string Name { get; set;}
    public double Price { get; set;}
    public string Description { get; set;}
}

[TestFixture]
public class ProductTests
{
    [Test]
    public void Test_Are_Products_Same()
    {
        var expectedProduct = new Product
        {
            Name = "C#",
            Price = 10,
            Description = "For the purpose of demoing test"
        };

        var reality = new Product
        {
            Name = "C#",
            Price = 10,
            Description = "For the purpose of demoing test"
        };

        // Assert that 2 products are the same. Id is ignored
    }
} 
```

任务很简单。我们如何断言这两种产品？

## 旧时尚

很简单。我们只是一个财产一个财产地主张。

```
[TestFixture]
public class ProductTests
{
    [Test]
    public void Test_Are_Products_Same()
    {
        var expectedProduct = new Product
        {
            Name = "C#",
            Price = 10,
            Description = "For the purpose of demoing test"
        };

        var reality = new Product
        {
            Name = "C#",
            Price = 10,
            Description = "For the purpose of demoing test"
        };

        Assert.AreEqual(expectedProduct.Name, reality.Name);
        Assert.AreEqual(expectedProduct.Price, reality.Price);
        Assert.AreEqual(expectedProduct.Description, reality.Description);
    }
} 
```

有些人可能认为我们应该在 Product 类中重写 Equals 方法。我认为这不是一个好主意。生产和单元测试之间相等的定义是非常不同的。在重写相等之前要小心。

产品类有 3 个属性(Id 属性除外)。所以代码看起来还是可读的。想想有 10 套房产的情况。

## 相似性——语义对比

有一篇[博文](https://www.rahulpnath.com/blog/semantic-comparison-improve-test-assertions/)详细解释了一下。在这个演示中，我们可以重写我们的简单测试。

```
[TestFixture]
public class ProductTests
{
    [Test]
    public void Test_Are_Products_Same()
    {
        var expectedProduct = new Product
        {
            Name = "C#",
            Price = 10,
            Description = "For the purpose of demoing test"
        }.AsSource()
        .OfLikeness<Product>();

        var reality = new Product
        {
            Name = "C#",
            Price = 10,
            Description = "For the purpose of demoing test"
        };

        Assert.AreEqual(expectedProduct, reality);
    }
} 
```

相似性是你测试工具箱中一个强有力的工具。如果你有兴趣的话，可以去看看。

## 元组定制

这个想法是，我们可以产生一个包含断言属性的元组，并对它们进行比较。如果需要，这允许我们展平结构。

```
[TestFixture]
public class ProductTests
{
    [Test]
    public void Test_Are_Products_Same()
    {
        var expectedProduct = (Name = "C#", Price = 10, Description = "For the purpose of demoing test");

        var reality = new Product
        {
            Name = "C#",
            Price = 10,
            Description = "For the purpose of demoing test"
        };

        Assert.AreEqual(expectedProduct, (reality.Name, reality.Price, reality.Description));
    }
} 
```

它可能看起来与相似性方法没有什么不同。我没有说哪种方法更好。这只是另一种做事方式。

## 总结

那么哪种方法更好呢？一个都没有。各有利弊。它们是你工具箱中的选项。如何使用它们取决于你，开发者。毫无疑问，我将在生产和单元测试代码中利用新的元组。
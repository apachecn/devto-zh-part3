# Symfony 项目中的六边形架构:使用域标识符

> 原文：<https://dev.to/apium_hub/hexagonal-architecture-in-a-symfony-project-working-with-domain-identifiers-1m25>

当我们谈论域标识符时，我们谈论的是我们的域中实体的 ID。很多时候，将一个 ID 分配给一个域实体的实例看起来是一个简单的过程，因此在很多情况下，这个重复的任务被分配给我们正在使用的 ORM(在我们的例子中是原则)。

在本文中，我们希望强调从我们自己的领域分配 ID 的重要性，展示它给我们带来的好处，并了解可以将此任务分配给外部代理(如 ORM)的问题(原则)。

在继续之前，我建议您(如果您还没有这样做的话)看看 Apiumhub 的[博客中关于六边形架构的第一篇文章，因为下面将要讨论的一些概念是在那里定义和解释的。](https://apiumhub.com/tech-blog-barcelona/applying-hexagonal-architecture-symfony-project/)

## Symfony 项目中的六边形架构:使用域标识符

### 为什么要给域名分配 ID？

根据我的经验，我工作过的许多带有 Symfony 的 PHP 项目都将 ID 分配给 ORM 中的实体。这种先验会给我们带来某些问题，例如，它使得测试非常困难，因为我们将实体的一部分构造委托给了外部代理。

为了更好地理解我所说的，我们将在一个例子中看得更清楚。让我们以第一篇文章中讨论六边形架构的产品实体为例:

```
 class Product
{
    private $id;

    private $name;

    private $reference;

    private $createdAt;

    private function __construct(
        string $name,
        string $reference
    ) {
        $this->name = $name;
        $this->reference = $reference;
        $this->createdAt = new DateTime();
    }

    public static function fromDto(CreateProductRequestDto $createProductResponseDto): Product
    {
        return new Product(
            $createProductResponseDto->name(),
            $createProductResponseDto->reference()
        );
    }

    public function toDto()
    {
        return new CreateProductResponseDto(
            $this->id,
            $this->name,
            $this->reference,
            $this->createdAt
        );
    }
} 
```

该类目前有一个 yml 格式的配置文件，在该文件中声明了到数据库的映射，并且 ID 的自动分配已经进行了增量配置:

```
 ProductBundle\Domain\Product:
  type: entity
  fields:
    id:
      type: integer
      id: true
      generator:
        strategy: AUTO

    name:
      type: string

    reference:
      type: string

  lifecycleCallbacks: {} 
```

正如我们所看到的，integer 类型的 ID 是自动递增分配的。乍一看，我们可以观察到两个问题:

困难的测试:在执行单元测试时，我们实体的 ID 将总是有一个空值，因为在我们的实体中没有一个 ID，直到它被保存在数据库中，根据定义，这是不应该的。

**外部代理的依赖性:**在构建实体的实例时，所述实例本身在我们的域中是有意义的，因此它应该从创建的那一刻起就具有分配的 id，而不管它是否已经在数据库中持久化(这将是执行具有当前配置的 ID 分配的时刻)。一个类的实例在被创建的同时应该是正确的，因为在构造函数本身中，我们将有所有必要的检查来允许它的创建。在最初的场景中，这不会发生，因为直到我们持久化实体，它才是完整的，这意味着尽管已经完成了它的构造，实体仍然是不完整的，因此是不正确的。

除了打破六边形架构最佳实践的这两个原则之外，我们还有一个明显的安全问题。如果我们想通过访问产品的 id 来公开产品的数据，我们公开的是一个自动生成的整数类型的 ID。因此，我们 API 的任何用户都可以访问我们产品的所有数据。我们也会努力解决这个问题。最后，通过不使用带有简单整数的 ids，如果我们在同一个数据库上同时使用几个服务器，我们还可以解决可能的冲突问题。

因此，我们将尝试改进我们的代码，迭代地应用上一篇文章中定义的关于领域实体的概念。

### 清除外部药剂

首先，我们必须从任何外部代理或依赖关系中抽象出我们的域，在这种情况下，我们将把实体 ID 的生成转移到实体的构造函数中，而不是从 yaml 文件中的配置中进行:

```
 private function __construct(
    string $name,
    string $reference
) {
    $this->id = Uuid::uuid4();
    $this->name = $name;
    $this->reference = $reference;
    $this->createdAt = new DateTime();
}

ProductBundle\Domain\Product:
  type: entity
  fields:
    id:
      type: string
      id: true

    name:
      type: string

    reference:
      type: string

  lifecycleCallbacks: {} 
```

这样，我们设法从 ORM 给我们的外部依赖中抽象出来。id 字段被定义为 string 类型的唯一标识符，但是它的生成是从域中给出的。

此外，当使用 ID **UUID** 类型时，我们按顺序消除对具有 id 的产品的访问，并且我们保证几乎不可能访问我们不想暴露用户的其他产品，以及 id 之间可能的冲突。

### 提高测试性

尽管如此，乍看之下，我们可以认识到，ID 的生成，尽管在我们的领域内，并不是在最好的地方。此时，我们不能正确地测试实体，因为我们没有办法预测对象一旦创建后的最终状态，因为 ID 是在实体本身的构造函数中随机生成的。

此外，我们只能创建新对象，而不能合并数据库中给定的对象，这一失败表明 ID 的生成不在适当的位置。

首先，我们将执行测试来验证实体的构造行为是否如预期的那样:

```
 public function testProductDomainEntity()
{
    $createRequestDto = new CreateProductRequestDto('nameTest', 'reference-124');
    $product = Product::fromDto($createRequestDto);
    $result = $product->toDto();

    $expected = new CreateProductResponseDto(
                    Uuid::uuid4(),
                    'nameTest',
                    'reference-124',
                    new DateTime()
                );

    self::assertEquals($expected, $result);
} 
```

我们可以看到，随着私有构造函数的定义，实体是通过静态方法*from to*构建的，带有*DTO createproductrequestto*。最后，为了从我们的实体中访问我们想要显示的数据，我们必须使用方法 toDto()来执行转换，以访问我们想要公开我们的实体的数据。正如您所猜测的，测试没有通过，因为 id 和 **createdAt** 不匹配。

这使我们认为，为了改进测试，我们必须提取我们实体的两个领域的创造:

```
 private function __construct(
    Uuid $id,
    string $name,
    string $reference,
    DateTime $createdAt
) {
    $this->id = $id->toString();
    $this->name = $name;
    $this->reference = $reference;
    $this->createdAt =$createdAt;
}

public static function fromDto(CreateProductRequestDto $createProductResponseDto): Product
{
    return new Product(
        $createProductResponseDto->id(),
        $createProductResponseDto->name(),
        $createProductResponseDto->reference(),
        $createProductResponseDto->createdAt()
    );
} 
```

如您所见，ID 和 createdAt 字段已被添加到 CreateProductRequestDto 中。这使得我们在测试中做了一些小的改变:

```
 public function testProductDomainEntity()
{
    $productId = Uuid::uuid4();
    $createdAt = new DateTime();
    $createRequestDto = new CreateProductRequestDto(
        $productId,
        'nameTest', 
        'reference-124',
        $createdAt
    );
    $product = Product::fromDto($createRequestDto);
    $result = $product->toDto();

    $expected = new CreateProductResponseDto(
        $productId->toString(),
        'nameTest',
        'reference-124',
        $createdAt
    );

    self::assertEquals($expected, $result);
} 
```

由于提取了 Id 和 createdAt 字段，我们有了绿色的测试，这允许我们在执行测试之前定义预期的结果。

## 添加域标识符

正如我们所看到的，即使在我们的 ID 的生成中提取了教义的依赖性，现在我们仍然依赖外部库来生成 UUID。如果我们想更进一步，改进我们的外部代理域的依赖性的抽象，我们可以创建我们自己的域 ID，其中我们将在这个*值对象*中封装外部库的依赖性:

```
 class ProductId
{
    private $id;

    private function __construct(string $id)
    {
        $this->id = $id;
    }

    public static function generate(): ProductId
    {
        return new self(Uuid::uuid4()->toString());
    }

    public function build(string $id): ProductId
    {
        if (Uuid::isValid($id)) {
            return new self($id);
        } else {
            throw new InvalidIdFormatException("Invalid ProductId format: ".$id);
        }
    }

    public function value(): string
    {
        return $this->id;
    }
} 
```

我们必须重构我们的域实体和 *createProductRequestDto* 来消除对 UUID 库的依赖:

```
 class Product
{
    private $id;

    private $name;

    private $reference;

    private $createdAt;

    private function __construct(
        ProductId $id,
        string $name,
        string $reference,
        DateTime $createdAt
    ) {
        $this->id = $id->toString();
        $this->name = $name;
        $this->reference = $reference;
        $this->createdAt =$createdAt;
    }

    public static function fromDto(CreateProductRequestDto $createProductResponseDto): Product
    {
        return new Product(
            $createProductResponseDto->id(),
            $createProductResponseDto->name(),
            $createProductResponseDto->reference(),
            $createProductResponseDto->createdAt()
        );
    }

    public function toDto()
    {
        return new CreateProductResponseDto(
            $this->id,
            $this->name,
            $this->reference,
            $this->createdAt
        );
    }
}

class CreateProductRequestDto
{
    private $id;

    private $name;

    private $reference;

    private $createdAt;

    public function __construct(
        ProductId $id,
        string $name,
        string $reference,
        DateTime $createdAt
    ) {
        $this->id = $id;
        $this->name = $name;
        $this->reference = $reference;
        $this->createdAt = $createdAt;
    }

    public function id(): ProductId
    {
        return $this->id;
    }

    public function name(): string
    {
        return $this->name;
    }

    public function reference(): string
    {
        return $this->reference;
    }

    public function createdAt(): DateTime
    {
        return $this->createdAt;
    }
} 
```

最后，用新的域标识符修改我们的测试:

```
 class ProductTest extends TestCase
{
    public function testProductDomainEntity()
    {
        $productId = ProductId::generate();
        $createdAt = new DateTime();
        $createRequestDto = new CreateProductRequestDto(
            $productId,
            'nameTest',
            'reference-124',
            $createdAt
        );
        $product = Product::fromDto($createRequestDto);
        $result = $product->toDto();

        $expected = new CreateProductResponseDto(
            $productId,
            'nameTest',
            'reference-124',
            $createdAt
        );
        self::assertEquals($expected, $result);
    }
} 
```

通过这最后一次迭代，我们已经将外部库 UUID 的依赖性减少到我们域中的一个点，在*值对象* **ProductId** 内。因此，如果将来我们想要修改我们的域的标识符的格式，我们应该只改变我们的域的单个点的依赖性。

## 结论:域标识符

通过这个非常简单的例子，我们已经能够展示 PHP 项目中最广泛的实践之一给我们带来的问题。正如我们所见，这是我们必须避免的做法。正如我们在上一篇文章中看到的，将我们的域与任何外部依赖隔离开来，保持我们的域的纯净和去耦是非常重要的。

通过这种实践，除了将我们的领域对外部代理的依赖最小化到单个和受控的点之外，我们已经设法测试了我们的实体的 100%,并且能够预见实体构造的最终结果，从而确保正确的行为。

最后，我们提高了数据的安全性，因为我们避免了暴露我们不想暴露的数据，避免了使用连续的 id。

如果你想了解更多关于域名标识符的信息，我强烈推荐你点击[这里](http://eepurl.com/cC96MY)订阅我们的每月简讯。

## 如果你觉得这篇关于域名标识符的文章很有趣，你可能会喜欢…

[将六边形架构应用于 Symfony 项目](https://apiumhub.com/tech-blog-barcelona/applying-hexagonal-architecture-symfony-project/)

[Scala 泛型 I: Scala 类型界限](https://dev.to/apium_hub/scala-generics-i--scala-type-bounds-38)

[Scala generics II:协方差和逆变](https://dev.to/apium_hub/scala-generics-ii-covariance-and-contravariance-in-generics-5dib)

[Scala generics III:通用类型约束](https://dev.to/apium_hub/scala-generics-iii-generalized-type-constraints-58km)

BDD:用户界面测试

[Scala 中泛型类型的 F-bound](https://apiumhub.com/tech-blog-barcelona/f-bound-scala-generics/)

[微服务 vs 整体架构](https://apiumhub.com/tech-blog-barcelona/microservices-vs-monolithic-architecture/)

[“几乎无限”的可扩展性](https://apiumhub.com/tech-blog-barcelona/almost-infinite-scalability/)

[iOS Objective-C app:成功案例研究](https://dev.to/apium_hub/protected-ios-objective-c-app-cornerjob-successfull-case-study-89e)

[年度移动应用开发趋势](https://dev.to/apium_hub/mobile-app-development-trends-of-the-year)

[Banco Falabella 可穿戴设备案例研究](https://apiumhub.com/tech-blog-barcelona/banco-falabella-wearable-ios-android/)

[移动开发项目](https://apiumhub.com/software-projects-barcelona/)

[面向 iOS 应用的 Viper 架构优势](https://apiumhub.com/tech-blog-barcelona/viper-architecture/)

[为什么是科特林？](https://dev.to/apium_hub/why-kotlin-language-android-why-did-google-choose-kotlin--639)

[软件架构会议](https://dev.to/apium_hub/apiumhub-software-architecture-meetups-in-barcelona-31df)

[安卓纯 MVP](https://dev.to/apium_hub/pure-model-view-presenter-in-android-1736)

[在 Java 中使用 Vavr 功能更强](https://dev.to/apium_hub/be-more-functional-in-java-with-vavr-5b4i)

Symfony 项目中的帖子[六角形架构:使用域名标识符](https://apiumhub.com/tech-blog-barcelona/hexagonal-symfony-domain-identifiers/)最早出现在 [Apiumhub](https://apiumhub.com) 上。
# 对象设计基础-第二部分

> 原文：<https://dev.to/olivermensahdev/basics-of-object-design-series-part-two-2g5>

继[上一篇关于对象设计的文章](https://dev.to/omensah/-basics-of-object-design-series---part-one-lo1)之后，我讲述了服务对象和一些有用的原则，它们可以帮助开发人员在面向对象的程序中设计对象。在这篇博文中，我将讨论除了服务对象之外的其他对象，这些对象可以在任何面向对象的程序中使用。

## 简介

众所周知，服务对象要么执行任务，要么返回一条信息。此外，还有其他类型的对象可以保存数据，并且可以选择公开一些操作或检索数据的行为。这些其他对象可以分为更具体的类型，即；实体、值对象和数据传输对象。

## 实体、值对象和数据传输对象

根据定义，实体是对象的核心，它们代表业务领域的重要概念，如预订、订购产品、客户等。作为开发人员，实体帮助您在应用程序中建模业务领域。例如，您可以使用预订实体对酒店预订进行建模。总之，一个实体持有相关的数据，它可以提供操纵和向其他对象公开一些有用信息的方法。关于实体的数据可能会随着时间的推移而改变，因此您不应该误认为这些改变发生在不同的对象中。这意味着一个实体可能会随着时间的推移而变化，相同的对象应该是经历变化的对象，而不是不同的对象。在这种情况下，实体需要是可识别的。例如,`SalesInvoice`实体在创建时返回相同的实体，因此，变化被记录在相同的实体中。

```
<?php
final class SalesInvoice
{
  private SalesInvoiceId $salesInvoiceId;
  public static function create(SalesInvoiceId $salesInvoiceId): SalesInvoice {
    $object = new self();
    $object->salesInvoiceId = $salesInvoiceId;
    return $object;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

考虑到实体的状态会随着时间的推移而改变，所以实体是可变的对象。它们有具体的实施规则。更改实体状态的方法应该是 void 返回类型。这些方法应该保护实体不被终止于无效状态。此外，他们不应该仅仅为了测试内部发生了什么而暴露他们所有的内部。相反，一个实体应该保存一个日志并公开它，这样对象就可以发现它发生了什么变化以及为什么发生了变化；

```
final class SalesInvoice
{
  /**
  * @var object[]
  */
  private array $events = [];
  // You can finalize it
  public function finalize(): void
  {
    $this->finalized = true;
    $this->events[] = new SalesInvoiceFinalized(/* ... */);
  }
  /**
  * @return object[]
  */
  public function recordedEvents(): array
  {
    return $this->events;
  }
}

// In a test scenario:
$salesInvoice = SalesInvoice::create(/* ... */);
$salesInvoice->finalize();
assertEquals(
  [
    new SalesInvoiceFinalized(/* ... */)
  ],
  $salesInvoice->recordedEvents()
); 
```

Enter fullscreen mode Exit fullscreen mode

值对象是完全不同的对象。它们通常小得多，只有一两种属性。它们代表领域概念，在这种情况下，它们代表实体的一部分。例如，在 SalesInvoice 实体中，我们需要 value 对象来表示销售发票的 ID、发票的创建日期以及每行产品的数量和 ID；

```
<?php 
final class SalesInvoiceId
{

}

final class Date 
{

}

final class ProductId
{
  public static function fromInt(int $productId): ProductId
{
// ...
}

}

final class Quantity
{
  public static function fromInt(
    int $quantity,
    int $precision
    ): Quantity {
    // ...
    }
}

final class SalesInvoice
{
  public static function create(
    SalesInvoiceId  $salesInvoiceId,
    Date $invoiceDate
  ): SalesInvoice{

  }

  public function addLine(
    ProductId $productId,
    Quantity $quantity
    ): void {
      $this->lines[] = Line::create(
      $productId,
    $quantity
    );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

值对象包装一个或多个基本类型值。我们不需要值对象是可识别的，我们不关心我们正在处理的确切实例，因为我们不需要跟踪值发生的变化。如果我们将该值转换为其他值，我们应该实例化一个新的副本来表示修改后的值。例如，当添加两个量时，我们返回一个新的 Quantity 对象来表示它们的和，而不是改变原始量的内部值；

```
<?php 
final class Quantity 
{
  private $quantity;
  private $precision;

  private function __construct(int $quantity, int $precision){
    $this->quantity = $quantity;
    $this->precision = $precision;
  }

  public static function fromInt(int $quantity, int $precision): Quantity{
    return new self($quantity, $precision);
  }

  public function add(Quantity $other): Quantity
  {
    // Assertion::same($this->precision, $other->precision);
    return new self(
      $this->quantity + $other->quantity,
      $this->precision
    );
  }
}

// A quantity of 1500 with a precision of 2 represents 15.00
$originalQuantity = Quantity::fromInt(1500, 2);
// The modified quantity represents 15.00 + 5.00 = 20.00
$newQuantity = $originalQuantity->add(Quantity::fromInt(500, 2)); 
```

Enter fullscreen mode Exit fullscreen mode

数据传输对象是您将在应用程序的边缘找到的对象类型，其中来自外部世界的数据被转换成应用程序可以使用的结构。它不必保护其状态，并公开其所有属性。不需要 getters 和 setters，这意味着对它们使用公共属性就足够了。它们通常用作命令对象，符合 ht 用户的意图，并包含实现其愿望所需的所有数据。这种命令对象的一个例子是下面的 ScheduleMeetup 命令，它表示用户希望安排具有给定标题和日期的会议；

```
<?php 
final class ScheduleMeetup
{
  public $title;
  public $date;
}

final class MeetupController
{
  public function scheduleMeetupAction(Request $request)
  {
    // Extract the form data from the request body:
    $formData = /* ... */

    // Create the command object using this data:
    $scheduleMeetup = new scheduleMeetup();
    $scheduleMeetup->title = $formData['title'];
    $scheduleMeetup->date  = $formData['date'];

    $this->scheduleMeetupService->__invoke($scheduleMeetup);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在 DTO 的情况下，这根本不是问题，因为 DTO 不会保护它的内部。因此，如果有意义的话，您可以向 d to 添加一个属性填充方法。例如，将表单数据或 JSON 请求数据直接复制到命令对象中。因为填充属性是 DTO 应该做的第一件事，所以将属性填充符实现为命名构造函数是有意义的:

```
<?php 
final class ScheduleMeetup
{
  public $title;
  public $date;

  public static function fromFormData(array $formData): ScheduleMeetup{
    $scheduleMeetup = new Self();

    $scheduleMeetup->title = $fromData['title'];
    $scheduleMeetup->date = $fromData['date'];
    return $scheduleMeetup;
  }
}

final class MeetupController
{
  public function scheduleMeetupAction(Request $request)
  {
    // Extract the form data from the request body:
    $formData = /* ... */
    $scheduleMeetup =  ScheduleMeetup::fromFormData($formData);
    $this->scheduleMeetupService->__invoke($scheduleMeetup);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

在这里，除了服务对象之外，我们还继续研究了面向对象应用程序中的其他对象，命名实体、值对象和数据传输对象。

我希望你喜欢它。接下来，我们将看看如何根据指导原则创建和使用这些对象。

## 进一步阅读

[Matthias Noback 的](https://twitter.com/matthiasnoback)描述是从实用设计原则的角度对[风格的对象设计指南](https://leanpub.com/object-design)的最好的深入讨论之一。

## 保持联系

当我们继续学习和探索更多关于软件工程的知识时，让我们保持联系。别忘了在推特或 T2【LinkedIn】和我联系
# 在 TypeScript 中使用区分联合的精确域建模

> 原文：<https://dev.to/miloszpp/precise-domain-modeling-with-discriminated-unions-in-typescript-3c8p>

在这篇文章中，我们将探讨 TypeScript 语言的一个有趣的特性。它被称为**有区别的联合**，也被称为**代数数据类型**。后一个名称来自函数式编程范例，其中这种类型被大量使用。

## 枚举类型的问题

让我先给你看一个可以通过歧视性结合解决的问题的例子。

您正在开发一个处理客户管理的应用程序。客户有两种:个人和机构。对于每种客户类型，您存储不同的详细信息:单个客户有一个名和一个姓以及一个社会保险号。公司有一个公司名称和一个税务标识符。

您可以使用以下类型对上述情况建模:

```
enum CustomerType {
    Individual,
    Institution
}

interface Customer {
    acquisitionDate: Date;
    type: CustomerType;
    firstName?: string;
    lastName?: string;
    socialSecurityNumber?: string;
    companyName?: string;
    companyTaxId?: number;
} 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，您必须将大多数字段设置为可选。如果没有，那么在创建`Customer`的实例时，您必须填写所有的字段。然而，当创建一个`Individual`客户时，您不希望填充`companyTaxId`。

这个解决方案的问题是，现在有可能创建在业务领域没有任何意义的实例。例如，您可以创建一个信息太少的对象:

```
const customer1: Customer = { 
    acquisitionDate: new Date(2016, 1, 1),
    type: CustomerType.Individual
}; 
```

Enter fullscreen mode Exit fullscreen mode

...或者提供的数据太多:

```
const customer2: Customer = { 
    acquisitionDate: new Date(2016, 1, 1),
    type: CustomerType.Individual,
    firstName: "John",
    lastName: "Green",
    companyName: "Acme",
    companyTaxId: 9243546
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果类型系统可以帮助我们防止这种情况，那不是很好吗？实际上，这是 TypeScript 应该做的，对吗？

## 受歧视的工会前来救援

通过有区别的联合，您可以更精确地对您的域建模。它们有点像枚举类型，但是也可以保存额外的数据。因此，您可以强制特定的客户类型必须有一组精确的字段。让我们看看它的实际效果。

```
interface IndividualCustomerType {
    kind: "individual";
    firstName: string;
    lastName: string;
    socialSecurityNumber: number;
}

interface InstitutionCustomerType {
    kind: "institutional";
    companyName: string;
    companyTaxId: number;
}

type CustomerType = IndividualCustomerType | InstitutionCustomerType;

interface Customer {
    acquisitionDate: Date;
    type: CustomerType;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们定义了两个接口。它们都有一个`kind`属性，该属性是一个**文字类型**。文字类型的变量只能保存一个特定的值。每个界面仅包含与给定类型的客户相关的字段。

最后，我们将`CustomerType`定义为这两个接口的联合。因为它们都有`kind`字段，TypeScript 将它们识别为不同的联合类型，并使使用它们更容易。

最大的收获是现在不可能创建`Customer`的*非法*实例。例如，以下两个对象都可以:

```
const customer1: Customer = { 
    acquisitionDate: new Date(2016, 1, 1),
    type: {
        kind: "individual",
        firstName: "John",
        lastName: "Green",
        socialSecurityNumber: 423435
    }
};

const customer2: Customer = { 
    acquisitionDate: new Date(2016, 1, 1),
    type: {
        kind: "institutional",
        companyName: "Acme",
        companyTaxId: 124345454
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

...但是 TypeScript 无法编译这个:

```
// fails to compile
const customer3: Customer = { 
    acquisitionDate: new Date(2016, 1, 1),
    type: {
        kind: "institutional",
        companyName: "Acme",
        companyTaxId: 124345454,
        firstName: "John"
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 与受歧视的工会合作

现在让我们看看如何实现一个函数，它接受一个`Customer`对象并根据客户的类型打印出客户的姓名。

```
function printName(customer: Customer) {
    switch (customer.type.kind) {
        case "individual": return `${customer.type.firstName}  ${customer.type.lastName}`;
        case "institutional": return customer.type.companyName;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，TypeScript 足够聪明，它知道`switch`语句的`customer.type`内部的`case "individual"`分支实际上是`IndividualCustomerType`的一个实例。例如，试图访问这个分支中的`companyName`字段会导致编译错误。我们会在一个`if`语句分支中得到相同的行为。

还有一个更有趣的机制叫做穷举检查。TypeScript 能够指出我们没有涵盖所有可能的客户类型！当然，如果我们有几十个而不仅仅是两个，这似乎会有用得多。

```
// fails to compile
function printName(customer: Customer) {
    switch (customer.type.kind) {
        case "individual": return `${customer.type.firstName}  ${customer.type.lastName}`;
        // case "institutional": return customer.type.companyName;
        default: const exhaustiveCheck: never = customer.type;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个解决方案利用了`never`类型。由于没有定义`case "institutional"`，控制落到`default`分支，其中`customer.type`被推断为`InstitutionCustomerType`类型，同时被分配给`never`类型，这当然会导致错误。

## 结论

受歧视的工会类型很酷。正如我所提到的，TypeScript 的全部目的是帮助我们发现在没有类型检查的情况下可能会犯的错误。有区别的联合帮助我们更详细地对域建模，因此使得不可能创建非法的实例。

## 免责声明

有人可能会争辩说，同样的事情可以通过继承(或者在这种情况下的接口扩展)来实现。这是事实。用继承来解决这个问题是一种面向对象的编程方法，而有区别的联合是特定于函数式编程的。我认为这种方法在 web 应用程序的上下文中更有意义，因为我们经常从一些不支持对象继承的 REST API 中获取数据。此外，用对象继承不可能实现穷举检查。

这是一个经典的*组合与继承*困境的例子。

## 想了解更多？

你喜欢这篇打字稿吗？我打赌你也会喜欢我的书！

[高级类型脚本](https://typescriptmasterclass.com)
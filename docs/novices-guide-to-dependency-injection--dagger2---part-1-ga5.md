# 依赖注入和危险新手指南 2 -第 1 部分-简介

> 原文：<https://dev.to/funkyidol/novices-guide-to-dependency-injection--dagger2---part-1-ga5>

本指南试图将难以理解的依赖注入概念转换成更容易理解的语言。这篇文章从我阅读的数百种资料中汲取了所有的智慧来理解这个概念，并在生产应用程序中使用它。

## 定义依赖注入(DI)

简单地说， *DI 正试图分离依赖创建和依赖调用*。

让我解释一下。

依赖项是任何必需的类或对象，但在当前范围内不可用。它可以是一个平台级的类，比如`Toast`，一个第三方库，比如`Retrofit`，或者是你自己在项目中其他地方定义的类。目前，每次你需要这些“依赖”中的任何一个时，你都试图使用`new`操作符来创建它的对象。或者，您可以尝试使用一些设计模式，如 Singleton 或 Factory，来调用所需依赖项的预先存在的对象。

```
class User {
   private ProfessionalDetails pDetails = new ProfessionalDetails();
   private AcademeicDetails aDetails = new AcademicDetails();
} 
```

但是现在让我要求你改变一下做事的方式。与其直接创建或访问您的依赖项，不如我告诉您，您需要的所有依赖项总是通过方法参数提供给您，您不必自己创建新对象。上面的代码看起来会像这样:

```
class User {
   private ProfessionalDetails pDetails;
   private AcademeicDetails aDetails;

   /* Example of Method (Dependency)Injection */
   public void userDetails(ProfessionalDetails pDetails, AcademicDetails aDetails){
      this.pDetails = pDetails;
      this.aDetails = aDetails;    
   }
} 
```

这叫做**方法(依赖)注入**。

正如我们在前面定义的那样，DI 将依赖项的创建和调用分离开来，在这里，您不用担心依赖项是在哪里或如何创建的，您只关心如何使用它们。类似地，如果你在类层次上需要的任何依赖通过如下所示的类构造函数提供给你，这就叫做**构造函数依赖注入**。

现在，作为第一步，我恳求我所有的读者坚持这种想法，不要创建新的依赖对象，而是通过方法或构造函数注入来获得依赖。更进一步，如果我可以要求你在方法中减少使用类变量，并尝试通过方法参数传递需求。当我们着眼于可测试代码的更大图景时，这将是有用的，但稍后会更多。
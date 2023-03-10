# 像专业人士一样建造物体！

> 原文：<https://dev.to/crisgoncalves84/the-fluent--facade-builder-pattern-9g>

设计模式提供了许多很好的方法，以一种奇特且容易理解的方式解决反复出现的复杂问题。

在整篇文章中，**我想向您展示**以及**应该如何使用构建器模式**，这样您就可以**以一种高效而优雅的方式构建您的业务逻辑的复杂对象**。

构建模式属于“创造模式”类别。如果您在 web 上查看这种模式，您会发现它的几种变体，这取决于您可能想要解决的特定问题。今天，我将向你展示两种常见的方法:流畅构建器和门面构建器。

### 流畅的建设者

我认为解释一个流畅的构建器如何工作的一个好方法是展示`StringBuilder`类的运行。

```
 StringBuilder sb = new StringBuilder()

    sb.append("A ")
        .append("big ")
        .append("black ")
        .append("bug ")
        .append("bit ")
        .append("a ")
        .append("big ")
        .append("black ")
        .append("dog ")
        .append("on ")
        .append("his ")
        .append("big ")
        .append("black ")
        .append("nose!")

        println sb.toString() 
```

通过查看`StringBuilder`的 *api* ，我们意识到这个类提供了一个**流畅的接口**(可链接的机制)，通过连续调用`append()`方法来构建长而复杂的字符串。这是通过在`append()`方法的最后返回构建器类本身来实现的:

```
 @Override
    public StringBuilder append(String str) {
        super.append(str);
        return this;
    } 
```

这是一个非常基本的例子，为了构建一个复杂的字符串，同一个方法被调用了几次。然而，应该使用相同的机制来调用所有可用的构建器方法，这样就可以无缝地构建对象。

#### 经典的例子

让我们假设您需要构建一个`Person`类的实例。

```
 class Person {

    String firstName
    String lastName
    int age
    String personalEmail
    String country
    String city
    String address
    String houseNbr
    String job
    String company
    String jobEmail
    String jobAddress

    Person(String firstName, String lastName, int age, String personalEmail, String country, String city, String address, String houseNbr, String job, String company, String jobEmail, String jobAddress) {
        this.firstName = firstName
        this.lastName = lastName
        this.age = age
        this.personalEmail = personalEmail
        this.country = country
        this.city = city
        this.address = address
        this.houseNbr = houseNbr
        this.job = job
        this.company = company
        this.jobEmail = jobEmail
        this.jobAddress = jobAddress
    }

} 
```

如您所见，该类本身有如此多的字段...创建一个默认的构造函数来接收所有这些必需的字段简直是疯了。

这种方法行不通。任何开发人员都可能在构造函数调用期间错误地切换这些字段中的任何一个。

你如何解决这个问题？！流畅的建设者模式来拯救！

基本上，毫无疑问，您只需提供一个人的构建器机制，每当有人想要获得一个人的实例对象时，都必须使用该机制。

为了实现这一点，您可以简单地实现一个`PersonBuilder`类，它包含构建 Person 对象所需的所有步骤，并在过程的最后返回对象本身。

我宁愿在 Person 的类中有一个静态的内部构造器类来达到同样的效果。

我们开始吧！

首先，我们必须**确保`Person`类提供了一个默认的空构造函数**供`Builder`使用，这样它就可以开始构建对象。**注意这个构造函数必须被声明为私有的，这样任何人——在类的范围之外——都不能通过它实例化一个人的对象！**。因为我们的 Builder 类将存在于 Person 的类范围内，所以它将能够访问构造函数。

```
 class Person {

(...)

private Person() {} // the empty private constructor to be accessed by the inner Builder class

} 
```

好了，现在我们有了默认的私有构造函数，是时候开始构建我们的 Person 的构建器了。

```
 class Person {

(...)

    static class Builder {
        Person p

        Builder() {
            p = new Person()
        }
    }

} 
```

*   **构建器内部类被声明为静态的**，因此它可以在 Person 的类作用域(`new Person.Builder()`)之外容易地被访问

*   **Builder 类在其构造函数**上声明并初始化一个空的 Person 实例，该实例将沿着其 Builder 方法构建。

`Builder`的职业框架已经准备好了。让我们添加所需的步骤(构建器方法)，这样我们就可以开始构建我们的人的对象:

```
class Person {

    (...)

    static class Builder {
        Person p

        Builder() {
            p = new Person()
        }

        Builder setFirstName(String aFirstName) {
            p.firstName = aFirstName
            return this
        }

        Builder setLastName(String aLastName) {
            p.lastName = aLastName
            return this
        }

        Builder setAge(int aAge) {
            p.age = aAge
            return this
        }

        Builder setPersonalEmail(String aPersonalEmail) {
            p.personalEmail = aPersonalEmail
            return this
        }

        Builder setCountry(String aCountry) {
            p.country = aCountry
            return this
        }

        Builder setCity(String aCity) {
            p.city = aCity
            return this
        }

        Builder setAddress(String aAddress) {
            p.address = aAddress
            return this
        }

        Builder setHouseNbr(String aHouseNbr) {
            p.houseNbr = aHouseNbr
            return this
        }

        Builder setJob(String aJob) {
            p.job = aJob
            return this
        }

        Builder setCompany(String aCompany) {
            p.company = aCompany
            return this
        }

        Builder setJobAddress(String aJobAddress) {
            p.jobAddress = aJobAddress
            return this
        }

        Builder setJobEmail(String aJobEmail) {
            p.address = aJobEmail
            return this
        }

        Person build() {
            return p
        }
    }

} 
```

注意，为了提供一个流畅的接口，我们需要在构建过程的每一步结束时返回`Builder`本身，除了在`build()`方法上，我们必须返回(完全构造的)Person 对象。

同样，`build()`方法应该只在对象构建完成时调用。

**使用人::建造者**

现在我们的`Builder`已经准备好了，让我们看看如何用它来构建一个人的对象:

```
 Person person = new Person.Builder() // the empty Person's object is created
            .setFirstName("John")
            .setLastName("Doe")
            .setAge(32)
            .setPersonalEmail("john.doe@email.com")
            .setCountry("USA")
            .setCity("Chicago")
            .setAddress("Some street address")
            .setHouseNbr("Some house nbr")
            .setJob("Software Developer")
            .setCompany("Easy Soft dot com")
            .setJobEmail("john.doe@easy.com")
            .setJob("some job address")

            .build() // returns the fully constructed object 
```

关于第一种方法，默认的构造函数接收许多参数，这无疑是一种更优雅和有效的方法。通过公开构建器的 *api* ，理解如何构建对象变得更加容易。

然而，我们可以让事情变得更有趣。在上面的用例中，我们使用单个构建器来构建对象。但有时，你的对象是如此复杂，以至于你需要将你的构建器分解成多个“低级”的构建器，它们将协同工作以提供构建对象所需的功能。

我说的是门面建造商。

### 门面建造者

Facade Builder 之所以得名，是因为它将 Facade 模式与 Builder 模式相结合，将多个流畅的界面合并成一个界面，从而构建非常复杂的对象。

我知道我们的`Person`对象并不复杂...但是我可以用它向您展示如何使用这个构建器模式变体来构建 Person 的对象。

通过查看`Person`类字段，让我们看看我们能从中获得多少构建者。

```
 class Person {

    // personal info
    String firstName
    String lastName
    int age
    String personalEmail

    // address
    String country
    String city
    String address
    String houseNbr

    // job profile
    String job
    String company
    String jobEmail
    String jobAddress
} 
```

乍一看，我们似乎可以确定 3 个不同的建设者:

1.  一个 **base Builder** ，包含此人的姓名、年龄和个人电子邮件
2.  一个**地址构建器**，负责保存构建个人地址的功能
3.  最后，一个**职位构建者**，负责构建这个人的职位档案。

那么，我们如何组合所有这些构建器接口来构建一个单独的 Person 对象呢？

通过遵守规则:

*   拥有基础构建器类(外观接口)
    *   基础构建器类**必须实例化一个人类的空对象**
*   所有其他的**构建器必须从上面的**基础构建器**扩展**
    *   那些**‘子构建器’必须在它们的构造函数**上接收**一个在基本构建器类上创建的空 Person 对象**的引用
*   **基础构建器**类**必须提供功能**，**允许**从**在**可用的**构建器**之间切换。

食谱在桌子上。是时候开始烹饪图案了！

我们将从 Facade Builder 开始(必需的 base Builder，还记得吗？)

```
 class Person {

    (...) 

    private Person() {}

    static class Builder {

        protected Person p

        protected Builder() {
            p = new Person()
        }

        Builder setFirstName(String aFirstName) {
            p.firstName = aFirstName
            return this
        }

        Builder setLastName(String aLastName) {
            p.lastName = aLastName
            return this
        }

        Builder setAge(int aAge) {
            p.age = aAge
            return this
        }

        Builder setPersonalEmail(String aPersonalEmail) {
            p.personalEmail = aPersonalEmail
            return this
        }

        AddressBuilder livesAt() {
            return new AddressBuilder(p)
        }

        JobBuilder worksAt() {
            return new JobBuilder(p)
        }

        Person build() {
            return p
        }
    }
} 
```

*   如您所见，我们将 Person 的构造函数保持为私有，因此我们保证 Person 对象只能通过基本的 Builder 类来构建。

*   我们的 base Builder 类是 Person 类的内部类，因此它可以访问 Person 的私有空构造函数。它还将`Person p`字段声明为受保护的，因此它只能由“低级”(子)构建器访问。

*   基本构建器类包含两个重要的方法，`livesAt()`和`worksAt()`，它们允许我们在对象构建过程中在基本构建器类与`AddressBuilder`和`JobBuilder`类之间切换。

门面构建器设置好了，现在让我们创建`AddressBuilder`和`JobBuilder`类

```
 class AddressBuilder extends Person.Builder {

    AddressBuilder(Person person) {
        p = person
    }

    AddressBuilder setCountry(String aCountry) {
        p.country = aCountry
        return this
    }

    AddressBuilder setCity(String aCity) {
        p.city = aCity
        return this
    }

    AddressBuilder setAddress(String aAddress) {
        p.address = aAddress
        return this
    }

    AddressBuilder setHouseNbr(String aHouseNbr) {
        p.houseNbr = aHouseNbr
        return this
    }
} 
```

```
 class JobBuilder extends Person.Builder {

    JobBuilder(Person person) {
        p = person
    }

    JobBuilder setJob(String aJob) {
        p.job = aJob
        return this
    }

    JobBuilder setCompany(String aCompany) {
        p.company = aCompany
        return this
    }

    JobBuilder setJobAddress(String aJobAddress) {
        p.jobAddress = aJobAddress
        return this
    }

    JobBuilder setJobEmail(String aJobEmail) {
        p.jobEmail = aJobEmail
        return this
    }
} 
```

*   “低级”构建器从基类扩展而来

*   它们还在它们的构造函数上接收一个对空 Person 对象的引用——来自基本的 Builder 类——这样它们就可以从被调用的那一刻开始构建对象。

事情看起来不错...看来我们的人的门面建设者是完全成立的。让我们来看看它的作用:

```
 Person person =
                new Person.Builder()
                        .setFirstName("John")
                        .setLastName("Doe")
                        .setAge(30)
                        .setPersonalEmail("john.doe@email.com")
                        .livesAt()
                            .setCountry("USA")
                            .setCity("Chicago")
                            .setAddress("10th Avenue")
                            .setHouseNbr("5400 N. Lakewood Avenue")
                        .worksAt()
                            .setJob("Software Developer")
                            .setCompany("Easy Soft dot com")
                            .setJobEmail("john.doe@easy.com")
                            .setJobAddress("12th Avenue")
                        .build() 
```

哇！这段代码看起来比上一段更酷。

通过组合可用的构建器，我们提供了一个门面接口，允许以一种更简单和直观的方式构建人的对象。此外，通过将构建步骤打包成多个分类的构建器，我们可以提供一个更有组织的 *api* ，在对象的构建过程中提供某种指导。

我没有做的一件事是在`build()`方法中添加一个`validate()`步骤，确保对象在返回之前满足所有必需的字段。这是至关重要的一步，然而(在我看来)这是这些方法的一个重要警告。

### 结论

不要用杂乱的构造函数制作复杂的对象，开始使用构建器模式方法:)

既然你已经知道了如何使用构建器模式，我希望从现在开始，每次你需要构建对象时，你都可以思考这个模式如何帮助你解决问题。

关于这种模式，你还有其他有用的信息与我们分享吗？别害羞，下面分享一下:)

保持调谐！
# 构建器模式，DSL 的第一步

> 原文：<https://dev.to/vga/builder-pattern-a-first-step-to-dsl-47de>

当您在寻找构建器模式的解释时，您可能会发现一些文章都展示了一个致力于创建对象的类。这很简单，我们可以用这个模式更深入。

让我们从下面的类`Person`开始:

```
public class Person {

   private String firstname;

   private String lastname;

   private String nickname;

   private String email;

   private String gender;

   private String country;

   private String city;

   public Person(String firstname, String lastname, String nickname, String email, String gender, String country, String city) {
       this.firstname = firstname;
       this.lastname = lastname;
       this.nickname = nickname;
       this.email = email;
       this.gender = gender;
       this.country = country;
       this.city = city;
   }
} 
```

从这段代码中我们可以看出，在创建新的 Person 对象时会出现问题:

*   构造函数太大，
*   构造函数参数都具有相同的类型。在使用时，我们必须记住每个参数的正确顺序。事实上，我们在伪装一种共同的代码味道:**原始的痴迷**。你可以看看这篇[文章](https://refactoring.guru/smells/primitive-obsession)，它解释了如何识别和处理它们。
*   我们如何设置约束条件？很难检查无效性或确保字段之间的一致性。

为了处理这些问题，我们可以添加几个构造函数。例如:

```
public Person(String firstname, String lastname) {
   this(firstname, lastname, null, null, null, null, null);
}

public Person(String firstname, String lastname, String nickname) {
   this(firstname, lastname, nickname, null, null, null, null);
}

public Person(String firstname, String lastname, String nickname, String email) {
   this(firstname, lastname, nickname, email, null, null, null);
} 
```

通过向我们的类 Person 添加不同的构造函数，结果是**构建责任被委托给了客户端**。想要创建一个对象的开发人员必须知道使用哪个构造函数以及为什么。此外，生成的代码难以阅读和理解:

```
Person john = new Person("john", "smith", "john"); 
```

在这段简单的代码中，很难知道名字、姓氏和昵称参数在哪里。我们必须检查源代码，检查哪些字段将被初始化。一个简便的解决方案是创建一个构建器类来解决这个可读性问题。

```
public class PersonBuilder {

   private final Person person;

   private PersonBuilder() {
       person = new Person();
   }

   public static PersonBuilder builder() {
       return new PersonBuilder();
   }

   public PersonBuilder withFirstname(String firstname) {
       if (firstname == null) {
           throw new IllegalArgumentException("firstname must be not null");
       }
       person.setFirstname(firstname);
       return this;
   }

   public PersonBuilder withLastname(String lastname) {
       if (lastname == null) {
           throw new IllegalArgumentException("lastname must be not null");
       }
       person.setLastname(lastname);
       return this;
   }

  ...

   public Person build() {
       return person;
   }
} 
```

然后，我们可以这样使用它:

```
Person john = PersonBuilder.builder()
   .withFirstname("john")
   .withLastname("smith")
   .build(); 
```

太好了！这种解决方案具有显式参数的优点。我们很容易理解一个人的名字是什么。此外，很容易为每个构建方法添加一个非空约束。此外，当每个方法返回 PersonBuilder 的实例时，它为我们提供了一个伪 [DomainSpecificLanguage](https://en.wikipedia.org/wiki/Fluent_interface) 。

但是，与构造函数不同的是，这个生成器不是自解释的，可能会被错误地使用。例如:

```
Person john = PersonBuilder.builder()
    .build(); 
```

这个最简单的例子揭示了缺乏指导。事实上，在创建阶段指导开发者是可能的。
例如，假设我们想将创作过程分为四个步骤:

*   开发者必须首先设置名字，
*   然后他可以设置姓氏，
*   然后他可以设置电子邮件
*   最后，他可以创建一个人对象。

为此，我们必须创建四个接口:

```
public interface StepFirstnameBuilder {

   StepLastnamePersonBuilder withFirstname(String firstname);
} 
```

这第一步使开发人员能够设置名字字段，然后使用第二步接口`StepLastnamePersonBuilder`。

```
 public interface StepLastnamePersonBuilder {

   StepEmailPersonBuilder withLastname(String lastname);
} 
```

```
public interface StepEmailPersonBuilder {

   FinalStepPersonBuilder withEmail(String email);
}

public interface FinalStepPersonBuilder {

   Person build();
} 
```

最后，在设置完所有必填字段后，我们可以访问`build`方法。
就是这样！现在我们可以修改`PersonBuilder`类来实现我们的四个步骤。

```
public class PersonBuilder implements StepFirstnameBuilder, 
StepLastnamePersonBuilder,
StepEmailPersonBuilder,
FinalStepPersonBuilder {

   private final Person person;

   private PersonBuilder() {
       person = new Person();
   }

   public static StepFirstnameBuilder builder() {
       return new PersonBuilder();
   }

   @Override
   public StepLastnamePersonBuilder withFirstname(String firstname) {
       if (firstname == null) {
           throw new IllegalArgumentException("firstname must be not null");
       }
       person.setFirstname(firstname);
       return this;
   }

   @Override
   public StepEmailPersonBuilder withLastname(String lastname) {
       if (lastname == null) {
           throw new IllegalArgumentException("lastname must be not null");
       }
       person.setLastname(lastname);
       return this;
   }

   @Override
   public FinalStepPersonBuilder withEmail(String email) {
       person.setEmail(email);
       return this;
   }

   @Override
   public Person build() {
       return person;
   }
} 
```

现在，让我们使用它:

```
Person john = PersonBuilder.builder() // -> returrns an instance of StepFirstnameBuilder
   .withFirstname("john") // -> returns an instance of StepLastnamePersonBuilder
   .withLastname("smith") // -> returns an instance of StepEmailPersonBuilder
   .withEmail("john@smith.com") // -> returns an instance of FinalStepPersonBuilder
   .build(); 
```

通过实现所有这些步骤，我们已经完全控制了一个人对象的构建方式。实际上，我们刚刚创建了一种特定于领域的语言。这种构建器模式是专门用于构建对象的 FluentInterface 的一个特例，它是一种表达对象构建方式的简单方式。

非常感谢[索妮丝](https://www.linkedin.com/in/sonyth-huber-57854424/)和[米凯尔](https://twitter.com/TsubaR00ck)的时间和校对。
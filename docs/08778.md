# 一个非常简单的测试驱动开发教程

> 原文：<https://dev.to/vepo/a-very-simple-test-driven-development-tutorial-3309>

现在我沉迷于测试驱动的开发，但是...在过去的某个时候，我认为这是个坏主意。现在没有这种技术，我无法开发任何代码。

在这篇文章中，我将介绍一些在日常工作中开始使用 TDD 的步骤。

# 提示

## 提示 1:知道你必须使用哪些工具

你使用哪种语言？我主要使用 Java 工作，多年来我从未听说过 Maven 以及如何将 Maven 与 JUnit 集成。所以没有 Maven 或 Gradle 或任何其他构建工具的 JUnit 什么都不是！我工作的公司里没有人开发 kwen Maven 或任何构建工具...那是某种业余制作软件。

当我被介绍给 Maven 时，在我自己使用 Gradle 几年后，我只问了一句:*“好的。它进行构建、运行测试并使应用程序可部署？*”。那正是我要找的！😀

当开始一个新项目时，制定一个路线图:

1.  安装构建工具: *Maven 或 Gradle for Java*
2.  配置测试工具:*为什么不使用 JUnit 5*
3.  使用断言创建您的第一个测试，并检查是否有效！

## 提示 2:了解 TDD 周期

红绿重构！这说得通吗？不，所以[读一下吧！这是你将使用 TDD 开发的方式。编写一个跌落测试，编写使测试工作的代码并重构它。](https://blog.cleancoder.com/uncle-bob/2014/12/17/TheCyclesOfTDD.html)

在测试成功之前，不要担心重复的代码，或者难闻的气味，或者你的代码是不是又好又漂亮！解决问题就好。现在不是说这个的时候。

小步前进。不要担心在测试前编写完整的特性。测试代码的每一步。如果您应该在数据库中保存一个实体，首先保存它，然后测试这个实体的每一种输入，然后编写所有的业务规则。编写完整的特性然后进行测试是浪费时间。小步前进您可以在错误变大之前修复它，并确保您一小时前编写的代码运行良好。

## 技巧 3:验证你的代码

通过测试意味着什么？对于一个开发团队来说，这意味着给出一个至少 80%的覆盖率报告！对吗？不要！你应该验证你所做的。

```
User user = givenUser();
userService.createUser(user); // here I have 100% of Code Coverage

List<User> users = userService.list();
assertThat(users).hasSize(1) // But I do not know if it was save
                 .allMatch(this::passwordIsEncrypted); // And if the password is correctly encrypted 
```

## 提示 4:了解好的测试库

Mockito、AssertJ、to 字符串验证器等...这会对你有帮助。相信我！不要改造一个库，重用它。有很多好的测试库。

## 技巧 5:组织你的代码进行测试

编写生产代码时，考虑谁可以测试它。

假设您有一个包含许多服务消费者和提供者(Kafka、 *MQ、Aws* 、Google*等)的代码..).每次创建新的生产者或消费者时，都应该由同一段代码创建！当你这样做的时候，很容易为这个类创建一个模拟。如果你有一个 KafkaClientFactory，你确实需要担心一个新的 KafkaProducer，只要用 Mockito 来嘲讽 KafkaClientFactory 就开心了

如何构建单例？

```
public class SomeSingleton {

    private static final instance AtomicReference<SomeSingleton> ref = new AtomicReference<>();

    public static SomeSingleton get() {
        return instance.updateAndGet(e -> {
            return isNull(e) ? new SomeSingleton() : e;
        });
    }
} 
```

这样的实现好吗？不要！为什么？如果我想清理这个单例并重新创建？如何访问字段`ref`？不可以！您必须选择，或者您让字段`ref`作为一个`package private`，或者您创建一个清理方法作为包私有，并在同一个包中创建一个 SomeSingletonHelper 作为代码测试来重置所有值。

意图是想，当你在写代码的时候，我怎么能测试我的代码。

## 提示 6:知道输入输出

创建测试时，您应该知道代码将接收的输入类型，以及应该提供的输出类型。使用已知的输入，而不仅仅是自动生成的。这有助于你确保它的工作

## 提示 7:使用真实输入

使用真实输入，而不仅仅是生成/虚拟输入。发现一个生产 bug？得到什么输入产生了 bug，用它创建一个测试！

## 技巧八:测试语言

为您的代码创建一个测试 DSL。

```
User user = new User();
user.setName(randomString());
user.setPassword(randomString());
userService.create(user)

List<User> allUsers = userService.list();
assertThat(allUsers).hasSize(1); 
```

将上面的代码与下面的代码
进行比较

```
userService.create(givenRandonUser())

assertThat(thenAllUsers()).hasSize(1); 
```

为你的代码构建一个高级的[给定时间](https://en.wikipedia.org/wiki/Given-When-Then)库

# 结论

测试不是浪费时间！当你从未做过 TDD 的时候，你会这样想，但是有了 TDD，你可以更好地利用你的时间。
# 尤达条件— Aprender devo eu？

> 原文：<https://dev.to/omarkdev/yoda-conditions-aprender-devo-eu-54kc>

<figure>[![](img/f4d7f0fe1005989a577ee327d69c488c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4HGu-Zw_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AXpaAh2IHb-c17Gqznc8f2A.jpeg) 

<figcaption>尤达学习语法</figcaption>

</figure>

可能是在您花时间编程的某个时候，您在 if 中分配了一个变量，该变量本来不存在，但由于某种原因，您忘了放另一个=并退出了 name = "Yoda "。这肯定产生了一些错误，因为没有报告错误，所以一定很难找出是什么。**尤达条件**正是来解决这个问题的。

> 在[编程](https://en.wikipedia.org/wiki/Computer_programming) [行话](https://en.wikipedia.org/wiki/Jargon)， **Yoda 条件**(也称为 Yoda 符号)是一种[编程风格](https://en.wikipedia.org/wiki/Programming_style)，其中表达式的两个部分与[条件语句](https://en.wikipedia.org/wiki/Conditional_(computer_programming))中的典型顺序相反。Yoda 条件将表达式的常数部分放在条件语句的左侧。这种编程风格的名字来源于《星球大战》中一个名叫[尤达](https://en.wikipedia.org/wiki/Yoda)的角色，他用一种不标准的语法说英语。

Yoda 条件的想法是，我们应该反转条件表达式，把不能赋值的运算符放在左边。反转条件的自然顺序。

所以，如果我们有这样的代码，

```
if (age = 18) {
    ...
} 
```

它将始终有效，因为变量赋值会生成一个 true 值。而如果我们这样做，

```
if (18 = age) {
    ...
} 
```

因此，常量 18 不能更改其值，因此当我们尝试更改该常量的值时，将引发异常。这样我们就可以更容易地发现错误。

### 何时使用或不使用？

此编程样式的唯一目的是在执行比较时获取不需要的变量分配。不久， ***当某个变量意外地被赋予新值*** 的机会出现时，应使用。让我们举几个例子。

#### 例 1

```
if (user.getName() == "Barbara Liskov") {
    ...
} 
```

此表达式不需要使用此样式，因为如果尝试为表达式赋值，则会引发异常，因为我们无法为函数调用赋值。但是，某些开发人员可能会选择在这种情况下使用该样式，以便稍后重新编写代码并将函数调用替换为变量。

#### 例 2

```
if (userEmail != user.getEmail()) {
    ...
} 
```

在此表示式中，您必须使用此型式，因为我们很容易忘记否定运算子，这样做可能会变更 userEmail 变数的值。

#### 例 3

```
if (userEmail == authenticatedUserEmail) {
    ...
} 
```

你怎么看？如果认为****有必要**使用的话，就打中了。在此表示式中，我们反转顺序没有关系，因为两者都是变数，所以即使我们忘记了等号运算子，也没有什么可以做的，因为无论如何都会有指派。**

 **#### 例 4

```
if (userAge >= 18) {
    ...
} 
```

最后，在此表示式中不需要使用此型式，因为它不是平等的表示式，而是要知道使用者年龄是否大于或等于 18 岁。在这里使用尤达条件会使这个代码更难阅读。建议仅在等式表达式中使用 Yoda 条件。

### 好处很清楚，但是……

很容易理解使用尤达条件有什么好处，但和这种生活中的一切一样，我们采用这种编码方式会产生副作用。

主要的副作用是使代码更难阅读。许多这种编程风格的批评者认为，缺乏可读性是一种无法被它给你带来的好处所超越的东西。您的部分工作是为其他开发者编写可读的代码，这可能会成为您团队中某些开发者的一个问题。

对于这种编程方式的批评者很多，在这些批评者中间有‘t0’鲍勃叔叔(罗伯特 c .Martin) 是软件开发领域相当强势的人物，主要在*软件设计*、*清洁代码*等问题上。他在博客上说，

> “我不喜欢代码中任何会让读者重复阅读的语句。以牺牲读者为代价保护作者的代码是有缺陷的代码。”—鲍勃叔叔

也许与其采用这种编程方式，不如在项目中采用一种能在条件下提醒您角色的，此外，今天我们已经有一些编译器在进行此类分配时显示警告。更不用说诸如***【python】***和***【swift】***等一些语言不允许在条件下进行分配。

在诸如***【Java】***等语言中可能成为巨大问题的另一个副作用，以及对空指针的延迟感知。问题是，空指针在运行时会得到更好的理解，如果忽略空指针，则可能会导致代码中出现漏洞。你糊涂了吗？举个例子吧，

```
class Bank {
    public void creditAccount(Customer cst, int amt) {
        //Verification steps.
        if ("closed".equals(cst.getAccountState()) {
            throw new RuntimeException("Account is closed.");
        } else {
            cst.credit(amt);
        }
    }
}

class Customer {
    private String state;
    public Customer(){
    }
    public void setAccountState(){
        this.state = "open";
    }
    public String getAccountState(){
        return this.state;
    }
    public void credit(int amt) {
        //Logic to credit account;
    }
}

public class Main {
    public static void main(String[] args) {
        Bank bank = new Bank();
        Customer customer = new Customer();
        bank.creditAccount(customer, 100000);
    }
}

// Exemplo retirado de Yoda Conditions its advantages and disadvantages. 
```

在此示例中，我们可以看到 Customer 类的状态尚未初始化，因此它是一个空值，因此，当我们在 creditAccount 方法中比较 Bank 类内部时，我们无法确定某个帐户何时存在，因此我们有一个很难跟踪的漏洞。

尽管采用这种代码样式会产生副作用，但这些副作用可能不会在您的项目中变得如此重要，所以您只能在尝试时才知道。也许对你来说很有意义而且很好用，也可能是一坨屎，最后你只能通过测试才能发现。

### Alguns cases

也许这个概念对你来说很有名，也许你从来没有听说过，但在编程界，这已经不再是什么新鲜事了。

我们在***【PHP】***地区有两个非常有名的事例，它们在其标准编码中使用这种编程风格，分别是**【WordPress】**和**【symfony】**。即使是这些标准编码，他们也说我们永远把常量或文字放在左边，不管表达式是什么。

*   [编码标准做 WordPress](https://make.wordpress.org/core/handbook/best-practices/coding-standards/php/#yoda-conditions)
*   [编码标准做 Symfony](https://symfony.com/doc/current/contributing/code/standards.html)

**推特**:[*https://twitter.com/omarkdev*](https://twitter.com/omarkdev)

***Github:***[*https://github.com/omarkdev*](https://github.com/omarkdev)

* * ***
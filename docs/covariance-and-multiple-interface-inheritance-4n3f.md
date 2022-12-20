# 协方差和多界面继承

> 原文：<https://dev.to/jessekphillips/covariance-and-multiple-interface-inheritance-4n3f>

*最初发布于 2014 年[我的博客](https://he-the-great.livejournal.com/54436.html)T3】*

(用于接口、类、函数的名称可能不能描述好的设计；选择它们只是为了更清楚地表达这种能力)

```
interface AAAAA { aaaaa aaA(); }
interface aaaaa {}

class AaAAaA : AAAAA {
    aaaaa aaA() {
        return aaaaa.init;
    }
}

class aAA : AaAAaA, aaaaa {
} 
```

Enter fullscreen mode Exit fullscreen mode

不好意思。我想我的电脑刚刚打喷嚏了。

今天发生了一件有趣的事情，我在和 C#争论类型安全。而且看来这个[郎。接下来的](http://channel9.msdn.com/Events/Lang-NEXT/Lang-NEXT-2014/Panel-Type-Systems-and-Much-More)演讲是关于对/协变和为什么有它的一些讨论。

我更喜欢静态打字。我希望编译器在使用错误的东西时大喊，我非常依赖这种能力。人们喜欢动态语言中的 REPL，因为他们可以尝试一些东西，看看它们是如何工作的，类型允许相同的原则。在结构和行为方面对类型进行一些修改，编译器会解释什么时候有人不遵守他们的约定。这提供了重新评估设计选择或更新类型以匹配变化的机会。

出于这个原因，我希望以一种方式来构建我的代码，使得编译器能够验证契约的履行。这意味着强制转换不是一个选项，这样的操作告诉编译器忽略它所知道的，并假设对象将满足所需类型的规则。函数返回类型的协方差是实现这些目标所需要的特性，C#缺少它。我研究了参数化我的类型，但这并不适合整体；我已经选定了[显式接口实现](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/interfaces/explicit-interface-implementation)，它只是增加了 boiler plate，我没有 mixins 来解决这个问题。

然而，d 确实提供了这样一个特性，让我们深入研究一下。

```
class A {
    A foo() { return A.init; }
}

class B : A {
    override B foo() { return B.init; }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的想法是[在返回类型上提供协方差](http://en.wikipedia.org/wiki/Covariance_and_contravariance_%28computer_science%29#Inheritance_in_object_oriented_languages)，这在 C#中是无效的。请注意，类 B 已经覆盖了类 A 的 foo()，但是它没有遵守 foo()返回 A 的约定，而是返回 B，因为我们知道类 B 是 _a A，所以进行这种更改是类型安全的。当调用 a.foo()时，已知“A”的类型是 A 类，对象是 B 类，则返回的类型将是 A。

希望我设计的这个例子能说明为什么这很棒。请不要认为这是一种设计模式，要理解它的工作原理，如果遇到这种应用的原因，那么请不要因为你编写的语言不支持它而感到沮丧。

```
interface LoanOffice {
    LoanItem getItemOfLoan();
}
interface LoanItem {} 
```

Enter fullscreen mode Exit fullscreen mode

LoanOffice 将提供一个方法来获取有贷款的项目。我在考虑金融贷款，所以 LoanItem 可能只是关于贷款的文书工作。贷款办公室真的很傻，因为他们只发放一笔贷款，他们可能会做一些其他有用的事情，比如补充库存，但让我们专注于这一笔贷款。

```
interface CarDealer : LoanOffice {
    Car getItemOfLoan();
}

interface CamperDealer : LoanOffice {
    Camper getItemOfLoan();
}
interface Car : LoanItem {}
interface Camper : LoanItem {} 
```

Enter fullscreen mode Exit fullscreen mode

这里有一些特定类型的 LoanOffices，每一个都将使用协方差来提供一个非常特定的 LoanItem。基本上与 A 类和 B 类的例子相同，但是使用了接口。

```
class RVDealer : CarDealer, CamperDealer {
    interface Truck : Car {}
    class RV : Truck, Camper {}
    override RV getItemOfLoan() {
        return RV.init;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在是 CarDealer 和 CamperDealer 的第一个实现。出售房车的人会有一个租借办公室，提供汽车租借项目和露营车租借项目，因为房车是这两者。我还加入了卡车接口，为了便于讨论，它是一辆汽车。卡车可以在协方差的路径上走得更远，如果确实有成为汽车的要求的话。

```
void handleCarLoan(Car c) {}
void handleHomeLoan(Camper d) {} 
```

Enter fullscreen mode Exit fullscreen mode

这是为什么我想要协方差的一个简化。在本例中，我展示了接受特定借项的函数(实际上我的例子是有效地与经销商一起工作)。RVDealer 应该能够为这两个函数提供 LoanItem，并且应该没有强制转换的理由。RVDealer 已经知道它提供的类型满足两个契约，即使 Car 和 Camper 是不同的类型。

```
void main() {
    auto rvDealer = new RVDealer();
    handleCarLoan(rvDealer.getItemOfLoan());
    handleHomeLoan(rvDealer.getItemOfLoan()); 
```

Enter fullscreen mode Exit fullscreen mode

d 允许调用这两个函数，因为它已经验证了这些类型将满足所有需要的契约。C#需要进行强制转换，消除所需的编译时检查。注意:如果我将 RVDealer 存储到 CarDealer 或 LoanOffice 变量中，编译器不会允许这两个调用都成功。这是因为类型信息表明 getItemOfLoan()将分别返回 Car 和 LoanItem，而不是 Camper。

```
 void handleCarLoan(RVDealer.Truck c) {}
    handleCarLoan(rvDealer.getItemOfLoan());
} 
```

Enter fullscreen mode Exit fullscreen mode

如果您想知道，handleCarLoan()可以专用于我们的卡车接口，我们的 RVDealer 将为我们提供满足该要求的类型。

# 结论

对函数返回类型使用协方差的能力极大地有助于消除类型转换和模板。我不会说它有频繁的需求，但它提供了选项，并在逻辑上遵循接口的契约。
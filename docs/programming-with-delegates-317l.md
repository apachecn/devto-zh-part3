# 委托编程

> 原文：<https://dev.to/jai00271/programming-with-delegates-317l>

那么英语中的代表是什么呢？代表是指被派遣或授权代表他人的人，特别是被派遣参加会议的当选代表。现在我们不断听到两国代表会面并讨论一些潜在问题的消息。类似地，委托只不过是两个实体之间的通信，其中实体之间可以交换一些信息。

根据 Wiki，在面向对象编程中，委托是指在另一个原始对象(发送方)的上下文中评估一个对象(接收方)的成员(属性或方法)。在 c#编程世界中，回调与事件相关联，当事件发生时，使用委托触发回调方法。例如，当你点击一个按钮时，一个事件被触发，一个相应的回调动作被执行。但是程序如何理解执行哪个回调呢？这是通过委托完成的。委托保存对方法的引用(出于同样的原因，委托也被称为函数指针),并可用于链接回调方法。

## 委托在 c#中定义为:

[修饰]delegate[return _ type][delegate _ name](https://dev.to%5Bparameter_list%5D)；

示例:

公共委托 int GoForestGo(int G，int F，int G)；

修饰符:是定义委托访问的必需修饰符，可以选择使用。
委托人:用于定义委托人的关键字。return_type:这是委托将要调用的方法返回的值的类型。它可以是空的。方法必须与委托具有相同的返回类型。
delegate_name:代表的自定义名称或标识。parameter_list:包含通过委托调用时方法所需的参数。

下面是一个简单委托的例子，我们创建了一个类并声明了一个委托，它将接受一个数字并执行打印操作。让我们声明一个方法 LetsPrint，它打印所有能被 2 整除的数字。我们为每一个成功的场景调用一个回调，并打印出数字。

公共委托 void 回调(int I)；

```
public class Program
{
    private static void Main(string[] args)
    {
        NewClass instance = new NewClass();
        instance.LetsPrint(CallBack);
    }

    private static void CallBack(int i)
    {
        Console.WriteLine(i);
    }
}

public class NewClass
{
    public void LetsPrint(CallBack obj)
    {
        for (int i = 0; i < 100; i++)
        {
            if (i % 2 == 0)
            {
                obj(i);
            }
        }
    }
} 
```

## 组播代理人

委托可以保存对一个或多个方法的引用。在上面的例子中，我们的委托只持有一个回调方法的引用，但是当一个委托持有多个引用时，它被称为多播委托。请看下面的代码片段:

公共委托 void 回调(int I)；

```
public class Program
{
    private static void Main(string[] args)
    {
        NewClass instance = new NewClass();
        CallBack callBack = null;

        callBack = CallBack_Method1;
        callBack += CallBack_Method2;

        instance.LetsPrint(callBack);
    }

    private static void CallBack_Method1(int i)
    {
        Console.WriteLine("Callback1: " +i);
    }
    private static void CallBack_Method2(int i)
    {
        Console.WriteLine("Callback2: " + i);
    }
}

public class NewClass
{
    public void LetsPrint(CallBack obj)
    {
        for (int i = 0; i < 10; i++)
        {
            if (i % 2 == 0)
            {
                obj(i);
            }
        }
    }
} 
```

在上面的例子中，我们通过将两个回调链接在一起来调用它们。类似地，我们可以将多个回调链接到一个委托。我们调用 LetsPrint 并传递链式委托，这些委托将在每次成功执行后执行。

我希望你看完文章后已经理解了委托和回调的概念。请分享您的宝贵反馈。
# F#中受歧视的联合简介

> 原文：<https://dev.to/manuelsidler/short-introduction-to-discriminated-unions-in-f-1h8l>

> 伊利亚·波什科夫在 [Unsplash](https://unsplash.com/search/photos/f%23?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的封面照片

在过去的几个月里，我一直在想 F#。如果你想开始学习 F#的话，我可以强烈推荐你使用艾萨克·亚伯拉罕的 F# 进行编程，尤其是当你像我一样有 C#背景的时候。
今天我学到了一些关于 F#中歧视性结合的东西。当你需要建立一个关系模型时，你可以使用阿杜。但是首先，在像 C#这样的面向对象语言中，这种关系是什么样的呢？

```
public abstract class Animal
{
    public abstract string Talk();
} 
```

Enter fullscreen mode Exit fullscreen mode

```
public class Dog : Animal
{
    public override string Talk()
    {
        return "Woof!";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
public class Cat : Animal
{
    public bool IsAggressive { get; set; }

    public override string Talk()
    {
        return IsAggressive
            ? "MEEEEEEEEOOOOOOOW!!!"
            : "Meow";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

```
// usage
var dog = new Dog();
dog.Talk(); // Woof!

var cat = new Cat();
cat.Talk(); // Meow

var aggressiveCat = new Cat { IsAggressive = true };
aggressiveCat.Talk(); // MEEEEEEEEOOOOOOOW!!! 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们看看 F#中的等价形式:

```
type Animal = // base type
    | Dog // no custom fields
    | Cat of IsAggressive:bool // add custom field

// add function with the help of pattern matching
let talk animal =
    match animal with
    | Dog -> "Woof!"
    | Cat aggressive when aggressive = true -> "MEEEEEEEEOOOOOOOW!!!"
    | Cat _ -> "Meow" 
```

Enter fullscreen mode Exit fullscreen mode

```
// usage

let dog = Dog
dog |> talk // Woof!

let cat = Cat false
cat |> talk // Meow

let aggressiveCat = Cat true
aggressiveCat |> talk // MEEEEEEEEOOOOOOOW!!! 
```

Enter fullscreen mode Exit fullscreen mode

很整洁，不是吗？我真正喜欢 F#方法的地方是，我一眼就能看到整个类型层次结构。当然，将所有 C#类放入一个文件在技术上是可能的。但是你肯定会遵循“每个文件一个类”的惯例；-)

你可以在 [*F#上为了乐趣和利益*](https://fsharpforfunandprofit.com/posts/discriminated-unions/) 或 [*微软文档*](https://docs.microsoft.com/en-us/dotnet/fsharp/language-reference/discriminated-unions) 上阅读更多关于歧视工会的内容。
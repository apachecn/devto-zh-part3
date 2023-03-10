# C#？在虚幻中？使用 Mono-UE 加速 UE4 开发

> 原文：<https://dev.to/brendanlobuglio/c-in-unreal-speeding-up-ue4-development-with-mono-ue-3ci1>

“我应该让我的游戏不真实还是统一？”是游戏开发者经常遇到的难题，最大的症结之一是 Unity 对 C#的使用，而不是 Unreal 的 C++。自由实验室在开发 ElemenTerra 时的困境是，我们更喜欢 Unreal 的渲染器和编辑器，而不是 Unity 的 C#脚本。我们的解决方案是集成开源的 [Mono-UE 插件](https://mono-ue.github.io/)，让我们为虚幻游戏编写 C#代码！

C#与 C++是一个复杂的话题，你可以在别处读到。在 Freeform Labs，我们对这两种语言都很精通，但更喜欢 C#的开发速度更快:

*   C#编译起来要快得多；我们的 C#解决方案在 3-5 秒内构建，而 C++代码库平均需要 100 秒。

*   因为这种语言构建得更快，所以 C # IDEs 更快，响应更快。

*   C#错误消息是具体的和连贯的；C++错误是冗长的，并且经常掩盖了线索。

*   C#很好地捕捉了它的异常，而大多数 C++错误会导致 Unreal 崩溃。

总的来说，能够在 C#中工作是一个巨大的生产力提升，证明了跳过几个圈来集成插件是正确的。

# Mono-UE 的基础知识

让我们从核心前提开始:Mono-UE 通过将 C#类与连接 Blueprints 和 C++的相同绑定挂钩来工作。

那些使用过 Unreal 的人将会熟悉制作 C++类的“蓝图”子类。Blueprint 类是用可视化编辑器创作的，该编辑器可以添加新的组件、脚本节点等等。在 C++中，程序员使用像 UCLASS、UPROPERTY 和 UFUNCTION 这样的宏来选择将哪些定义暴露给蓝图。然后编译器创建“绑定”，即围绕 C++代码的蓝图可访问的包装器，允许两个生态系统进行通信。

Mono-UE 允许我们使用相同的绑定来创建 C#类，而不是蓝图。蓝图可扩展的类可以从 C#中继承，C#可以调用和覆盖蓝图可访问的子函数。仅这一点就包含了大量的功能，让我们可以用 C#代码编写复杂的游戏逻辑。更何况 C#类本身也可以被蓝图子扩展！

下面是一个简单的 Mono-UE C#类的例子，注释块突出了 C#和 C++之间的连接点:

```
 /// A new C# class that extends Unreal’s “AActor,” which is defined in C++
public class MyCSharpActor : Actor
{  
    /// Overridden class constructors: 
    protected MyCSharpActor(ObjectInitializer initializer) : base(initializer) {}  
    protected MyCSharpActor(IntPtr inNativeObject) : base(inNativeObject) {}   

    /// C# override of the ReceiveTick function, which is defined in C++ 
    protected override void ReceiveTick(float deltaSeconds)  
    {     
        base.ReceiveTick(deltaSeconds)
        // C# can invoke the UFunctions SetActorLocation and GetActorLocation,
        //  just as we would from a Blueprint or C++:
        this.SetActorLocation(this.GetActorLocation() + new Vector3(1f, 0f, 0f));
    }

    /// Custom function that Blueprints can call because of the UFunction and
    ///  BlueprintCallable attributes 
    [UFunction, BlueprintCallable, Category("My C# Actor")]
    protected void MyBlueprintCallableFunction(int input)  
    {
        // ... 
    }

    /// Custom function with no UFunction attributes, callable only by other C# code
    public void MyCSharpOnlyFunction(int input)  
    {       
        // ... 
    }       
} 

```

Mono-UE 是由 Mikayla Hutchinson 和一群可爱的志愿者维护的开源软件，我们对此深表感谢。这里的详细介绍了[的设置过程。由于插件仍在开发中，一些功能缺失或未完成，请注意，在您学习使用它时，您将需要选择一些特殊的变通方法。](https://github.com/mono-ue/UnrealEngine/tree/monoue/Engine/Plugins/MonoUE)

# 我们的 Mono-UE 工作流程

现在我们已经建立了基本要素，让我们来谈谈使用这个插件的日常工作吧！我在 Mono-UE 上花了一年时间，这种情况可能会一直持续下去，但为了简洁起见，这里只列出一些用法说明:

*   C#类可以访问 Blueprints 的几乎所有功能。这包含了我们需要的大部分内容，但有时我们会发现 C++函数没有绑定。这也是 Blueprint scripters 的常见问题，在这两种情况下，解决方案都是围绕所需的函数编写 Blueprint-exposed C++包装器。

*   我们的大多数对象是由一个 C++库、一个 C#子类和最后一个具体蓝图组成的“三层蛋糕”。一般游戏性逻辑用 C#，引擎增强用 C++，美工在蓝图里设计视听。因此，我们将大部分时间花在编写速度最快的语言上，只在必要时才使用 C++语言，并将我们的外观和感觉功能保持为艺术家可以编辑的格式。

[![3-layer-cake-freeform-labs](img/5312d95b2e65eea1907c030e8f650a64.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UWNh9Q5D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5uvwse63f23owoodqb42.png)

*   每隔一段时间，我们必须修改 Mono-UE 源。当我们启动 ElemenTerra 时，打包&发布一个独立的可执行文件是不可用的，但是我们修复了这个错误并发布了游戏！我们的修改现已被接受为拉取请求。

*   “热重新加载”在 ElemenTerra 开发期间不起作用，所以我们需要在更改 C#代码后关闭并重新打开编辑器。这很烦人，但我们仍然发现这比重新编译我们的 C++要快得多。在撰写本文时，有一个尚未集成的 [pull 请求要求部分修复](https://github.com/mono-ue/UnrealEngine/pull/201)。

有了这些方法，我们享受到了 C#的所有好处:更快的构建时间，更灵敏的 ide，更好的错误消息，以及不会导致引擎崩溃的异常处理！

# 性能怎么样？

*ElemenTerra* 是一款 VR 游戏，要求我们达到 90+ fps。C++的主要吸引力之一是它是一种运行速度极快的低级语言；使用 C#会让我们成为负担吗？答案并不是非黑即白的

[![ue4 Performance Analysis](img/ca8d2e12ee2c6994e00e39425aa494ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zBNt58wt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zftjbfglgh8jyvyh0dwq.png)

从事实开始，我们已经观察到 Unreal C++无条件地比 Mono-UE C#运行得更快。事情变得不明朗的地方是，有时 C++明显更快，但通常这种差异是微不足道的。对于一般算法和数学函数，我们看到的差别很小，但是当我们做大量的 thunks 时，C#会慢一些。“ [Thunk](https://en.wikipedia.org/wiki/Thunk#Interoperability) ”是一个愚蠢的软件术语，在这个上下文中指的是从 C#调用一个虚幻的 C++函数。运行时环境之间的这种跳跃本身就很昂贵，如果全部执行，会增加性能成本。

这是一个需要尊重的因素，但绝不是 Mono-UE 的交易破坏者。以下是我们使用的最佳实践:

*   **永远不要在评测前进行优化！只有几个衍生实例或者没有从循环中调用 thunks 的 C#对象通常不会出现在分析器上:不需要任何操作。这适用于我们大部分的 C#代码库。**

*   避免多余的 thunks。我们可以对 GetActorLocation()执行一次 thunk，并将其输出保存在一个变量中，而不是执行三次。我们可以编写一个新的 DestroyMultipleActors() C++函数并调用它一次，而不是对数组中的每个对象调用 DestroyActor()。

*   **将昂贵的 C#代码移植到 C++。**当评测器*显示*我们的一段 C#代码很贵时，我们把它移到了 C++。通常，这是用可以想象的最简单的方法完成的:复制整个脚本，粘贴到。h 和。cpp 文件，做一些查找替换，并修复错误，直到它工作。我们惊讶于这种方法如此之少的并发症；通常 C++在第一次运行时就能工作！我们在 Freeform Labs 的严格封装政策使这种方法变得非常容易，因为每个脚本都几乎没有外部依赖性。

有了这些实践，我们享受到了 C#的生产力提升，并且仍然达到了 90fps。

# Mono-UE 适合我吗？

这篇文章是关于为什么 Mono-UE 是我们团队的正确选择，我们没有遗憾。如果我激起了你的兴趣，我会在结尾给那些有兴趣尝试 Mono-UE 的人一些建议。

*   如果你是一个有 C#和 C++经验的团队，并且想写更多的前者，Mono-UE 可能适合你！请注意，设置它需要前期成本，并且您可能会遇到需要在插件源代码中修复的错误。

*   如果你是一个技术爱好者，正在寻找新的东西，看看 Mono-UE 吧！这是一个了解 Mono 和 UE4 如何工作的好机会，作为一个开源项目，它一直在寻找新的贡献者。

*   如果你是一个想尝试虚幻引擎的 Unity 用户，并且只知道 C#，只要你能忍受使用仍在开发中的软件所带来的障碍，你就是幸运的。第一次设置是棘手的，你会遇到需要社区帮助解决的错误，但是回报是肯定存在的。

*   如果你是编程新手，并且认为 C#比 C++更容易学习，我建议你选择 Blueprint Scripting 或 Unity。这两个工具已经为初次学习者提供了丰富的在线资源，而 Mono-UE 社区才刚刚起步。

*   如果你想安装到游戏机或手机上，Mono-UE 目前还不支持这些平台，但将来可能会支持。

*   如果您想让您的公司安装 Mono-UE，但不知道从哪里开始，也许我们可以帮助您！这里可以联系[。](http://www.freeformlabs.xyz/contact)

# 编程语言与安心

作为游戏开发者，选择正确的工具对我们的工艺质量和生活质量都很重要。就个人而言，我发现 Mono-UE 使用起来非常有趣:C#是一种我可以进入流畅状态的语言，不会被编译时间或迟钝的错误打断有助于我保持专注。无论您决定使用什么，我希望这篇文章能够启发您反思自己的开发工作流！

如果你有兴趣自己尝试 Mono-UE，这个[登陆页面](https://mono-ue.github.io/)会指引你找到源代码和一些社区资源。如果您需要提示或者[想了解更多信息](http://www.freeformlabs.xyz/contact)，也可以直接联系我们。下次见！

* * *

***Freeform Labs，Inc.** 是一个游戏和软件开发团队，致力于提供最高质量的数字工艺，并以通过尖端的体验设计激发学习和创造力为使命。该团队位于洛杉矶市中心，在全球范围内维护着一个值得信赖的合作伙伴网络。凭借在 VR、AR、游戏 AI 等领域的专业经验，Freeform Labs 提供了一系列服务，包括咨询、雇佣工作软件开发和现场“发货”协助。该公司的作品包括微软、迪士尼和 Starbreeze 的作品，以及屡获殊荣的原创内容。*

* * *
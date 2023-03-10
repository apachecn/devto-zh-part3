# 我们与 Xamarin 合作了近三年，这是我们学到的东西

> 原文：<https://dev.to/ljosmyndun/we-worked-with-xamarin-for-almost-three-years-this-is-what-we-learned-34ph>

在口袋里，我们过去主要关注本地开发。然而，我们认识到了使用跨平台框架的好处:单一的代码库可以更好地管理特性的对等性和维护，同时降低客户成本。

在被微软收购后的短时间内，Xamarin 成为开源软件，对社区开发者免费。这些(在我们看来)积极的变化促使我们决定将 Xamarin 作为客户项目的跨平台解决方案进行研究。

在与 Xamarin 合作了近三年后，我们希望与我们一直从事的不同类型的 Xamarin 项目分享我们的经验和技术。

# Xamarin 形式:一切共享

如果我们在谈论 Xamarin，我们需要澄清有多种方法使用 Xamarin。您可以使用 Xamarin Native，这是更传统的方法。使用这种方法，我们可以与其他平台共享应用程序的业务逻辑，但是仍然需要为每个平台单独创建 UI 层。第二种方法称为 Xamarin Forms:它建立在传统方法的基础上，但是为常用的 UI 组件添加了一些抽象。这允许我们为几个平台编写一次 UI。

我们很快就接受了 Xamarin Forms 的想法，因为它意味着共享应用程序的业务逻辑和 UI。虽然共享的业务逻辑已经是一个很大的优势，但我们过去常常花费大量时间为每个平台创建单独的视图。只需这样做一次的想法真的很吸引人，所以我们决定给 Xamarin Forms 一个机会。

最初，这似乎是一个好方法。由于我们确实共享业务逻辑和视图，不再有典型的 Android 开发人员和 iOS 开发人员:我们都在编写相同的代码，而不是在单独的筒仓中工作，这导致了非常紧密的合作。

然而，随着项目的推进，我们遇到了一些不可预见的限制。首先，在口袋里，我们为提供像素完美的设计而自豪。我们希望我们的项目看起来完美，执行起来没有任何问题。使用 Xamarin Forms，您可以快速创建一个基本外观的 UI，但是一旦您想要开始定制，您就需要转向定制呈现器。虽然我们的设计师做出了妥协，使视图不那么复杂，但我们仍然需要为我们的许多视图定制渲染器。这种工作方式大大降低了我们的速度，我们又一次为每个平台创建了不同的视图。

另一个问题是性能:虽然在 iOS 上可以接受，但我们注意到我们的 Android 版本感觉很慢，尤其是在启动阶段。此外，XAML 页面的渲染也成了一个瓶颈，这导致了导航过程中的中断。虽然我们做了一些改变来提高性能，但最终我们仍然对应用程序的流畅度不满意。

我认为 Xamarin 表单的主要问题是，它不能与真正的本地应用程序相提并论。在开始使用 Xamarin 之前，我们都是本地开发人员，在我们过去可以定制几乎任何东西并拥有本地性能的地方，Xamarin 表单确实限制了我们的开发和性能。

我想指出的是，这是我们在框架方面的经验。我确信许多其他团队会发现 Xamarin 为他们的应用程序形成了完美的框架，他们可能是对的。因为在口袋里，Xamarin Forms 并不是完美的，因为我们觉得受到了它的一些限制的阻碍。

# Xamarin Native:更适合

我们的热情被与表单的斗争所冲淡，我们决定用 Xamarin 本地方法创建我们的下一个 Xamarin 项目。因为我们的团队中仍然有 Xamarin 经验丰富的 Android 和 iOS 开发人员，所以切换到这种方法没有问题。

很快就清楚了，这种工作方式更适合我们的需要。虽然业务逻辑仍然是共享的，但我们现在有能力以更本地的方式定制 UI，就像我们习惯的那样。性能也(几乎)与本机平台相同，而且我们仍然可以共享大部分代码库，因为有许多第三方库。这些有助于共享一些特定于平台的逻辑组件，如数据库或查找设备信息。

对我们来说，另一个好处是，我们可以利用我们团队中已经存在的本地经验。由于我们的视图仍然是本地处理的(尽管使用了不同的语言)，我们可以与其他本地开发人员讨论特性以及如何实现它们。

为了开发 Xamarin 原生应用程序，我们确实需要改变我们处理特性的方式。虽然我们可以将任何开发人员分配到 Xamarin Forms 故事中，但是我们现在必须在其他平台插入视图之前公开 viewmodel 的公共属性。目前，我们让一个平台领导一个特性，首先创建视图和视图模型，同时让第二个平台在第一个平台完成编码或至少完成视图模型后插入。我们发现这种方法在开发的同时导致的冲突最少。

交流是 Xamarin 项目的关键:视图模型的变化可能会对两个平台都产生影响。影响本地实现的变更需要记录在故事中，我们也明确地将它传达给相关的开发人员。通过紧密合作，我们已经成为一个更好的团队，更好的开发人员。

# 我们在 MVVM 的做法:MvvmCross

目前，我们使用 MvvmCross 作为我们的框架，在平台之间共享业务、通信和表示逻辑。这个框架已经存在了一段时间，并且多年来已经明确证明了它的价值。它也得到很好的维护，定期发布新版本，并总是扩展文档。它从头开始构建，具有可扩展性和适应性。这使我们能够在基础实现上快速构建应用程序——同时允许我们在调整 UI 和表示方面有充分的灵活性。

当然，还有其他有趣的框架。我们继续对其他框架保持开放的态度，因为我们喜欢反应式编程，我们可能也会尝试一下 ReactiveUI。

# 本地绑定:一种替代方法

我们的一个 Xamarin 项目涉及另一种 Xamarin 开发方法:使用本机绑定。

所有的 UI 组件和 UI 逻辑都构建在一个本地项目中，该项目被编译为本机库。然后，这些库在一个 Xamarin 绑定项目中被使用，在那里我们可以将 UI 组件连接到 Xamarin 环境。

这使得我们能够快速提升没有 Xamarin 经验的开发人员的团队。对他们来说，这只是另一个本地项目，他们可以在自己的能力范围内工作。然后由 Xamarin 开发人员将库与 Xamarin 项目连接起来。

这种方法也给了我们很大的自由:我们可以在库中任意使用所有的本地组件和库，并且您不需要担心将这些库绑定到 Xamarin，因为它们没有向 Xamarin 组件公开。

然而，这种方法并非没有缺陷。本机组件和 Xamarin 组件之间的连接是通过在本机组件中创建的抽象类来完成的。这些是后来在 Xamarin 组件中实现的(Android 的视图模型，iOS 的控制器)。这意味着我们必须创建单独的项目，因此代码重用是不可行的。

另一个障碍是结合 Xamarin 项目调试 UI 组件。我们创建了一个模拟项目来快速测试所有可用的本地组件和视图。然而，当与 Xamarin 组件结合使用时，我们有时会遇到在模拟项目中无法重现的问题。调试这些问题被证明是非常困难的，因为在 Xamarin Bindings 项目中不可能出现断点，因此我们只能使用日志。

# 总结

在口袋里，我们研究了 Xamarin 的不同变体。虽然我们认识到 Xamarin 表单的好处，但它对我们来说是一条崎岖不平的道路，因为它不太适合，这是由于可定制性和性能问题的限制。Xamarin 原生方法更合我们的口味，尤其是与 MvvmCross 这样的框架结合使用时。它允许我们共享我们的代码库，同时仍然能够交付像素级的完美设计，具有接近原生的性能。
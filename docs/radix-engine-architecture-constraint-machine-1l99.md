# 基数引擎架构约束机

> 原文：<https://dev.to/radixdlt/radix-engine-architecture-constraint-machine-1l99>

# 简介

本文将介绍约束机制背后的设计逻辑，约束机制是 Radix 引擎的两个中间层之一。

约束机器是一个概念，它采用比特币的 UTXO 模型，并将其扩展用于泛型编程。该机器针对新原子和历史数据运行约束验证。

另一个中间层是[原子模型](https://docs.radixdlt.com/alpha/learn/architecture/atom-structure)，它采用了核心通用编程语言，并为开发正确的状态机提供了更简单的抽象。

> 提示:您可以在这篇文章中阅读更多关于原子模型结构的内容。

[![alt text](img/1fc592c783d4443663c2e5884b621d54.png "Radix Engine Layers")](https://res.cloudinary.com/practicaldev/image/fetch/s--KIM4hgun--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%252F-LMPbV3hGbTEzGtYlH-m%252F-Lexnp-WzMRymxJsoUM6%252F-Lexo8Dosy_DqedCT9Ss%252F20.png%3Falt%3Dmedia%26token%3Df06dde8e-b18c-4af9-aaab-6dd273dcbe80)

# 约束机器 vs .计算机器

在我们深入研究之前，让我们解释一下为什么我们正在远离让以太坊和 EOS 如此流行的区块链虚拟机(VM)。

在高层次上，传统的计算机器计算状态。如此通用使得它非常强大，但也带来了一些问题:

-很难证明正确性
-很难陈述碎片

另一方面，约束机器验证新的状态。这类似于比特币验证新 UTXOs 的方式。虽然与传统的计算机器相比，这在能力上是一个急剧的下降，但是我们得到了以下好的特性:

-证明正确性要容易得多
-状态分片几乎是内置的

在创建分布式分类帐时，我们认为这是正确的权衡，因为:

-“计算”不是必需的，因为用户总是知道他们想要达到什么状态(我们不需要分类账来计算游戏物理)
-可证明的正确性比一般编程重要得多
-分布式吞吐量不是来自计算速度，而是来自并行状态分片

# 状态机

如以太坊的智能合约所流行的，在分类帐之上进行一般开发的主要用例是实现正确的状态机。为此，Atom 模型被设计成在状态机管理和约束机之间提供一个简单的接口。

我们现在将介绍一些状态机的基础知识，因为对于 Radix 开发人员来说，了解这些概念非常重要。

> 状态机是一种设备，根据其先前的条件和其输入的当前值，它可以处于一组稳定条件之一。

# 状态机定义

状态机的定义定义了它的行为和生命周期。例如:

[![alt text](img/03cc3c2f148e78ca16ecf009d581d260.png "Constraint Machine")](https://res.cloudinary.com/practicaldev/image/fetch/s--lk7gv2dd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%252F-LMPbV3hGbTEzGtYlH-m%252F-Lexnp-WzMRymxJsoUM6%252F-LexoCLQMubVWnA0zzXl%252F21.png%3Falt%3Dmedia%26token%3Ddb055d09-6318-490d-ab09-a05ad3e790c7)

# 状态机实例

状态机实例是具有特定状态的状态机的实际实例。一个已定义的状态机可能有多个实例。例如，下面是上面定义的状态机的两个实例:

[![alt text](img/75da8d6d2112c277ffded497febd3621.png "Constraint Machine")](https://res.cloudinary.com/practicaldev/image/fetch/s--5rhGfwEn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%252F-LMPbV3hGbTEzGtYlH-m%252F-Lexnp-WzMRymxJsoUM6%252F-LexoFJWzMEg1LJBW7b_%252F22.png%3Falt%3Dmedia%26token%3D3a50aa00-8e39-4201-82ec-543a31760555)

# 量变与质变

作为状态机设计者，理解扩展状态机或状态机的定性和定量方面的概念也很重要。

# 数量跃迁

这些是到相同状态的转换，其中状态的“扩展状态变量”被更新。例如:

[![alt text](img/288982afadbbedd2c37c6bea27fff2c9.png "Constraint Machine")](https://res.cloudinary.com/practicaldev/image/fetch/s--qKUl2cuP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%252F-LMPbV3hGbTEzGtYlH-m%252F-Lexnp-WzMRymxJsoUM6%252F-LexoHuL-BW0X22MrVsZ%252F23.png%3Falt%3Dmedia%26token%3D4f242ce1-be6c-4a5a-a6f4-5f4c69ade60f)

# 质变

这些都是向不同状态的转变。例如:

[![alt text](img/d80c500210c1c7a88e2477e1754af34c.png "Constraint Machine")](https://res.cloudinary.com/practicaldev/image/fetch/s--JGowG1jV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%252F-LMPbV3hGbTEzGtYlH-m%252F-Lexnp-WzMRymxJsoUM6%252F-LexoK4avrJLF3AqsR4e%252F24.png%3Falt%3Dmedia%26token%3D0a44a999-0920-4d32-9f5f-81a9963981d9)

# 粒子

粒子可以被定义为“全局状态的子状态”，它是在约束机器上开发时的基本块。当谈到粒子时，有两个部分:一个粒子类型和一个粒子实例。两者之间的区别类似于状态机定义和状态机实例之间的区别。也就是说，ParticleType 定义子状态，ParticleInstance 是该子状态的一个实例。

> 提示:你可以在本文中阅读更多关于粒子和粒子类型的内容。

# 粒子自旋

粒子自旋是粒子如何转变成其他粒子的关键。
让我们举个例子，从“我口袋里有 5 美元”的状态过渡到“你口袋里有 5 美元”。

[![alt text](img/6cda139f1946e6e8a2138ff8a3d1f938.png "Constraint Machine")](https://res.cloudinary.com/practicaldev/image/fetch/s--ibT96xwx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%252F-LMPbV3hGbTEzGtYlH-m%252F-Lexnp-WzMRymxJsoUM6%252F-LexoRM8t-bDz2M61KPm%252F29.png%3Falt%3Dmedia%26token%3Dc98813e6-4299-4ce7-b7bd-02ff7712d888)

# 实际粒子跃迁是如何工作的？

假设一个粒子代表一个“子态”，那么粒子的自旋就代表它是否被“启用”。在“自旋语言”中，UP(启用)或 DOWN(未启用)。
这意味着一个粒子的跃迁是由当前粒子的自旋下降和下一个粒子的自旋上升来表示的。
回到我们的示例,“我口袋里有 5 美元”状态变为禁用，而“你口袋里有 5 美元”状态变为启用:

[![alt text](img/52908d9376b03d4596fe27f6af81b12c.png "Constraint Machine")](https://res.cloudinary.com/practicaldev/image/fetch/s--eCSpJTaT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%252F-LMPbV3hGbTEzGtYlH-m%252F-Lexnp-WzMRymxJsoUM6%252F-LexoUtN_2xn50LsaSZP%252F30.png%3Falt%3Dmedia%26token%3D895499f0-4a16-422f-a537-896bc1055001)

鉴于此，低级粒子状态机转换管理开始应用逻辑，该逻辑基于对于要加速旋转的粒子，什么粒子必须被减速旋转。

# 粒子和状态机

由于粒子代表可以打开和关闭的部分状态，状态机是管理粒子的完美构造。有两种主要的状态机实例构造，它们都以不同的方式使用粒子来表示状态:

-索引状态机
-转换状态机

> 注意:某些状态机转换可能依赖于其他状态机实例的状态。目前状态依赖仅在可索引状态机实例上受支持。

# 索引状态机实例

在带索引的状态机实例中，每个粒子都有一个与之关联的索引，该索引表示它是哪个状态机实例的一部分。

[![alt text](img/b4e578a3f83d5a96de0528346437bd20.png "Constraint Machine")](https://res.cloudinary.com/practicaldev/image/fetch/s--VlU2UTqL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%252F-LMPbV3hGbTEzGtYlH-m%252F-Lexnp-WzMRymxJsoUM6%252F-LexoXho2e09DMKauQKi%252F25.png%3Falt%3Dmedia%26token%3D058f7b0d-e60d-4559-bbcb-0125bb0e1daf)

> 用例:主要用于状态机定义的状态都位于同一个碎片中的情况(它们不会在空间中移动)。例如，令牌定义。

# 转移状态机实例

在转换状态机实例中，每个粒子都定义了需要转换的当前粒子。

[![alt text](img/faef568bb8029a27226b9fbf7af1749c.png "Constraint Machine")](https://res.cloudinary.com/practicaldev/image/fetch/s--OrhQVGyK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%252F-LMPbV3hGbTEzGtYlH-m%252F-Lexnp-WzMRymxJsoUM6%252F-LexoZlvF1XAUdIAykkB%252F26.png%3Falt%3Dmedia%26token%3D63a06ca2-4457-41f0-a017-ab04648281de)

> 用例:用于状态机实例可以跨分片运行的情况。例如，TokenInstances。

# 索引状态机依赖关系

索引状态机实例转换可以依赖于另一个索引状态机实例。

[![alt text](img/d5c536dfc4da7aedc0df5feb75775daf.png "Constraint Machine")](https://res.cloudinary.com/practicaldev/image/fetch/s--_1B4npuj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%252F-LMPbV3hGbTEzGtYlH-m%252F-Lexnp-WzMRymxJsoUM6%252F-LexoaoqBn-saEByHgcT%252F27.png%3Falt%3Dmedia%26token%3D301a548f-093b-4215-9024-3a8c17e53ec6)

> 用例:用于同一个碎片中的多个状态机定义相互依赖的情况。例如，铸造权限。

# 自旋状态机与粒子状态机

不要混淆自旋状态机和更高级的粒子状态机，因为它们是不同的抽象。自旋状态机描述子状态的生命周期，而粒子状态机描述通用应用程序对象的生命周期。

按照我们的“5 美元”示例，下图显示了粒子状态机和自旋状态机之间的不同抽象级别:

[![alt text](img/ad794d2d33fc4b2d95dc60ba2fa6826f.png "Logo Title Text 1")](https://res.cloudinary.com/practicaldev/image/fetch/s--Zg_80MPj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%252F-LMPbV3hGbTEzGtYlH-m%252F-Lexnp-WzMRymxJsoUM6%252F-Lexodg2SY7WBP3Kfr64%252F32.png%3Falt%3Dmedia%26token%3D2c37c316-1c90-454d-a901-3241a5d77cfe)

# 原子模型层

约束机器不知道诸如 ParticleTypes 和状态机之类的高级抽象。相反，它的语言包括:原子、粒子、自旋、指数和碎片。

为了填补高级脚本和约束机器之间的空白，原子模型层将高级脚本转换成约束机器可以理解的两种结构:

1.约束程序
2。ParticleTypeDefs

[![alt text](img/c632602a5cef9af354ccc4f4e7f76fe3.png "Constraint Machine")](https://res.cloudinary.com/practicaldev/image/fetch/s--ER-xLFYC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%252F-LMPbV3hGbTEzGtYlH-m%252F-Lexnp-WzMRymxJsoUM6%252F-LexohSNRnr-97cC-Alg%252F28.png%3Falt%3Dmedia%26token%3Ddcac7580-95ab-4bc8-9394-9834e1c7d108)

# 约束程序

约束过程依赖于对约束机器的自旋状态机验证，因为约束机器不知道粒子状态机。两者之间的转换是原子模型层的作用之一。
约束程序看起来像:

> /**
> 
> *   历史粒子数据的只读接口 */公共接口 ParticleStore { /* *
>     *   检索具有给定索引的所有粒子
>     *   TODO:如何管理内存？这个应该分页还是变成可观察的？
>     *   [@param](https://dev.to/param) 索引要查找的索引
>     *   @返回 index *下的粒子列表/ public List getSpuns(EUID 指数)；} /* *
> *   根据历史分类帐数据验证新的 particleGroup。
> *   在调用 validate 时，可以假设 particleGroup 已经通过了约束机器不变量。
> *   注意:访问存储区 */公共接口约束过程{ /* *时不是线程安全的
>     *   验证粒子组
>     *   [@ param](https://dev.to/param)particle group particle group 要验证
>     *   [@ param](https://dev.to/param)atom 元数据来自 atom 的元数据
>     *   [@param](https://dev.to/param) 储存粒子的历史总账储存
>     *   @throws ValidationException 当 particleGroup 没有通过所有约束要求时*/public void validate(particle group particle group，AtomMetaData atomMetaData，ParticleStore store)抛出 validation exception；}

# 粒子类型定义

为了检索粒子用于将来的验证，它们也必须以可预测的方式存储。这是由 ParticleTypeDef 给出的，particle typedef 是约束机器可以理解的另一个构造，也是原子模型层必须从约束加密中产生的一个构造。这些定义具有以下结构:

> 接口 ParticleWrite {
> /**
> 
> *   返回存储该粒子的索引
> *   TODO:这应该总是有目的地编码在其中，也许有一种方法来结合索引和目的地？
> *   @返回该粒子应存储在其中的 EUID 索引集，位于 */ Set getIndices()下；/* *
> *   返回应该存储粒子的目的地(即碎片+密钥)
> *   @返回 EUID 目标的集合，该粒子应存储在该集合中的*/ Set getDestinations()下；}接口 particle typedef { Class type()；SpinStateMachine 生命周期()；particle write mapToWrite(T T)；}

# 系统调用粒子类型定义

系统调用是使用粒子定义状态机的创建和管理的机制。Atom 模型层根据发出的所有系统调用动态创建 ParticleTypeDef。也就是说，它可以判断出哪些索引和目的地是脚本单独需要的。

[![alt text](img/525973ad8e01c2b173072c5deff84135.png "Constraint Machine")](https://res.cloudinary.com/practicaldev/image/fetch/s--AcxndZrV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%252F-LMPbV3hGbTEzGtYlH-m%252F-Lexnp-WzMRymxJsoUM6%252F-LexolXTcr8qtEHiroM7%252F33.png%3Falt%3Dmedia%26token%3D4a88c72f-9cfd-4d15-8c40-b416b679800c)

1.约束脚本定义了某个 ParticleType
2。Constraint Scrypt 根据它需要的某些可索引数据结构定义约束
3。一个可索引的类型可能在另一个脚本中被定义，以供其他脚本使用
4。一个可分片的类型也可能在另一个脚本中定义，以供其他脚本使用
5。给定 2 中的约束使用的可索引类型，可以创建从实例到其目的地和索引的映射

# 约束机器

在 Atom 模型层将所有的 scrypts 转换成 ConstraintProcedures 和 ParticleTypeDefs 之后，它将这两个结构的所有实例传递给 Radix 约束机器。

一旦基数约束机器收集了这些实例，它就可以验证并返回任何给定只读原子存储的新原子的写指令。

注意，这种情况下的约束机器不更新任何状态，它只是返回原子写应该是什么，而不更新原子存储本身。因此，它是一个纯粹的功能性组件，这意味着测试要容易得多，因为不需要担心状态维护。

概括来说，这看起来像是:

[![alt text](img/64c285593cd0266d2555c046e8043fc9.png "Constraint Machine")](https://res.cloudinary.com/practicaldev/image/fetch/s--DBYZEiL2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%252F-LMPbV3hGbTEzGtYlH-m%252F-Lexnp-WzMRymxJsoUM6%252F-Lexoo2etT7Oyeo3BMvZ%252F34.png%3Falt%3Dmedia%26token%3De15c70c5-6910-4cc4-b9c7-08485e90f998)

# 粒子群和从属状态的变化

假设在一个原子中可能存在多个粒子状态转移，每个粒子状态转移可能依赖于另一个粒子状态转移(例如，在同一原子中创建令牌并为该令牌铸造令牌)，则需要一种机制来确定在约束过程的验证期间哪个状态是相关的。这是由原子中粒子群的排列定义的。验证按照以下规则进行:

-按顺序验证每个粒子组
-一旦粒子组验证成功，该粒子组的状态变化将被记录，并可作为“虚拟状态”用于后续粒子组验证(仅在内存中，不会保存到 AtomStore)

这意味着相互依赖的状态转换不应该进入同一个粒子群。

粒子组处理如何发生的示例如下:

[![alt text](img/0358ae5b3c122c29ffc09f1283760647.png "Constraint Machine")](https://res.cloudinary.com/practicaldev/image/fetch/s--pYxxzz7K--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%252F-LMPbV3hGbTEzGtYlH-m%252F-Lexnp-WzMRymxJsoUM6%252F-LexorLsWt56p-aI-piL%252F35.png%3Falt%3Dmedia%26token%3D19e467da-3d16-470c-9f81-8401048e3657)

用当前原子库验证第一个粒子组

[![alt text](img/fd5958bee2995808fccd20e1dc2968c3.png "Constraint Machine")](https://res.cloudinary.com/practicaldev/image/fetch/s--dwxt6xWb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%252F-LMPbV3hGbTEzGtYlH-m%252F-Lexnp-WzMRymxJsoUM6%252F-Lexp6e9Lkc1PBmjebIY%252F36.png%3Falt%3Dmedia%26token%3Dcb2f5382-b024-4a60-b33a-8ead95bcaf36)

假设第一个粒子组通过了验证，则第二个粒子组用第一个粒子组的当前原子存储+状态来验证

[![alt text](img/cc5df010959bf324dae6707715170285.png "Constraint Machine")](https://res.cloudinary.com/practicaldev/image/fetch/s--agMnw0u7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%252F-LMPbV3hGbTEzGtYlH-m%252F-Lexnp-WzMRymxJsoUM6%252F-LexoutGzdAl5Do1BlbU%252F37.png%3Falt%3Dmedia%26token%3D9e7b9d2e-2da8-4cea-9644-82c458e19ac8)

相同的模式继续，直到所有粒子组都被成功验证。一旦失败，机器将提前退出并抛出一个 ValidationException。

# 约束机器验证

除了约束程序验证，还有其他依赖于机器本身的验证。这些都是在约束程序之前验证的。

例如:最大原子大小、时间戳元数据要求或最大碎片数量。

# 索引和分片

一旦所有的验证都通过了约束，机器就会返回一个 AtomWrite，描述原子应该如何被索引和存储。约束机器本身从不存储状态，这样它可以被视为一个纯粹的功能组件。

原子的索引和分片是由包含在原子中的粒子决定的。具体来说，粒子的 particleType 与实例一起映射到一组索引和碎片，这些索引和碎片由原子模型层根据定义每个 ParticleType 所表示的状态机的约束脚本来确定。这由 Atom 模型层提供的 ParticleTypeDef 来表示。

一旦所有的 ParticleWrites 都被解析，这些将被合并以产生一个 AtomWrite，约束机器能够将该 atom write 传递回去以通知 [Tempo](https://docs.radixdlt.com/alpha/learn/whitepapers/tempo) 原子应该如何被存储。

如果找到不匹配 ParticleTypeDef 的 ParticleType，将引发约束机器验证异常。

[![alt text](img/5193fb9eff6ba177d84736d997aceed8.png "Constraint Machine")](https://res.cloudinary.com/practicaldev/image/fetch/s--My6T17Nl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blobscdn.gitbook.com/v0/b/gitbook-28427.appspot.com/o/assets%252F-LMPbV3hGbTEzGtYlH-m%252F-Lexnp-WzMRymxJsoUM6%252F-LexpEYVRI7mEgKfIW8p%252F38.png%3Falt%3Dmedia%26token%3Dc4888546-ff53-42ba-a18d-168374cbe8b7)

[电报](https://t.me/radix_dlt)普通聊天
[不和](https://discord.gg/7Q7HSZZ)开发者聊天
[Reddit](https://reddit.com/r/radix) 普通讨论
[论坛](https://forum.radixdlt.com/)技术讨论
[推特](https://twitter.com/radixdlt)发布公告
[电子邮件](https://radixdlt.typeform.com/to/nyKvMV)简讯每周更新
邮件[hello@radixdlt.com](//hello@radixdlt.com)普通查询
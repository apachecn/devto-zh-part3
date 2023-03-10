# 揭开规范化和反规范化的神秘面纱

> 原文：<https://dev.to/gmlion/demystifying-normalization-and-denormalization-2cph>

<figure>[![](img/5242f2f02d6b7efcb0b7789bf123f983.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--56QEaI2k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/0%2ALqc5cB3LERfn7vb-) 

<figcaption>照片由[缪策勒](https://unsplash.com/@samuelzeller?utm_source=medium&utm_medium=referral)上[下](https://unsplash.com?utm_source=medium&utm_medium=referral)</figcaption>

</figure>

在应用程序的设计过程中，首先要定义的元素之一是数据模型。数据模型是数据表示:它必须是正确的，并且以尽可能简单的方式设计。要考虑的第一个方面是需要保存什么数据:尽管这个问题看起来很琐碎，但它隐含着不同的推理，而这些推理一点也不琐碎。其中一个是数据归一化的程度。

在关系数据库的上下文中，有几种标准的规范化形式，您可以选择将它们应用于您的数据模型。通常你会在应用一个范式时努力保持连贯性，但通常情况下可能需要不同的方法。你可以找到几篇讨论这个主题的文章，但是大多数都太详尽或者太学术性，不能作为“快速参考”，所以我认为对这个主题采取一种更简洁的方法可能是有用的。

每个范式都假定前面的形式受到尊重。同样，这不一定适用于您的数据模型，但如果是这样，一定要有充分的理由。

### 第一范式

在这种范式中，实体的每个属性都由为该属性选择的域的唯一值组成。如果您在单个属性中保存一个值列表，就会违反它。

```
| Name\* | Address |
|----------------|------------------------------------------------|
| Telematica srl | Via Rigoletto 20, Milano; Via Manzoni 15, Roma |
| NextImage spa | Via Isonzo 3, La Spezia |

1 - This table does not satisfy the First Normal Form 
```

### 第二范式

当属性不是候选键时，它就不是质数。在这种范式中，没有依赖于候选键子集的非素数属性。简而言之，您的密钥可能具有某些属性，撇开这些属性，它们可能是其他属性的密钥。您可以使用不太具体的键将这些其他属性带到另一个表中。例如，在将年份和月份作为关键字的表中，季节属性违反了第二范式，因为它只依赖于月份。

```
| Year\*| Month\* | Season |
|------|----------|--------|
| 2019 | January | Winter |
| 2019 | August | Summer |
| 2018 | February | Winter |

2 - This table does not satisfy the Second Normal Form 
```

### 第三范式

在这种范式中，键和属性之间没有传递依赖关系。换句话说，你没有依赖于另一个属性的属性，因为这意味着它们依赖于键只是传递性的。例如，在一个以月份为关键字、以季节为属性的表中，如果气候属性仅仅依赖于季节，那么它就违反了这种范式。

```
| Month\* | Season | Climate |
|---------|--------|----------------|
| January | Winter | Freezing cold |
| August | Summer | Unbearably hot |

3 - This table does not satisfy the Third Normal Form 
```

### Boyce-Codd 范式

这种范式更难定义。在这种范式中，您删除了表中仍然存在的任何可能的函数冗余，因此只存在键和一些属性之间的函数依赖。通常，第三范式满足这一要求。然而，存在这样的情况，在相同的信息上，几个函数依赖叠加，或者存在未表达的缺失链接。

*对于第一种情况*，让我们想象一个表示混合类型偏好列表的表，要求任何人对每种类型只能有一个偏好。

```
| Name\* | Type\* | Preference |
|-------|---------|------------|
| Paolo | City | Rome |
| Luca | Color | Red |
| Lucia | Climate | Hot |

4 - This table does not satisfy Boyce-Codd Normal Form 
```

正如主键所暗示的，表达的依赖关系是 Name，Type -> Preference。

然而有一个未表达的依赖:值- >类型。事实上，偏好的值明确地决定了哪种类型是正确的。在这种情况下，转换到 Boyce-Codd 范式是不可能的:这将意味着删除 Type 属性(将其用作 Preference，Type 表中的一个属性)，但这种删除将破坏原始表的主键。如果你只是添加前面提到的第二个表，而没有改变第一个表，你不满足 BCNF，但是你满足另一个范式，初等键范式，所以这仍然是一个改进。

*对于第二种情况*，想象一个分配给一组人的指定设备表，以及他们计划在哪里使用它。

```
| Name\* | Destination\*| Equipment |
|---------|-------------|-----------|
| Paolo | Mountain | Ski suit |
| Lucia | Mountain | Sunscreen |
| Alberto | Sea | Sunscreen |
| Roberta | Sea | Gym suit |

5 - This table does not satisfy Boyce-Codd Normal Form 
```

在这种情况下我们可以想象一个未表达的依赖设备的目的地，也就是说只有设备和目的地的某些组合才有意义。如果我们把隐藏变量季节显式化，我们就能使信息正常化。

```
| Name\* | Destination\*| Season |
|---------|-------------|--------|
| Paolo | Mountain | Winter |
| Lucia | Mountain | Summer |
| Alberto | Sea | Summer |
| Roberta | Sea | Winter |

| Destination\*| Season\*| Equipment |
|-------------|--------|-----------|
| Sea | Winter | Gym suit |
| Sea | Summer | Sunscreen |
| Mountain | Winter | Ski suit |
| Mountain | Summer | Sunscreen |

6 - These tables satisfies Boyce-Codd Normal Form 
```

### 进一步范式

还存在其他正常形式。值得一提的是第六范式，它规定表只有两列，键值。

到目前为止所展示的规范化形式只是间接地对应用领域的建模有所说明，但是领域关键范式明确地处理了这个方面。它指出，数据之间任何可能的约束都应该由一个键-值关系来表示，或者是一个域约束。不应该有隐含的依赖，重点是良好的建模，而不是正确的表结构。

例如，假设您已经对知道一组人的年龄和状况的需求建模如下:

```
| Name\* | Condition | Age |
|---------|------------|-----|
| Paolo | Unemployed | 20 |
| Lucia | Employed | 36 |
| Clara | Unemployed | 20 |
| Stefano | Retired | 70 |

7 - Under our premises, this table does not satisfy Domain-Key Normal Form 
```

为了举例，我们假设年龄和可能的条件之间有确切的关系；特别是，70 岁前不工作被视为失业，而 70 岁后不工作则意味着退休。模型本身的重构是有序的:

```
| Name\* | Working | Age |
|---------|---------|-----|
| Paolo | No | 20 |
| Lucia | Yes | 36 |
| Clara | No | 20 |
| Stefano | No | 70 |

| Age segment\*| Non-working condition |
|-------------|-----------------------|
| \>= 70 | Retired |
| \< 70 | Unemployed |

8 - These tables satisfy Domain-Key Normal Form 
```

### 反规格化

根据定义，具有冗余的数据库是不正常的。冗余本身并不可取，但如果在特定环境下有用，则可以接受。可能的话，应该隔离。

如果冗余或源信息(在派生数据的情况下)是不可变的，则在大多数情况下可以避免冗余的常见问题:与冗余相关的典型问题是“插入异常”、“更新异常”和“删除异常”。如果数据是不可变的，只有第一个适用。

你有意识地放置一些冗余的过程被称为反规范化。这意味着有一个基本的、标准化的数据模型，冗余仅限于某些特定的情况。反规范化可以是性能优化的一种形式，或者最终由特定的技术考虑决定。反规格化的一个结果是写操作复杂度的增加被读操作复杂度的降低所补偿，这种折衷是有益的。

在选择非规范化之前，重要的是要记住数据库不仅是为了存储信息，而且是为了有效地找到它们。数据库提供了一些本机“工具”来优化性能，而无需借助数据模型中的冗余:

*   指数

*   视图(尤其是物化视图)

*   内部缓存

在某些情况下，反规范化可以被视为一种渴望数据缓存的形式。

有几种方法可以应用反规格化，但是我发现它们都可以归结为三种类型:

*   保存派生信息:您保存了一些精化的结果。你避免了再次做详细阐述。

*   保存连接前信息:在一个表中保存另一个链接表的属性。您不必进行一个或多个连接。

*   短路键:这可以看作是前一种情况的一个特殊实例，在这种情况下，您在一个表中保存一个外键，该外键来自与另一个表的传递关系。您不必进行一个或多个连接。

希望这篇文章能成为设计数据模型时有用的参考。一些例子有点做作，目的是保持简单。

### 参考文献

如前所述，穷举并不是主要目的，这里有两个参考资料，您可以在其中深化这些主题。

关于正常化:[https://en.wikipedia.org/wiki/Normalization](https://en.wikipedia.org/wiki/Normalization)

关于反规范化:[https://ruby garage . org/blog/database-反规范化-举例](https://rubygarage.org/blog/database-denormalization-with-examples)
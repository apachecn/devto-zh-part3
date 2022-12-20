# 在 Unity 中使用可脚本化对象实现策略设计模式

> 原文：<https://dev.to/eriksk/implementing-the-strategy-design-pattern-using-scriptable-objects-in-unity-292i>

# 在 Unity 中使用`ScriptableObject`实现*策略*设计模式

*策略*是《设计模式(GOF)
》一书中的设计模式，该模式的目的是以一种干净的方式在运行时改变行为(不是一堆 if 语句)。

你可以在这里阅读更多关于*战略*模式[的内容，因为这里不会涉及。](https://en.wikipedia.org/wiki/Strategy_pattern)

用 C#实现该模式非常简单，可以简单地解释如下:

## C#示例

假设您有一个可以执行不同类型攻击的`Player`类。这可能看起来像这样。

```
 class Player
    {
        void Attack(AttackType type)
        {
            if(type == AttackType.Melee)
            {
                // TODO: melee logic
            }
            else if(type == AttackType.Crossbow)
            {
                // TODO: crossbow logic
            }
        }
    } 

```

添加新的攻击意味着添加新的 if 语句。更不用说这种方法会很快变得很长，很难遵循。通常，攻击需要不同的参数。两者都需要一些伤害，但是弩需要射程甚至弹药，而近战不需要。当然，我们可以将所有这些参数传递给`Attack`方法。但是策略可以让这变得更干净。

相反，使用*策略*,我们可以使它变得更加清晰:

```
 // Player.cs
class Player
{
    void Attack(IAttack attack)
    {
        attack.Execute(this);
    }
}

// IAttack.cs
interface IAttack
{
    void Execute(Player player);
}

// MeleeAttack.cs
class MeleeAttack : IAttack
{
    public int Damage { get; set; }

    void Execute(Player player)
    {
        // TODO: Melee attack logic
    }
}

// CrossbowAttack.cs
class CrossbowAttack : IAttack
{
    public int Damage { get; set; }
    public float Range { get; set; }
    public int Arrows { get; set; }

    void Execute(Player player)
    {
        // TODO: Crossbow attack logic
    }
} 

```

好了，还有一点代码。我们将它们移到不同的文件中，并将每个攻击的功能分成不同的类，所有这些类都实现了`IAttack`。现在玩家只需要知道`IAttack`，并把自己作为参数传递给它正在使用的攻击。

现在添加新的攻击就像添加新的攻击类别一样简单。不需要改变`Player`类。

## 团结例子使用`ScriptableObject`

所以这一切都很美好。但是在 Unity 中这样做呢？我认为在 Unity 中只使用 C#会有好处，但是当使用 Unity 的`ScriptableObject`来实现每一个策略的时候才是真正的亮点。

[阅读更多关于 ScriptableObject 的信息](https://docs.unity3d.com/ScriptReference/ScriptableObject.html)

`ScriptableObject`的好处是它们可以作为资产来创建。这意味着你可以在 Unity 编辑器中将你的策略直接拖放到游戏对象中。(这也是有代价的，我们稍后会谈到)

```
 // Player.cs
class Player : MonoBehaviour
{
    public void Attack(IAttack attack)
    {
        attack.Execute(this);
    }
}

// IAttack.cs
interface IAttack
{
    void Execute(Player player);
}

// Attack.cs
public abstract class Attack : ScriptableObject, IAttack
{
    public abstract void Execute(Player player);
}

// MeleeAttack.cs
[CreateAssetMenu(menuName="Custom/Attacks/Melee Attack")]
class MeleeAttack : Attack
{
    public int Damage = 1;

    public override void Execute(Player player)
    {
        // TODO: Melee attack logic
    }
}

// CrossbowAttack.cs
[CreateAssetMenu(menuName="Custom/Attacks/Crossbow Attack")]
class CrossbowAttack : Attack
{
    public int Damage = 3;
    public float Range = 20f;
    public int Arrows = 5;

    public override void Execute(Player player)
    {
        // TODO: Crossbow attack logic
    }
} 

```

这里有几件事是不同的:

*   我们添加了一个抽象类`Attack`，它继承了`ScriptableObject`并实现了`IAttack`
*   `MeleeAttack`和`CrossbowAttack`继承`Attack`而不是直接使用接口
*   添加了一个属性`CreateAssetMenu`——这使得在 Unity 编辑器中右键单击并为特定类型的攻击添加新的资产成为可能。
*   属性被更改为字段——这意味着我们现在也可以在 Unity 编辑器中为我们的资产编辑这些值

这也意味着可以为同一类型的攻击创建不同的资产。例如，我们可以有一个弱近战攻击和一个强近战攻击，也许会增加使用这种攻击的耐力成本。

这种方法的灵活性非常大。逻辑与`Player`的分离使得它更加容易。

另一件事意味着，不是程序员的人可以很容易地添加新的攻击配置，而不用接触代码！

### 无往不利

老实说，这没什么大不了的，但可能会节省你一些时间:记住这些`ScriptableObject`是资产。这意味着当你将一个`ScriptableObject`附加到一个游戏对象时，它将使用该资产的同一个实例。即使两个不同的对象正在使用它。

这就是为什么*状态*应该**永远不要**存储在`ScriptableObject`中。请仅将它视为配置和行为。

另一件事是你不能在运行中实例化一个`ScriptableObject`，Unity 不允许。但是，您可以使用这个工厂方法:

 `ScriptableObject.CreateInstance()` 

这将给你一个`ScriptableObject`的新实例，它是**而不是**资产。

这是我在这里的第一篇帖子，所以，你好！:)
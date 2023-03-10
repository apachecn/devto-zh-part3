# 接口和抽象类

> 原文：<https://dev.to/rodrigosyscop/interfaces-and-abstract-classes-35oh>

对于我在软件开发过程中谈到的大多数人来说，*抽象*总是带来*代码重用*的想法。您可能会想到公司和个人对象，它们都有姓名、地址、电话联系人等等。他们都有共同的属性，甚至可能有共同的行为。你可以定义一个名为`Entity`的抽象基类，然后创建两个从`Entity`扩展而来的具体类`Person`和`Company`。

让我们后退一步，想象一下基本的 PHP 类型，boolean，integer，float，objects 和所有你能使用它们的方式。每个人都知道或者至少应该知道这些类型的预期行为。我的意思是，如果你创建了一个整型变量，你就知道该期待什么样的值，以及可以用它执行什么样的操作。

> 每种类型都有自己的稳定行为。

编程语言关心其基本类型的不可改变的行为。当我们定义抽象类和它们的具体实现时，同样的想法也适用。

## 抽象类

当从其他类扩展时，我们正在创建新的*数据类型*。下面的代码片段定义了一个新的子类`ArrayCache`，这个**是一个** `AbstractCache`类。因此，子类是一个规范，它将继承父类的所有非私有属性和方法。**客户端代码仍然希望它们的行为和它们的父代码完全一样** :

```
abstract class AbstractCache
{
    abstract public function get(string $key)
    { }

    abstract public function set(string $key, $value)
    { }
} 
```

```
class ArrayCache extends AbstractCache
{
    protected $items;

    public function get(string $key)
    {
        return $this->item[$key];
    }

    public function set(string $key, $value)
    {
        $this->item[$key] = $value;
    }
} 
```

根据 Liskov 替换原理，我们可以使用这些子类型，不需要任何专门的知识，只需要读取父类公共接口。

来自同一个抽象基类的所有具体实现都应该服从抽象类接口。使用继承时，尊重父类接口是我们的责任。当处理继承时，尽量保持层次简短，不要超过三层。另外，请记住，如果在客户端的*父*类中有一些代码被用作`parent::method()`，那么您也必须关心返回值。

> 当我们提供公共方法时，我们是在说:“使用这种数据类型，你可以这样做，它将以这种特定的方式运行。”

你可以把抽象理解为去掉不必要的细节，用更简单的术语描述一些东西，以便只关注当前上下文的重要方面。封装在这里起着主要的作用，作为一种用于对外界隐藏某些状态和行为的技术。客户端代码可以通过它的方法与之交互，但不能直接访问它的状态。

换句话说:

> 公共接口是一组规则，它定义了我们与对象交互的方式。

## 界面

当您试图对不同的类型实施相同的行为时，您可能想要利用*接口*。

PHP 中的接口可以包含方法和常量，但不能包含任何变量。所有方法都必须是公共的，并且没有实现。同样，一个接口可以通过*扩展*关键字从另一个接口继承。尽管类可以从多个接口继承，但不能实现共享相同方法名的两个接口，这将导致模糊性。

因此，在定义数据类型的层次结构时，我们通常使用抽象来实现代码重用。至于接口，它的常见用途是强制执行某些行为。

例如，我们想确保这两种类型，`Company`和`Person`可以通过提供自己的键和自己的值来缓存:

```
interface Cacheable
{
    public function getCacheKey();
    public function getCacheValue();
} 
```

我们必须创建`Company`类和由`Cacheable`接口强加的方法:

```
class Company extends Entity implements Cacheable
{
    public function getCacheKey()
    {
        return 'company_' . $this->id;
    }

    public function getCacheValue()
    {
       return [
           'name' => $this->name,
           'phone' => $this->phone,
           'address' => $this->address,
           'foundation_date' => $this->foundation_date
       ];
    }
} 
```

同样适用于`Person`类:

```
class Person extends Entity implements Cacheable
{
    public function getCacheKey()
    {
        return 'person_' . $this->id;
    }

    public function getCacheValue()
    {
       return [
           'name' => $this->name,
           'phone' => $this->phone,
           'address' => $this->address,
           'birth_date' => $this->birth_date
       ];
    }
} 
```

使用这种*反转*的方法，所有的缓存逻辑都封装在一个*可缓存的*类中。每个类都必须定义自己的规则，以及它应该如何存储在缓存中。`Cache`类对这些细节一无所知，它信任`Cacheable`对象并调用它们的`getCacheKey`和`getCacheData`方法。

> 这种盲目的信任是使用接口的主要思想。我们正在签订一份双方都必须遵守的合同。

我们现在可以这样重新定义我们的`ArrayCache`:

```
class AbstractCache extends AbstractCache
{
    protected $items;

    abstract public function get(Cacheable $entity)
    {
        $key = $entity->getCacheKey();
        return $this->item[$key];
    }

    abstract public function set(Cacheable $entity, $value)
    {
        $key = $entity->getCacheKey();
        $data = $entity->getCacheValue();

        $this->item[$key] = $value;
    }
} 
```

请记住，接口并没有定义一个新的类型，它们描述了一个类型的一个方面。

## 参考文献

*   [https://daylerees.com/php-pandas-interfaces/](https://daylerees.com/php-pandas-interfaces/)
*   [https://daylerees.com/php-interfaces-explained/](https://daylerees.com/php-interfaces-explained/)
*   [https://leanpub.com/phpoopway](https://leanpub.com/phpoopway)
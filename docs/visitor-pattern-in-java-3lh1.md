# Java 中的访问者模式

> 原文：<https://dev.to/vicentemaldonado/visitor-pattern-in-java-3lh1>

[![](img/e836c735cf17f5ffb590532d0875a65b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wFuvFiWr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2As3CV9XEYJ--O0Pg02hPwig.jpeg)

当我发现一个概念很难理解时，我会尽量把它精简到最基本的部分。这是我最近在[访客模式](https://en.wikipedia.org/wiki/Visitor_pattern)中遇到的，所以下面是我对此的看法。当然，我会感谢任何更正。开始了。

假设我们有三个从同一个父类派生的类，叫做

```
abstract class A
{
    public String name;
    abstract void accept(Visitor v);
} 
```

具有两个对象作为组件的 B 类:

```
class B extends A
{
    public A child1;
    public A child2;

    public B(String name)
    {
        this.name = name;
    }

[@Override](http://twitter.com/Override)
    void accept(Visitor v)
    {
        v.visitB(this);
    }
} 
```

具有一个组件的 C 类:

```
class C extends A
{
    public A child;

    public C(String name)
    {
        this.name = name;
    }

[@Override](http://twitter.com/Override)
    void accept(Visitor v)
    {
        v.visitC(this);
    }
} 
```

以及没有组件的 D 类

```
class D extends A
{
    public D(String name)
    {
        this.name = name;
    }

[@Override](http://twitter.com/Override)
    void accept(Visitor v)
    {
        v.visitD(this);
    }
} 
```

这三个类都公开了一个属性，一个让我们区分它们的实例的名称，以及一个允许访问者访问它们的名为 accept 的方法。这些类不关心也不需要知道他们的访问者在做什么。访问者是一个界面:

```
interface Visitor
{
    public void visitB(B b);
    public void visitC(C c);
    public void visitD(D d);
} 
```

它访问的每个类都有一个方法。让我们用一个 visitor 实现来尝试一下，它只打印出它所访问的对象的名称:

```
class PrintVisitor implements Visitor
{
    public void visitB(B b)
    {
        b.child1.accept(this);
        System.out.println(b.name + " visited.");
        b.child2.accept(this);
    }

    public void visitC(C c)
    {
        System.out.println(c.name + " visited.");
        c.child.accept(this);
    }

    public void visitD(D d)
    {
        System.out.println(d.name + " visited.");
    }
} 
```

访问者是递归的:它访问一个树节点，然后访问其子节点。现在让我们制作一个由类 B、C 和 D 组成的树:

```
 /\*
            F
          / \
        B G
      / \ \
     A D H
         / \ \
        C E I

        \*/ 
```

有九个对象和七种关系。首先，创建对象:

```
 B f = new B("F");
        B b = new B("B");
        B d = new B("D");

        C g = new C("G");
        C h = new C("H");

        D a = new D("A");
        D c = new D("C");
        D e = new D("E");
        D i = new D("I"); 
```

接下来，关系:

```
 f.child1 = b;
        f.child2 = g;

        b.child1 = a;
        b.child2 = d;

        d.child1 = c;
        d.child2 = e;

        g.child = h;
        h.child = i; 
```

最后通过访问它的根节点开始访问我们的树:

```
 PrintVisitor v = new PrintVisitor();
        f.accept(v); 
```

输出为:

```
A visited.
B visited.
C visited.
D visited.
E visited.
F visited.
G visited.
H visited.
I visited. 
```

如果看[这篇文章](https://en.wikipedia.org/wiki/Tree_traversal)，上面的代码执行的是树遍历，在那个()上什么叫做[有序遍历](https://en.wikipedia.org/wiki/Tree_traversal#In-order_(LNR))。让我们修改 visitor 类来做一个前序遍历——visitor 首先显示节点名，然后访问其子节点:

```
class PrintVisitor implements Visitor
{
    public void visitB(B b)
    {
        System.out.println(b.name + " visited.");
        b.child1.accept(this);
        b.child2.accept(this);
    }

    public void visitC(C c)
    {
        System.out.println(c.name + " visited.");
        c.child.accept(this);
    }

    public void visitD(D d)
    {
        System.out.println(d.name + " visited.");
    }
} 
```

现在输出是:

```
F visited.
B visited.
A visited.
D visited.
C visited.
E visited.
G visited.
H visited.
I visited. 
```

在[后序遍历](https://en.wikipedia.org/wiki/Tree_traversal#Post-order_(LRN))中，访问者首先访问节点子节点，然后才显示其名称:

```
class PrintVisitor implements Visitor
{
    public void visitB(B b)
    {
        b.child1.accept(this);
        b.child2.accept(this);
        System.out.println(b.name + " visited.");
    }

    public void visitC(C c)
    {
        c.child.accept(this);
        System.out.println(c.name + " visited.");
    }

    public void visitD(D d)
    {
        System.out.println(d.name + " visited.");
    }
} 
```

下面是输出:

```
A visited.
C visited.
E visited.
D visited.
B visited.
I visited.
H visited.
G visited.
F visited. 
```

除了我在开头链接的维基百科文章之外，这里还有一个关于访问者模式的很好的描述。简而言之:

*   被访问的对象不需要知道他们的访问者做什么，他们只需要接受他们。
*   需要有一个协议让被访问的对象和访问者进行通信，在我们的例子中是访问者接口。
*   一个访问者使用不同的方法(即 visitB、visitC 和 visitD 访问每个类)

(可以在 [Github](https://github.com/Wurdlack/Medium/blob/master/visitor_test/Main.java) 上找到代码。)
# Groovy 的比较运算符和等式

> 原文：<https://dev.to/joemccall86/groovy-s-compareto-operator-and-equality-312n>

让我们假设我们有一个类似这样的 Book 类:

```
@CompileStatic
class Book {
  String name
  String author
  Float price
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们的应用程序中，有几个地方可以向最终用户展示小部件，比如驱动移动应用程序的 API、驱动单页面应用程序的 API(比如 react/vue/etc ),甚至是服务器端呈现的东西，比如 GSP。

数据表现得很好，客户也很满意，但是现在有一条业务规则规定所有的书都应该按字母顺序排列。我们可以通过几种方式做到这一点:

*   在控制器中排序
*   在视图本身中进行排序
*   (编辑)指定域类中的排序顺序

编辑:如果您正在使用支持它的数据源，您可以直接在`mapping`闭包中指定排序列和顺序。这篇文章的观点仍然站在 GORM 的上下文之外。如果您有任何 POGO 列表，您可能会受到影响。

如果我们在控制器中对它进行排序，它看起来像这样:

```
@ReadOnly
def index() {
   // old code
   //respond Book.list()

   // new code to sort by name
   respond Book.list().sort { it.name }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但是一些开发人员可能想要记录一本书的“自然顺序”是按名称排序的事实。换句话说，事实存储在域本身中，它应该如何对 T2 进行排序。让我们用 java 的`compareTo`方法:
来实现它

```
@CompileStatic
class Book implements Comparable<Book> {
    String name
    String author
    Float price

    @Override
    int compareTo(Book other) {
        // Sort by name ascending
        return this.name <=> other.name
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的控制器方法看起来像这样:

```
@ReadOnly
def index() {
    // old code
    //respond Book.list()

    // new code to sort, this time by using the pre-defined "natural order"
    respond Book.list().sort()
} 
```

Enter fullscreen mode Exit fullscreen mode

## 这有一个非常严重的副作用

以上是*而不是*理想的解决方案，最好用一个具体的例子来说明。

```
def book1 = new Book(name: 'The Gadfly', author: "Ethel Voynich", price: 19.95)
def book2 = new Book(name: 'The Gadfly', author: "Johnny Copycat", price: 9.95)

assert book1 != book2 // THIS FAILS!!! GROOVY THINKS THEY ARE EQUAL! 
```

Enter fullscreen mode Exit fullscreen mode

这两本书是平等的，这有点令人担忧。事实显然并非如此。

### 发生什么事了？

我一会儿会谈到技术原因，但是根据观察，它似乎只是检查我们类的`name`字段是否相等，并就此打住。

在这一点上，我们所知道的是，这种语言错误地解释了两本书之间平等的概念。它认为仅仅因为书籍名称匹配，书籍对象就必须相等。

### 让我们试着解决这个问题

我们引入了自然排序，但它显然影响了书籍平等的定义。因此，让我们尝试纠正这种情况，定义一个`equals`方法，这样就不会混淆:

```
@CompileStatic
class Book implements Comparable<Book> {
    String name
    String author
    Float price

    @Override
    int compareTo(Book other) {
        // Sort by name ascending
        return this.name <=> other.name
    }

    @Override
    boolean equals(Book other) {
        this.name == other.name &&
            this.author == other.author &&
            this.price == other.price
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们表达了这样一种观点:只有在名字、作者和价格匹配的情况下，一本书才能与另一本书相提并论。

我们来测试一下:

```
def book1 = new Book(name: 'The Gadfly', author: "Ethel Voynich", price: 19.95)
def book2 = new Book(name: 'The Gadfly', author: "Johnny Copycat", price: 9.95)

assert !book1.equals(book2) // This passes, we should be ok
assert book1 != book2 // THIS STILL FAILS!!! WHY IS THIS?! 
```

Enter fullscreen mode Exit fullscreen mode

### 为什么没有定义`equals`修正`==`？

毕竟，groovy 中的`==`操作符不就是委托给了`equals`方法吗？

我们再仔细看看文档:[http://docs . groovy-lang . org/latest/html/documentation/index . html # _ behavior _ of _ code _ code](http://docs.groovy-lang.org/latest/html/documentation/index.html#_behaviour_of_code_code)

> 在 Java 中==表示对象的基本类型或身份相等。如果它们是可比较的，则在 Groovy 中==转换为 a.compareTo(b)==0，否则转换为 a.equals(b)。要查身份，还有就是。例如 a 是(b)。

哦。这真的阻碍了我们定义自然秩序的能力。即使我们定义了`equals`，如果我们的类实现了`Comparable`也会被忽略。

### 有什么解决办法？

我们来看看 Java 对`Comparable`接口的推荐:[https://docs . Oracle . com/javase/7/docs/API/Java/lang/comparable . html](https://docs.oracle.com/javase/7/docs/api/java/lang/Comparable.html)

> 强烈建议(尽管不是必需的)自然排序与 equals 保持一致。这是因为没有显式比较器的排序集(和排序映射)在与自然排序与 equals 不一致的元素(或键)一起使用时表现“奇怪”。特别是，这种有序集合(或有序映射)违反了集合(或映射)的一般约定，集合(或映射)是根据 equals 方法定义的。

在 groovy 中，如果我们遵循这个建议，一切都会很好。所以让`compareTo`和`equals`一致:

```
@CompileStatic
class Book implements Comparable<Book> {
    String name
    String author
    Float price

    @Override
    int compareTo(Book other) {
        // Sort by name ascending
        int cmp = this.name <=> other.name

        // Then sort by author, then price
        if (!cmp) {
            cmp = this.author <=> other.author
        }

        if (!cmp) {
            cmp = this.price <=> other.price
        }

        return cmp
    }

    @Override
    boolean equals(Book other) {
        this.name == other.name &&
            this.author == other.author &&
            this.price == other.price
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们在另一个实例上调用`compareTo`时，它只会在实例相等时返回`0`。

```
def book1 = new Book(name: 'The Gadfly', author: "Ethel Voynich", price: 19.95)
def book2 = new Book(name: 'The Gadfly', author: "Johnny Copycat", price: 9.95)

assert book1.compareTo(book2) != 0 // passes
assert !book1.equals(book2) // passes
assert book1 != book2 // passes 
```

Enter fullscreen mode Exit fullscreen mode

### 代码太多了。有没有更好的办法？

我们可以使用 Groovy 的内置转换，用少得多的代码实现同样的结果:

```
@CompileStatic
@Sortable(includes = ['name', 'author', 'price'])
@EqualsAndHashCode // Not required, but here for completeness
class Book {
    String name
    String author
    Float price
} 
```

Enter fullscreen mode Exit fullscreen mode

`@Sortable`注释为我们实现了`Comparable`接口。此外，如果使用了`includes`参数，它将按照字段列出的顺序检查字段。

我在这里也列出了生成这些方法的`@EqualsAndHashCode`转换，只是因为我们在上面的代码片段中有它们。它们对于通过我们的测试并不是绝对必要的，因为 Groovy 只会查看`compareTo`，但是我认为记录下*我们*在这个类中的某个地方*定义了等式*是很重要的，这是实现这一点的一种方式。

提醒一句:一定要包括`@Sortable`包含列表中的所有字段。否则，阶级的自然秩序就会与平等不一致。

### 如果我想使用自定义的`equals`方法呢？

我想不出你需要它的理由，但是它应该能够做这样的事情:

```
 @Override
    boolean equals(Book other) {
        // custom definition of equality
        // ...
        return equalsObject(other)
    }

    @Override
    int compareTo(Book other) {
        if (this.equals(other)) {
            return 0

        } else {
            // Normal sort logic from above:

            // Sort by name ascending
            int cmp = this.name <=> other.name

            // Then sort by author, then price
            if (!cmp) {
                cmp = this.author <=> other.author
            }

            if (!cmp) {
                cmp = this.price <=> other.price
            }

            return cmp
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

记住，关键是要保证自然秩序与平等相一致。

我希望这能帮助一些人！
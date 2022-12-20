# 扔还是不扔？这是个问题

> 原文：<https://dev.to/tohidheshmati/tostring-or-not-to-tostring--this-is-the-question-5coa>

一句**“你好世界！”将这句话打印在屏幕上的程序是几乎所有程序员编写的第一个程序。
您可能遇到过这样的情况，您传递了一个对象给 print 或 println 方法，结果收到了一个奇怪的 **name@number** 的回答。
在这个例子中，我们将一个对象传递给 println 函数，没有任何附加。** 

```
public class ActionClass {
    public static void main(String[] args) {
        Book book = new Book("Odyssey","Homer");
        System.out.println(book);
    }

    private static class Book {
        String name;
        String author;

        public Book(String name, String author) {
            this.name = name;
            this.author = author;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

结果可能如下所示:

```
Book@5a39699c 
```

Enter fullscreen mode Exit fullscreen mode

类被称为引用变量。参考变量能够存储对象的位置。
再深入一点，我们会发现原因是打印功能的实现。通过将一个对象传递给打印函数，我们编写的实际命令是:

```
System.out.println(book); 
```

Enter fullscreen mode Exit fullscreen mode

但是 Java 看到并运行的是:

```
System.out.println(book.toString()); 
```

Enter fullscreen mode Exit fullscreen mode

嗯。现在我们有了另一种方法。头罩下的 toString()方法看起来是这样的:

```
public String toString() {
        return this.getClass().getName() + "@" + Integer.toHexString(this.hashCode());
    } 
```

Enter fullscreen mode Exit fullscreen mode

啊哈，这就解释了 **className@weirdNumber** 的神秘案件。

有什么建议吗？是的，当然。通过为我们的对象覆盖 **toString()** 方法，我们可以轻松地将对象传递给打印方法，而不用将它们转换成字符串。

```
@Override
        public String toString() {
            return "Book{" +
                    "name='" + name + '\'' +
                    ", author='" + author + '\'' +
                    '}';
        } 
```

Enter fullscreen mode Exit fullscreen mode

这样做之后，我们在本文中编写的第一个代码将会有如下结果，而没有添加 **toString()** 方法。

```
Book{name='Odyssey', author='Homer'} 
```

Enter fullscreen mode Exit fullscreen mode
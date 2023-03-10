# 新的时候如何使用 PowerMockito

> 原文：<https://dev.to/aldok/how-to-use-powermockito-whennew-16m2>

`PowerMockito.whenNew`是一个强大的函数，用来存根一个构造函数。本文将展示一些我们使用 whenNew 时的场景，以及我在这个过程中遇到的一些问题。

假设我们有两个类，BookDao 和 BookRepository。

```
public enum BookDao {

    INSTANCE;

    public String getRecentBookTitle() {
        BookRepository repository = new BookRepository();
        return repository.queryBookTitle();
    }
}

public class BookRepository {

    public String queryBookTitle() {
        return "xUnit Test Patterns";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

BookRepository 在 getRecentBookTitle 块上实例化。如何测试 bookDao.getRecentBookTitle()？

```
@RunWith(PowerMockRunner.class)
@PrepareForTest({BookRepository.class})
public class BookDaoTest {

    @Test
    public void shouldReturnEffectiveJava() throws Exception {
        String bookTitle = BookDao.INSTANCE.getRecentBookTitle();

        // How to verify queryBookTitle?
        // Mockito.verify(mock).queryBookTitle();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

理想的解决方案是让 BookDao 通过构造函数或 setter 注入来改变它的 BookRepository 类。然而，让我们假设我们不想修改现有的源代码。然而，我们仍然希望验证是否调用了 queryBookTitle。

这是我们可以在新的时候使用 PowerMockito 的地方。使用 whenNew，我们可以用一个模拟来存根 BookRepository 创建。

```
BookRepository mock = Mockito.mock(BookRepository.class);
PowerMockito.whenNew(BookRepository.class).withAnyArguments().thenReturn(mock);

// Verify BookRepository is created
PowerMockito.verifyNew(BookRepository.class).withNoArguments(); 
```

Enter fullscreen mode Exit fullscreen mode

您甚至可以使用 withArguments
来指定构造函数的参数

```
// Assume BookDao constructor have two arguments
PowerMockito.whenNew(BookRepository.class).withArguments(true, ArgumentMatchers.anyString()).thenReturn(mock); 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们有了 BookRepositry mock，我们可以做很多事情，包括存根 BookRepository queryBookTitle 返回值。

```
// Given
BookRepository mock = Mockito.mock(BookRepository.class);
Mockito.when(mock.queryBookTitle()).thenReturn("Effective Java");
PowerMockito.whenNew(BookRepository.class).withAnyArguments().thenReturn(mock);

// When
String bookTitle = BookDao.INSTANCE.getRecentBookTitle();

// Then
Assert.assertEquals("Effective Java", bookTitle); 
```

Enter fullscreen mode Exit fullscreen mode

另一个用例是捕获 BookRepository 参数。在这个示例中，BookRepository 有一个新的 getBookByIsbn 方法，它将 String 作为参数。

```
// Given
String isbn = "978-0134685991";
ArgumentCaptor<String> isbnCaptor = ArgumentCaptor.forClass(String.class);

BookRepository mock = Mockito.mock(BookRepository.class);
PowerMockito.whenNew(BookRepository.class).withNoArguments().thenReturn(mock);

// When
String ignoredBookTitle = BookDao.INSTANCE.getBookByIsbn(isbn);
Mockito.verify(mock).queryBookTitle(isbnCaptor.capture());

// Then
Assert.assertEquals(isbn, isbnCaptor.getValue()); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以验证 queryBookTitle 中的 isbn 值与传递给 BookDao 的值相似。很酷不是吗？

## 明白了！

如果您尝试运行上面的代码，您可能会遇到这些错误:

*   想要但没有调用
*   org . JUnit . comparison 失败

这是因为我们未能 stub BookRepository 模拟。但是为什么呢？我们不是已经在@PrepareForTest 上添加了 BookRepository 吗？这是真的，但是 PowerMockito 要求我们指定创建 BookRepository 的类，即 BookDao。而不是图书仓库本身。

```
@RunWith(PowerMockRunner.class)
// You should also add BookDao on PrepareForTest! Even though you didn't mock or stub BookDao class.
@PrepareForTest({BookDao.class})
public class BookDaoTest {

    @Test
    public void shouldReturnEffectiveJava() throws Exception {
       // ...
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的测试是绿色的。

## 总结

使用 PowerMockito.whenNew，我们可以非常容易地用模拟对象来存根构造函数。也就是说，三思你当前的设计，重构你的源代码是更好的选择。毕竟，这是我们进行单元测试的原因之一，以减少代码中的代码味道。这里是这篇文章的完整源代码。希望你觉得有用。

**BookDao.java**

```
public enum BookDao {

    INSTANCE;

    public String getBookFromCache() {
        BookRepository repository = new BookRepository(true, "http://example.com:4416");
        return repository.queryBookTitle();
    }

    public String getRecentBookTitle() {
        BookRepository repository = new BookRepository();
        return repository.queryBookTitle();
    }

    public String getBookByIsbn(String isbn) {
        BookRepository repository = new BookRepository();
        return repository.queryBookTitle(isbn);
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

**BookRepository.java**T2】

```
public class BookRepository {

    // unused variables, for demonstration purpose only
    private boolean useCache;

    private String connectionUrl;

    public BookRepository(boolean useCache, String connectionUrl) {
        this.useCache = useCache;
        this.connectionUrl = connectionUrl;
    }

    public BookRepository() {
        this(false, "");
    }

    public String queryBookTitle(String isbn) {
        return queryBookTitle();
    }

    public String queryBookTitle() {
        return "xUnit Test Patterns";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

**BookDaoTest.java**T2】

```
 import com.aldoapps.mybookshop.BookDao;
import com.aldoapps.mybookshop.BookRepository;
import org.junit.Assert;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.ArgumentCaptor;
import org.mockito.ArgumentMatchers;
import org.mockito.Mockito;
import org.powermock.api.mockito.PowerMockito;
import org.powermock.core.classloader.annotations.PrepareForTest;
import org.powermock.modules.junit4.PowerMockRunner;

@RunWith(PowerMockRunner.class)
@PrepareForTest({BookDao.class})
public class BookDaoTest {

    @Test
    public void shouldReturnEffectiveJava1() throws Exception {
        // Given
        BookRepository mock = Mockito.mock(BookRepository.class);
        Mockito.when(mock.queryBookTitle())
               .thenReturn("Effective Java");

        PowerMockito.whenNew(BookRepository.class)
                    .withAnyArguments()
                    .thenReturn(mock);

        // When
        String bookTitle = BookDao.INSTANCE.getRecentBookTitle();

        // Then
        PowerMockito.verifyNew(BookRepository.class)
                    .withNoArguments();

        Assert.assertEquals("Effective Java", bookTitle);
        Mockito.verify(mock)
               .queryBookTitle();
    }

    @Test
    public void shouldReturnEffectiveJava2() throws Exception {
        // Given
        BookRepository mock = Mockito.mock(BookRepository.class);
        Mockito.when(mock.queryBookTitle())
               .thenReturn("Effective Java");

        PowerMockito.whenNew(BookRepository.class)
                    .withArguments(ArgumentMatchers.anyBoolean(),
                            ArgumentMatchers.anyString())
                    .thenReturn(mock);

        // When
        String bookTitle = BookDao.INSTANCE.getBookFromCache();

        // Then
        PowerMockito.verifyNew(BookRepository.class)
                    .withArguments(ArgumentMatchers.anyBoolean(), 
                                   ArgumentMatchers.anyString());

        Assert.assertEquals("Effective Java", bookTitle);
        Mockito.verify(mock)
               .queryBookTitle();
    }

    @Test
    public void shouldHaveSimilarIsbnValue() throws Exception {
        // Given
        String isbn = "978-0134685991";
        ArgumentCaptor<String> isbnCaptor = ArgumentCaptor.forClass(String.class);

        BookRepository mock = Mockito.mock(BookRepository.class);

        PowerMockito.whenNew(BookRepository.class)
                    .withNoArguments()
                    .thenReturn(mock);

        // When
        String ignoredBookTitle = BookDao.INSTANCE.getBookByIsbn(isbn);

        // Then
        PowerMockito.verifyNew(BookRepository.class)
                    .withNoArguments();

        Mockito.verify(mock)
               .queryBookTitle(isbnCaptor.capture());
        Assert.assertEquals(isbn, isbnCaptor.getValue());
    }
} 
```

Enter fullscreen mode Exit fullscreen mode
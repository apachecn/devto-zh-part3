# 使用 OnPush 更改检测和更新 UI

> 原文：<https://dev.to/prestonjlamb/using-onpush-change-detection-and-updating-the-ui-3ei4>

Angular 有两种类型的变化检测。简单回顾一下，这两种类型是`Default`策略和`OnPush`策略。

每次应用程序发生变化时，都会运行`Default`策略。它可以是按钮点击、HTTP 调用、`setTimeout`或任何其他类型的计时器或用户交互。

另一方面，`OnPush`策略仅在满足以下四个条件之一时运行:

1.  `Input`参考变化
2.  源自组件或其子组件之一的事件(触发一个`@Output`事件)
3.  显式运行更改检测
4.  `async`管道接收新数据

在本文中，我们将主要讨论当情况 1 或 2 发生时，如何确保 UI 正确更新。如果你想了解 Angular 的变化检测的更多细节，去阅读 [Netanel Basal](https://netbasal.com/@NetanelBasal) 的这篇文章。

现在，我在看[丹·瓦林](https://www.twitter.com/DanWahlin)最新的关于棱角建筑的 Pluralsight 课程时偶然发现了这个话题。他谈到了使用`OnPush`变更检测策略的好处，但是这样做的时候，你的 UI 可能会有不更新的时候。这里有一种情况，您的应用程序可能不会自动更新，即使您使用了上面列表中应该触发变更检测的`@Input`和`@Output`方法。

假设您有一个应用程序，用户可以在其中添加或编辑他们喜欢的书籍。每本书都有标题、作者、评级和 ID。当应用程序加载时，它调用一个服务来获取图书列表，然后将它们缓存在服务中。之后，当图书被检索时，它们只是从服务中被检索。用户可以选择一本书进行编辑，也可以添加一本新书。当这种情况发生时，该书被直接添加到缓存的图书列表中，或者就地更新。至于组件，您有一个父组件，它包含一个列出书籍的子组件和另一个允许编辑这些书籍的子组件。这两个子组件是带有`OnPush`变更检测集的组件。下面你可以看到接口和服务的例子。

```
export interface Book {
  id: number;
  author: string;
  title: string;
  rating: number;
}

@Injectable({ providedIn: 'root' })
export class BooksService {
  private books: Book[] = [{
    id: 1,
    author: 'Brandon Mull',
    title: 'Fablehaven',
    rating: 3
  }];

  constructor() { }

  getBooks(): Observable {
    return of(this.books);
  }

  updateBook(book: Book): Observable {
    const idx = this.books.findIndex((bk: Book) => bk.id === book.id);
    this.books[idx] = book;
    return of(this.books);
  }

  addBook(): Observable {
    console.log('called')
    this.books.push({
      id: this.books.length + 1,
      title: 'New Book',
      author: 'Some Author',
      rating: 3
    });

    return of(this.books);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，在这种情况下，当添加或编辑一本书并对缓存的图书列表进行更改时，应用程序的 UI 不会更新。这是因为两个子组件的输入要么是对象(针对要编辑的选定书籍)，要么是数组(显示所有书籍)。因为它们是对象/数组，所以对内容的任何更改都不会触发更改检测周期。那是因为它们仍然指向内存中的同一个位置；换句话说，输入是通过引用传递的。从技术上来说，根据 Angular 的说法，变量从未改变，所以变化检测不会运行。

我们可以通过每次添加或编辑对象时返回对象列表的方式来改变这一点。我们可以返回一个克隆的数组，而不是每次都返回相同的数组。这在每次列表更新时都会改变它在内存中的位置，因此 UI 也会更新。这里有一个这样编写的服务的例子:

```
@Injectable({ providedIn: 'root' })
export class Books2Service {
  private books: Book[] = [{
    id: 1,
    author: 'Brandon Mull',
    title: 'Fablehaven',
    rating: 3
  }];

  constructor() { }

  private cloneBooksArray() {
    return JSON.parse(JSON.stringify(this.books));
  }

  getBooks(): Observable {
    return of(this.cloneBooksArray());
  }

  updateBook(book: Book): Observable {
    const idx = this.books.findIndex((bk: Book) => bk.id === book.id);
    this.books[idx] = book;
    return of(this.cloneBooksArray());
  }

  addBook(): Observable {
    this.books.push({
      id: this.books.length + 1,
      title: 'New Book',
      author: 'Some Author',
      rating: 3
    });

    return of(this.cloneBooksArray());
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我知道只写下来有点难以理解，所以我准备了一个 [StackBlitz 示例](https://stackblitz.com/edit/pjlamb12-change-detection-example)来演示。您需要做的就是改变在`app.component.ts`文件中使用的服务。`BooksService`不克隆返回的 books 数组；`Books2Service`确实克隆了这个数组。因此，第二个服务更新 UI，而第一个服务不更新。

在这个例子中，因为它是一个非常基本的例子，所以我每次都使用`JSON.stringify()`和`JSON.parse()`来克隆数组。如果对象或数组更复杂，或者有日期属性，您可能希望找到更好的解决方案。有许多第三方库可以为您克隆对象，任何一个都可以。
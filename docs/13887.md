# 角材料反应形式更新 Firestore

> 原文：<https://dev.to/codingcatdev/angular-material-reactive-forms-update-firestore-5a72>

> 原帖:[https://ajonp . com/courses/angular material/angular-material-reactive-forms-update-firestore/](https://ajonp.com/courses/angularmaterial/angular-material-reactive-forms-update-firestore/)

[https://www.youtube.com/embed/92hYB6jivvQ](https://www.youtube.com/embed/92hYB6jivvQ)

# 角状物质反应形态更新 Firestore

## 设置

我们可以从上一课开始，构建我们的反应式表单。
上一课:[棱角分明的材料构成 Firestore](https://github.com/AJONPLLC/lesson12-angular-material-forms-firestore)T3】

```
git clone https://github.com/AJONPLLC/lesson12-angular-material-forms-firestore 
```

Enter fullscreen mode Exit fullscreen mode

这将为我们开始工作提供一个坚实的基础，但是如果您正在创建一个新的 firebase 项目，您应该更改 environment/environment.ts 文件以匹配您的 firebase 详细信息。如果您从未这样做过，请参见[Angular Navigation Firestore](https://ajonp.com/lessons/11-angular-navigation-firestore/)，因为这将提供有关如何更新的更多详细信息。

确保你更新了你的 npm 包

```
npm install 
```

Enter fullscreen mode Exit fullscreen mode

# 更新图书型号

导航到 src/app/core/models/book.ts，以便我们可以更新更多关于我们将在教程中添加和编辑的书籍的详细信息。

你会注意到一个很大的变化，不再使用它作为一个只允许输入的接口，使用这个类将允许我们根据我们对 Book 的定义创建新的对象。我真的很喜欢 Todd Moto 在[类与 Typescript](https://ultimatecourses.com/blog/classes-vs-interfaces-in-typescript) 接口中对此的描述。

您可以在这里看到，我们还提供了一个构造函数，允许提供部分图书类型，将它指定为一本新书，而不需要完整的对象。你可以在这里阅读更多关于部分[的内容。对象 assign 将把所有可枚举的 own 属性的值从一个或多个源对象复制到一个目标对象，并返回目标对象，在我们的例子中，它将返回一个 Book 对象。](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-1.html)

src/app/core/models/book.ts

```
import { Timestamp } from '@firebase/firestore-types';

export class Book {
  ageCategory?: string;
  description?: string;
  fiction?: boolean;
  genre?: string;
  hasAudio?: boolean;
  hasPhotos?: boolean;
  hasVideos?: boolean;
  id?: string;
  publishDate?: Timestamp | Date;
  rating?: number;
  status?: string;
  title?: string;

  public constructor(init?: Partial<Book>) {
    Object.assign(this, init);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 凡士多当前账面价值形成

## 从 Id 订阅图书

> 请注意，稍后我已经更新了取消订阅主题的数组推送方法，并使用了`takeUntil(this.unsubscribe$)`。

我们在 ngOnInit 的第一部分中所做的是订阅路由器，获取我们指定的`bookId`,并设置全局变量来存储它，这样我们就可以使用这个 ID 来获取关于当前图书的数据。

src/app/modules/books/book-edit/book-edit . component . ts

```
 bookId: string;

  ...

  ngOnInit() {
    // Get bookId for book document selection from Firestore
    this.subs.push(
      this.route.paramMap.subscribe(params => {
        this.bookId = params.get('bookId');
        this.rebuildForm();
      })
    ); 
```

Enter fullscreen mode Exit fullscreen mode

## 建造(或重建)有棱角的形体

然后，我们可以用它来调用方法`rebuildForm()`,该方法将更新我们的角度表单上任何所需的绑定。
如果我们分解这个方法，我们可以看到有一条线设置了 blobal `book$`变量 Observable。不要被`this.subs.push`弄糊涂了，你甚至可以为了学习练习而省去这个(不过我会为一个生产应用留下一些不订阅的东西)。

接下来是`this.book$.pipe(map(book`，我们将`book.publishDate`从一个`Timestamp`转换成一个 Javascript 日期时间。这是必要的，因为我们的角度组件需要这种格式。

src/app/modules/books/book-edit/book-edit . component . ts

```
 bookForm: FormGroup;
  book$: Observable<Book>;

  ...

  rebuildForm() {
    if (this.bookForm) {
      this.bookForm.reset();
    }
    this.book$ = this.fs.getBook(this.bookId);
    this.subs.push(
      this.book$
        .pipe(
          map(book => {
            console.log(book.publishDate);
            if (book.publishDate) {
              const timestamp = book.publishDate as Timestamp;
              book.publishDate = timestamp.toDate();
            }
            return book;
          })
        )
        .subscribe(book => {
          this.bookForm = this.fb.group({
            ageCategory: [book.ageCategory, Validators.required],
            description: [
              book.description,
              [Validators.required, Validators.maxLength(500)]
            ],
            fiction: [book.fiction || false, Validators.required],
            genre: [book.genre, Validators.required],
            hasAudio: [book.hasAudio],
            hasPhotos: [book.hasPhotos],
            hasVideos: [book.hasVideos],
            id: [book.id],
            publishDate: [book.publishDate],
            rating: [book.rating, Validators.required],
            status: [book.status, Validators.required],
            title: [book.title, [Validators.required, Validators.maxLength(50)]]
          });
        })
    );
  } 
```

Enter fullscreen mode Exit fullscreen mode

### 使用表单生成器的表单控件

我们还使用`this.book$.subscribe(book`订阅了来自 Firestore 的可观察值，其中我们用来自 Firestore 的值设置了全局变量`bookForm`。我们使用依赖注入的[表单生成器](https://angular.io/guide/form-validation#adding-to-reactive-forms) `private fb: FormBuilder`或`fb`来创建一个包含所有必要表单控件的表单组。

在我们的表单中，我们可以引用这些控件，例如,`ageCategory: [book.ageCategory, Validators.required],` ageCategory 现在是一个 FormControl，它有一个来自 Firestore 的默认值`book.ageCategory`,它也是一个基于`Validators.required`的必填字段。

您可以看到，我们使用`formControlName="ageCategory"`来链接基于名称的表单控件。

```
<mat-select
  placeholder="Age Category"
  formControlName="ageCategory"
> 
```

Enter fullscreen mode Exit fullscreen mode

FormControl 验证的一些更有趣的用例是类似于`title: [book.title, [Validators.required, Validators.maxLength(50)]]`的东西，它说我们的标题不能超过 50。只是提醒一下，这都是基于前端的，所以有人可能会恶意添加一个更长的 book.title，所以你需要确保这是一个硬要求，你需要相应地调整你的 firestore.rules。

```
<input matInput placeholder="Title" formControlName="title" /> 
```

Enter fullscreen mode Exit fullscreen mode

### 表格字段错误

像魔术一样(好的编程)，如果一个字段验证不正确，你会看到一个错误出现。
[![Form Field](img/9dc1f5870cc1e621335cd24439161a3f.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--7dir-aJb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/v1553629225/ajonp-ajonp-com/12-angular-material-from-firestore/sr7u3gojyeztganortmm.png)

这是用组件`mat-error`通过 html 处理的，它必须在`mat-form-field`的内部，就像所有的角形材料组件一样。在我们的例子中，我们为标题显示两条消息，它是空白的，我们显示必填，然后如果它是错误的，当前不需要，我们显示最大长度为 50。

```
<mat-form-field style="width: 100%">
  <input matInput placeholder="Title" formControlName="title" />
  <mat-error *ngIf="!bookForm.get('title').hasError('required')">
    Title has a max length of 50.
  </mat-error>
  <mat-error *ngIf="bookForm.get('title').hasError('required')">
    Title is <strong>required</strong>
  </mat-error>
</mat-form-field> 
```

Enter fullscreen mode Exit fullscreen mode

### 表单只提交原始的

这里的一些逻辑看起来有点落后，但是因为我们禁用了按钮，所以一切都是反向应用的。对于取消，我们只关心数据是否被更改，所以我们检查`pristine`(输入的数据)，对于提交按钮数据必须是原始的，也是有效的。这意味着任何验证器都不能为假，比如 required 和 length。

在数据输入之前，我们只有取消的选择。
[![No Data](img/62510788a30c3c71f1b344e1ff5dd1d0.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--D1DzUtb0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/v1553629673/ajonp-ajonp-com/12-angular-material-from-firestore/u2cngx79xvpu9h1hfybp.png)

输入数据后，如果无效，我们只能恢复。
[![Bad Data](img/a4ccae08c8d4ec7ab7400544cbd83edf.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--h8mQ-wDj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/v1553629709/ajonp-ajonp-com/12-angular-material-from-firestore/jafwiibebcbqn7digvhe.png)

我们终于可以保存好数据了。
[![Good Data](img/e31ba6b451491c9dc6c1f0eca49994c5.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--NQceCSjj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/v1553629744/ajonp-ajonp-com/12-angular-material-from-firestore/fqqrn4hsnytvcadwag51.png)

## 提交并保存数据

一旦所有数据都是原始的和有效的，我们就可以按保存。

```
<button
  mat-raised-button
  color="primary"
  type="submit"
  [disabled]="bookForm.pristine || !bookForm.valid"
  aria-label="Save"
>
  Save
</button> 
```

Enter fullscreen mode Exit fullscreen mode

这个按钮在表单组件中，并有一个名为`(ngSubmit)="saveBookChanges()"`的方法。

```
<form
  *ngIf="bookForm"
  [formGroup]="bookForm"
  (ngSubmit)="saveBookChanges()"
  fxLayout="column"
> 
```

Enter fullscreen mode Exit fullscreen mode

当它调用方法`saveBookChanges`时，它将调用 firestore updateBook `await this.fs.updateBook(book);`，在导航回主图书列表之前，它会在其中等待。这也是您可以在 await 语句之前抛出保存对话框的地方。

你会注意到我们做的第一件事是创建了`Book`类，这是它变得非常有价值的地方。我们可以直接传递`bookForm.value`，它将创建一个新的`Book`对象来进行更新！

```
 async saveBookChanges() {
    const book = new Book(this.bookForm.value);
    await this.fs.updateBook(book);
    this.router.navigate(['/books', this.bookId]);
  } 
```

Enter fullscreen mode Exit fullscreen mode

# 视频

我认为本课的[视频](https://youtu.be/92hYB6jivvQ)是最好的指南，别忘了把那些断点放进去看看所有调用中发生了什么，打开 Firestore 看它自动更新。
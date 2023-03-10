# 火焰风暴形成的有棱角的物质

> 原文：<https://dev.to/codingcatdev/angular-material-forms-from-firestore-184i>

> 原帖:[https://ajonp . com/courses/angular material/angular-material-forms-from-firestore/](https://ajonp.com/courses/angularmaterial/angular-material-forms-from-firestore/)

[https://www.youtube.com/embed/LLupkLEszdY](https://www.youtube.com/embed/LLupkLEszdY)

# 来自 Firestore 的棱角分明的材料形态

🌎演示:[https://ajonp-lesson-12 . firebase app . com/books/first book/edit](https://ajonp-lesson-12.firebaseapp.com/books/FirstBook/edit)

这一课将讲述如何创建所有的[有角的材料形状组件](https://material.angular.io/components/categories/forms)，其中许多组件背后的数据将来自[云商店](https://firebase.google.com/docs/firestore/)。

# 设置

我们将从[Angular Navigation Firestore](https://ajonp.com/lessons/11-angular-navigation-firestore/)的上一课开始

## 来源于前一课

克隆

```
git clone https://github.com/AJONPLLC/lesson11-angular-navigation-firestore.git lesson12-angular-material-forms-firestore 
```

Enter fullscreen mode Exit fullscreen mode

移除原点，如果你想把它添加到你自己的回购协议中，总是比较容易的(或者你也可以直接复制。)

```
git remote rm origin 
```

Enter fullscreen mode Exit fullscreen mode

## 安装依赖项

确保您位于正确的目录`cd lesson12-angular-material-forms-firestore`。

```
npm install 
```

Enter fullscreen mode Exit fullscreen mode

# 图书编辑模块

## 创建

使用 Angular CLI 创建带有路由和相应组件的模块。

```
ng g m modules/books/book-edit --routing && ng g c modules/books/book-edit 
```

Enter fullscreen mode Exit fullscreen mode

# 路由器更新

## books-routing.module.ts

添加新的惰性加载路径，以便我们的主 books-routing 知道向哪里发送编辑请求。

```
...
  {
    path: ':bookId/edit',
    loadChildren: './book-edit/book-edit.module#BookEditModule'
  }
  ... 
```

Enter fullscreen mode Exit fullscreen mode

## 预订-编辑-路由. module.ts

现在我们在图书编辑模块中，确保它有一个到图书编辑组件的路径。

```
...
const routes: Routes = [
  {
    path: '',
    component: BookEditComponent
  }
];
... 
```

Enter fullscreen mode Exit fullscreen mode

## 服务编辑路径

启动服务器

```
ng serve 
```

Enter fullscreen mode Exit fullscreen mode

现在我们的路由器都设置好了，我们应该开始看到 book-edit.component.html。因为我们还没有办法导航到这个路径，只需手动在地址栏中键入它`localhost:4200/books/FirstBook/edit`。

你应该看看

### 图书编辑作品！

# 更新图书编辑

## 结构

为了给我们的表单添加一些结构，我们现在可以添加 Flex 布局和 75%居中的材料卡来给我们的表单一个漂亮的外观。

book-edit.component.html

```
<div fxLayout="column" fxLayoutAlign="space-around center">
  <mat-card style="width: 75%; margin-bottom: 100px;">
    <mat-card-content>
    </mat-card-content>
    <mat-card-actions> <button mat-button>Ok</button> </mat-card-actions>
  </mat-card>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

因为这些是新元素，我们需要将它们导入到我们的图书编辑模块中。
book-edit.module.ts

```
import { FlexLayoutModule } from '@angular/flex-layout';
import {MatCardModule} from '@angular/material';

...

@NgModule({
  declarations: [BookEditComponent],
  imports: [
    CommonModule,
    BookEditRoutingModule,
    FlexLayoutModule,
    MatCardModule,
  ]
})
... 
```

Enter fullscreen mode Exit fullscreen mode

## 获取 Firestore 数据进行图书编辑

因为我们现在正在导航到一个使用角度路由器的区域，并且部分路径包含一个指定的参数 id `:bookId/edit`，所以我们可以从当前激活的路径中获得这个`bookId`。为了做到这一点，我们需要使用依赖注入，并在我们的构造函数中提供这一点。为了从我们的`FirestoreService`获取数据，我们也可以注入这个服务。

```
 subs: Subscription[] = [];
  book$: Observable<Book>;
  constructor(private router: ActivatedRoute, private fs: FirestoreService) {}

  ngOnInit() {
    // Get bookId for book document selection from Firestore
    this.subs.push(
      this.router.paramMap.subscribe(params => {
        const bookId = params.get('bookId');
        this.book$ = this.fs.getBook(bookId);
      })
    ); 
```

Enter fullscreen mode Exit fullscreen mode

通过调用 firestore `getBook`函数并传入当前参数`bookId`,我们现在有了一个对 firestore 数据的可见引用。

firestore.service.ts

```
getBook(bookId: string): Observable<Book> {
  // Start Using AngularFirebase Service!!
  return this.afb.doc$<Book>(`books/${bookId}`);
} 
```

Enter fullscreen mode Exit fullscreen mode

> 这是杰夫在 [fireship.io](https://angularfirebase.com/lessons/firestore-advanced-usage-angularfire/) 创造的一个很酷的包装。
> 你可以随意复制这个服务，并把它作为你所有项目的一个漂亮的包装器，我不会把这两个调用包括在内

```
 doc$<T>(ref: DocPredicate<T>): Observable<T> {
    return this.doc(ref)
      .snapshotChanges()
      .pipe(
        map(
          (
            doc: Action<
              DocumentSnapshotDoesNotExist | DocumentSnapshotExists<T>
            >
          ) => {
            return doc.payload.data() as T;
          }
        )
      );
  } 
```

Enter fullscreen mode Exit fullscreen mode

Firestore 控制台中的图书数据示例:

[![Book Data](img/7989e459cb773654e514aa11e323e720.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y_VNqToF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1548959741/ajonp-ajonp-com/11-lesson-angular-navigation-firestore/yep4ngt60x0ki8jocisx.jpg)

在上面的代码片段中，我们还将我们的 RxJs 订阅推送到一个数组中，这样我们就可以在 destroy 方法和 unsubscribe 期间遍历任何订阅。当我不能使用`pipe(take(1))`或`| async`时，我经常使用这种模式。

```
 ngOnDestroy() {
    this.subs.forEach(sub => {
      sub.unsubscribe();
    });
  } 
```

Enter fullscreen mode Exit fullscreen mode

## 使用 Firestore 模板内的数据

我们不能在模板中使用新的`book$` Observable 来显示任何当前数据。我们可以更新我们的卡片，仅在图书数据可用时显示，否则显示材料微调器。如果你不熟悉 Angular 文档，你可以阅读更多关于 NgIf T2 指令如何工作的内容。

```
 <mat-card *ngIf="(book$ | async); let book; else: spin" style="width: 75%; margin-bottom: 100px;">
  <mat-card-title>{{book.title}}</mat-card-title>
  ...
<ng-template #spin><mat-spinner></mat-spinner></ng-template> 
```

Enter fullscreen mode Exit fullscreen mode

当前标题:
[![Title View](img/174a0eb018859dd6a02fe2e18d1b26b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H_xpAfyE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1548960658/ajonp-ajonp-com/11-lesson-angular-navigation-firestore/oclrlecxgwgbhbooidhg.jpg)

## 添加表单输入

> 这里只是一个警告，如果您想查看详细的[角反应形式](https://angular.io/guide/reactive-forms)用法，这将在下一课中完成。

现在我们知道我们的 Observable 正在成功地工作，我们现在可以改变卡片的标题，并开始将我们的卡片切换到几个表单输入中。

为此，我们需要将`FormsModule`、`ReactiveFormsModule`、`MatFormFieldModule`和`MatInputModule`包含到我们的图书编辑模块中。

book-edit.module.ts

```
 imports: [
    CommonModule,
    BookEditRoutingModule,
    FlexLayoutModule,
    MatCardModule,
    FormsModule,
    ReactiveFormsModule,
    MatFormFieldModule,
    MatInputModule, 
```

Enter fullscreen mode Exit fullscreen mode

### 标题输入

[![Title Input](img/f36a922e00a74c1782cd68659b48bfdc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xFPpCIU---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1548961254/ajonp-ajonp-com/12-angular-material-from-firestore/bvls9ivndkkacihjr2hp.jpg)

整个材料卡片现在应该看起来像下面这样，卡片周围的新 div 只是为了让每个部分在它自己的列中流动。如果您没有使用过[柔性布局](https://github.com/angular/flex-layout/wiki/fxFlex-API)，请查看详细信息。

book-edit.component.html

```
 <mat-card
    *ngIf="(book$ | async); let book; else: spin"
    style="width: 75%; margin-bottom: 100px;"
  >
    <mat-card-content>
      <div fxLayout="column" fxLayoutAlign="space-around stretch">
        <section>
          <mat-form-field style="width: 100%">
            <input matInput placeholder="Title" [(ngModel)]="book.title" />
          </mat-form-field>
        </section>
      </div>
    </mat-card-content>
    <mat-card-actions> <button mat-button>Ok</button> </mat-card-actions>
  </mat-card> 
```

Enter fullscreen mode Exit fullscreen mode

上面是我们的前两个组件

- [表单域](https://material.angular.io/components/form-field/overview)，你可以把它想象成我们所有组件的包装器，允许其他表单域的样式。

- [输入](https://material.angular.io/components/input/overview)这个键是指令`matInput`，这将允许你在`<mat-form-field>`内使用正确风格的本地`<input>`或`<textarea>`。

> 如果此时您发现任何问题，请确保您已经将所有模块导入到`book-edit.module.ts`

对于额外的，你也可以检查文本区的例子。

```
 <section>
    <mat-form-field style="width: 100%">
      <textarea
        matInput
        placeholder="Description"
        [(ngModel)]="book.description"
        rows="5"
      ></textarea>
    </mat-form-field>
  </section> 
```

Enter fullscreen mode Exit fullscreen mode

### 滑动切换

[![Slide Toggle](img/ef77b2d5fe67c3b9ed3469cb684668e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--asYZemiv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1548961935/ajonp-ajonp-com/11-lesson-angular-navigation-firestore/m2ox1ibaek2ybrj9wdsr.jpg)

- [Slide Toggle](https://material.angular.io/components/slide-toggle/overview) 是一个非常简单的组件，不是开就是关(二进制)。

book-edit.module.ts

```
 imports: [
    ...
       MatSlideToggleModule, 
```

Enter fullscreen mode Exit fullscreen mode

对于我们的要求，我们将使用这个来确定一本书是小说还是非小说。

我们将设置默认值`fictionSelected = true;`,以便首先设置虚构。

book-edit.component.ts

```
export class BookEditComponent implements OnInit, OnDestroy {
  subs: Subscription[] = [];
  book$: Observable<Book>;
  fictionSelected = true; 
```

Enter fullscreen mode Exit fullscreen mode

book-edit.component.html

```
<section>
  <mat-slide-toggle
    [checked]="fictionSelected"
    (change)="fictionChange($event)"
    #ficToggle
  >
    <p *ngIf="ficToggle.checked; else nonFic">Fiction</p>
  </mat-slide-toggle>
</section> 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到我们的输入指令`checked`(用`[]`表示)，现在将采用`fictionSelected`的值，每次切换改变时，我们将使用输出指令`change`(用`()`表示)来触发函数`fictionChange`，传入事件`$event`的当前组件实例。你可以阅读更多关于 [DOM 事件负载](https://angular.io/guide/user-input#get-user-input-from-the-event-object)的内容。

book-edit.component.ts

```
 fictionChange(e) {
    this.fictionSelected = e.checked;
    this.genreControl.reset();
  } 
```

Enter fullscreen mode Exit fullscreen mode

### 自动完成

<video controls="" loop=""><source src="https://res.cloudinary.com/ajonp/video/upload/v1548963680/ajonp-ajonp-com/12-angular-material-from-firestore/autocomplete.webm" type="video/webm"> <source src="https://res.cloudinary.com/ajonp/video/upload/v1548963680/ajonp-ajonp-com/12-angular-material-from-firestore/autocomplete.mp4" type="video/mp4"></video> 

*   在我们的例子中，自动完成只是文本的另一种输入类型。但是，它也有一个相关的面板来提供选项列表。对于我们的用例，它将基于我们的滑动开关列出两个不同的列表。所以我们要么过滤一般虚构的值列表，要么过滤非虚构的值列表。

book-edit.module.ts

```
 imports: [
    ...
       MatAutocompleteModule, 
```

Enter fullscreen mode Exit fullscreen mode

这两个列表将包含在我们 Firestore 数据库的新集合中，称为`config`。在我们的`config`集合中，我们将创建一个名为`book`的文档，它将保存我们的许多不同配置。特别是对于这两个，它们将是在`book`对象上的数组。

[![Firestore Fiction/Non-Fiction](img/311ee7324572240e89ca7ff845c60648.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ln_JIBKH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1548964455/ajonp-ajonp-com/11-lesson-angular-navigation-firestore/vnxbzg5fnpnuoevun1nu.jpg)

首先，我们将创建 Config Book 对象的可观察对象。
book-edit.component.ts

```
 bookConfig$: Observable<ConfigBook>;
  ...
    // Set Book Config
    this.bookConfig$ = this.fs.getConfigBook(); 
```

Enter fullscreen mode Exit fullscreen mode

为我们的类型创建`ConfigBook`接口。

```
ng g i core/models/config-book 
```

Enter fullscreen mode Exit fullscreen mode

config-book.ts

```
export interface ConfigBook {
  ageCategory?: Array<string>;
  fiction?: Array<string>;
  nonFiction?: Array<string>;
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以获取 Firestore 发出的第一组值，并将其作为类型为`ConfigBook`的[行为主题](http://reactivex.io/rxjs/manual/overview.html#behaviorsubject)发送出去。我们的开关已经设置了`this.fictionSelected`，所以我们可以决定在`this.genereList$`中应该发出什么列表。

book-edit.component.ts

```
export class BookEditComponent implements OnInit, OnDestroy {
  ...
  genreControl = new FormControl();
  ...
    // Set default Genere
    this.bookConfig$.pipe(take(1)).subscribe(bookConfig => {
      this.subs.push(
        this.genreControl.valueChanges.pipe(startWith('')).subscribe(value => {
          const filterValue = value ? value.toLowerCase() : '';
          if (this.fictionSelected) {
            this.genreList$.next(
              bookConfig.fiction.filter(option =>
                option.toLowerCase().includes(filterValue)
              )
            );
          } else {
            this.genreList$.next(
              bookConfig.nonFiction.filter(option =>
                option.toLowerCase().includes(filterValue)
              )
            );
          }
        })
      );
    }); 
```

Enter fullscreen mode Exit fullscreen mode

您还会注意到，我们已经订阅了在我们的新`genreControl`上发生的任何`valueChanges`。下面你会看到`formControl`输入指令被传递给了我们的类参数`genreControl`，它是`FormControl`的一个实例。我们将在下一课中更详细地探究所有的`@angular/forms`。为了我们的缘故，这里只需要知道这允许我们在您键入时检查所有变化的值。当我们开始输入时，它使用我们从 Firestore 传入的数组，并根据我们使用`bookConfig.fiction.filter`或`bookConfig.nonFiction.filter`输入的字符串过滤它们。

```
 <section>
    <mat-form-field style="width: 100%">
      <input
        name="genre"
        type="text"
        matInput
        [formControl]="genreControl"
        [matAutocomplete]="auto"
        placeholder="Genre"
        aria-label="Genre"
      />
    </mat-form-field>
    <mat-autocomplete #auto="matAutocomplete">
      <mat-option
        *ngFor="let genre of (genreList$ | async)"
        [value]="genre"
      >
        {{ genre }}
      </mat-option>
    </mat-autocomplete>
  </section> 
```

Enter fullscreen mode Exit fullscreen mode

上面我们正在收听来自`genreList$` BehaviorSubject 的更新，以创建我们的`<mat-option>`值列表。我们的输入有一个输入指令`[matAutocomplete]="auto"`，通过使用`matAutocomplete`将实例变量`#auto`分配给输入来附加这个`<mat-autocomplete>`。

### 复选框

[![Checkbox](img/df63304a220aa851c539e730e17361f8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y6MeBSLL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1548966370/ajonp-ajonp-com/11-lesson-angular-navigation-firestore/hhk37heauwmp6ns7minw.jpg)

> 深呼吸，自动完成功能已经完成了很多，剩下的就简单多了😺

*   [复选框](https://material.angular.io/components/checkbox/overview)再次提供了与原生`<input type="checkbox">`相同的功能，并增强了材料设计。

book-edit.module.ts

```
 imports: [
    ...
       MatCheckboxModule, 
```

Enter fullscreen mode Exit fullscreen mode

[![Firestore Config](img/58a53520ab640d1d2c15377e19ee43ca.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--urusc1fK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1548966831/ajonp-ajonp-com/11-lesson-angular-navigation-firestore/caewhsmpkrkxdo1dcj2d.jpg)

对我们来说，这包含了我们书中更多的配置项，因为我们已经在`bookConfig$`中创建了一个可观察对象，我们可以告诉 Angular 监听它，并将其分配给我们的本地模板变量`bookConfig`。然后我们将这些分配给指令 [NgModel](https://angular.io/api/forms/NgModel) 。这是一个双向绑定，因为我们的例子并不意味着什么，但我们将在下一课中进一步阐述这些内容。

```
<section *ngIf="(bookConfig$ | async); let bookConfig">
  <h3>Options</h3>
  <div fxLayout="column">
    <mat-checkbox [(ngModel)]="bookConfig.options.hasAudio">
      Audio
    </mat-checkbox>
    <mat-checkbox [(ngModel)]="bookConfig.options.hasPhotos">
      Photos
    </mat-checkbox>
    <mat-checkbox [(ngModel)]="bookConfig.options.hasVideos">
      Videos
    </mat-checkbox>
  </div> </section> 
```

Enter fullscreen mode Exit fullscreen mode

### [日期选择器](#datepicker)

[![Date Picker](img/d059740dd380cfd400532d3f519bcfd5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GMdi6woj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1548967267/ajonp-ajonp-com/11-lesson-angular-navigation-firestore/eaz6tqu7haioisymv83l.jpg)

*   Datepicker 允许用户通过文本输入或从日历中选择日期来输入日期。它由几个协同工作的组件和指令组成。

> 特别说明这里除了`MatDatepickerModule`还需要`MatNativeDateModule`。

book-edit.module.ts

```
 imports: [
    ...
       MatDatepickerModule,
       MatNativeDateModule 
```

Enter fullscreen mode Exit fullscreen mode

这只是创建了预先固定的日期选择器。我们目前没有任何数据可以用来更新这个字段。我们将在下一课讲述这一点。

book-edit.component.html

```
 <section>
    <mat-form-field>
      <input
        matInput
        [matDatepicker]="picker"
        placeholder="Publish Date"
      />
      <mat-datepicker-toggle
        matSuffix
        [for]="picker"
      ></mat-datepicker-toggle>
      <mat-datepicker #picker></mat-datepicker>
    </mat-form-field>
  </section> 
```

Enter fullscreen mode Exit fullscreen mode

### 选择

[![Select](img/df3914d82aeeca13aed3c860eb9e8c37.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H7FrUlg1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1548967053/ajonp-ajonp-com/11-lesson-angular-navigation-firestore/wos1r5ksaofipa6fbmhb.jpg)

*   [选择](https://material.angular.io/components/select/overview)你可以使用`<mat-form-field>`中的材料选择或原生选择。本机控件有几个性能优势...但是我真的很喜欢使用材料设计的风格。

book-edit.module.ts

```
 imports: [
    ...
       MatSelectModule, 
```

Enter fullscreen mode Exit fullscreen mode

> 在模板中打开我们的`bookConfig$`可能会更好，但是我想把它们作为单独的单元保存。

对于我们的 book 组件，我们将再次从 Firestore 的 Config Book 中获取所有年龄类别，并使用这些值进行选择。

[![Mat Select](img/06f6f2bfd348f376b2f008899285fd2c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_hAc-eJG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1548967697/ajonp-ajonp-com/11-lesson-angular-navigation-firestore/rxghw62n7jkgwdilxh80.jpg)

book-edit.component.html

```
 <section>
    <mat-form-field style="width: 100%">
      <mat-select placeholder="Age Category">
        <mat-option
          *ngFor="let ageCategory of (bookConfig$ | async)?.ageCategory"
          [value]="ageCategory"
        >
          {{ ageCategory }}
        </mat-option>
      </mat-select>
    </mat-form-field>
  </section> 
```

Enter fullscreen mode Exit fullscreen mode

### 滑块

[![Slider](img/6c21ed08f2218026cbf84e87a854ad12.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mejfwoun--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1548967734/ajonp-ajonp-com/11-lesson-angular-navigation-firestore/j0hmgry94kxytkpaws0a.jpg)

*   [滑块](https://material.angular.io/components/slider/overview) `<mat-slider>`允许通过鼠标、触摸或键盘从一个范围内选择一个值，类似于`<input type="range">`。

book-edit.module.ts

```
 imports: [
    ...
       MatSliderModule, 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们不会对这个值做任何事情，但是我想向您展示如何在创建时默认这个值。稍后，我们将把它直接与我们图书的 Firestore 价值联系起来。

book-edit.component.ts

```
 bookRating = 3; 
```

Enter fullscreen mode Exit fullscreen mode

book-edit.component.html

```
 <section>
    <h3>Rating</h3>
    <mat-slider
      min="1"
      max="5"
      step="0.5"
      value="1.5"
      [(ngModel)]="bookRating"
    ></mat-slider>
  </section> 
```

Enter fullscreen mode Exit fullscreen mode

### 单选按钮

[![Radio Button](img/f7b273b69c06d87601a485850b91d774.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AZ634w6B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1548968040/ajonp-ajonp-com/11-lesson-angular-navigation-firestore/edkb9mkpmr46tkfjthr4.jpg)

*   [单选按钮](https://material.angular.io/components/radio/overview) `<mat-radio-button>`提供与原生`<input type="radio">`相同的功能，并增强了材料设计风格和动画。

book-edit.module.ts

```
 imports: [
    ...
       MatRadioModule, 
```

Enter fullscreen mode Exit fullscreen mode

对我们来说，这将再次直接引用我们的书上的状态，我们可以从 Firestore 配置中创建这些，但我不认为我们会经常更改这些选项。如果您愿意，您可以像我们使用 select 选项一样执行相同的循环，并添加配置。在下一课中，我们将添加该配置，并展示如何进行排序验证。

book-edit-component.html

```
 <section>
    <h3>Status</h3>
    <mat-radio-group [(ngModel)]="bookStatus" fxLayout="column">
      <mat-radio-button value="Draft">Draft</mat-radio-button>
      <mat-radio-button value="Published">Published</mat-radio-button>
    </mat-radio-group>
  </section> 
```

Enter fullscreen mode Exit fullscreen mode

# 总结起来

在这里，我们已经创建了所有的角形材料表单组件，其中许多组件的数据来自 Firestore。下一步是让我们的表单更具反应性。

[![Kitchen Sink](img/efa4eddb4bfb68ed8b10fc010d6e6ff4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QK7SZh93--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/f_auto%2Cfl_lossy%2Cq_auto/v1548968315/ajonp-ajonp-com/11-lesson-angular-navigation-firestore/dwg3vmpnxdsam560rvhr.jpg)
# Angular、Akita 和 Firebase 入门

> 原文：<https://dev.to/arielgueta/getting-started-with-akita-and-firebase-3pe2>

所以你是 Firebase 的忠实粉丝，但你也想开始和 Akita 合作(反之亦然)。两者怎么一起玩？事实证明，非常好😁这是因为两者有很多共同点:它们都是基于可观察性的、健壮的、在各自领域有良好文档记录的解决方案。

在本文中，我将展示一个使用 Akita with AngularFire 管理书店库存的示例，AngularFire 是 Firebase 的官方 Angular 库。

它假设你有一些 Akita 和 Firebase 的工作知识。如果没有，请从秋田基础/ AngularFire 基础开始。

## 设置角度火

首先，我们需要安装 [AngularFire](https://github.com/angular/angularfire2) 库:

```
npm install @angular/fire 
```

并在环境文件中设置我们的`firebase`设置:

```
// environment.ts

export const environment = {
  production: false,
  firebase: {
    apiKey: 'yourkey',
    projectId: 'yourid',
  }
}; 
```

接下来，我们需要将`AngularFireModule`导入到我们的应用程序中，并调用`initializeApp`方法，传递我们之前设置的配置对象。

```
import { AngularFireModule } from '@angular/fire';
import { AngularFirestoreModule } from '@angular/fire/firestore';

@NgModule({
  declarations: [
    AppComponent,
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    AngularFireModule.initializeApp(environment.firebase),
    AngularFirestoreModule,
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

现在我们的应用程序中有了 firebase，让我们添加 Akita。

## 设置秋田

将 Akita 添加到我们的项目很容易。我们可以通过运行以下命令来使用 NG add 原理图:

```
ng add @datorama/akita 
```

上面的命令将 Akita、Akita 的开发工具和 Akita 的原理图添加到我们的项目中。下一步是创建一个商店。我们需要维护一个图书集合，所以我们搭建了一个新的实体特性:

```
ng g af books 
```

这个命令为我们生成一个书店、一个图书查询、一个图书服务和一个图书模型:

```
// book.model.ts
import { ID } from '@datorama/akita';

export interface Book {
  id: ID;
  title: string;
}

// books.store.ts
export interface BooksState extends EntityState<Book> {}

@Injectable({ providedIn: 'root' })
@StoreConfig({ name: 'books' })
export class BooksStore extends EntityStore<BooksState, Book> {

  constructor() {
    super();
  }

}

// books.query.ts
@Injectable({ providedIn: 'root' })
export class BooksQuery extends QueryEntity<BooksState, Book> {

  constructor(protected store: BooksStore) {
    super(store);
  }
} 
```

我要做的下一件事是围绕集合 [`stateChanges`](https://github.com/angular/angularfire2/blob/master/docs/firestore/collections.md#statechanges) API 创建一个可重用的抽象。`stateChanges`返回一个在集合发生变化时发出变化的可观察对象。我们可以利用它透明地更新我们的商店:

```
import { AngularFirestoreCollection } from '@angular/fire/firestore';
import { EntityStore, withTransaction } from '@datorama/akita';

export function syncCollection<T>(collection: AngularFirestoreCollection<T>, store: EntityStore<any, any>) {
  function updateStore(actions) {

    if(actions.length === 0) {
      store.setLoading(false);
      return;
    }

    for ( const action of actions ) {
      const id = action.payload.doc.id;
      const entity = action.payload.doc.data();

      switch( action.type ) {
        case 'added':
          store.add({ id, ...entity });
          break;
        case 'removed':
          store.remove(id);
          break;
        case 'modified':
          store.update(id, entity);
      }
    }
  }

  return collection.stateChanges().pipe(withTransaction(updateStore));
} 
```

`syncCollection`函数获取集合和存储，监听集合中的任何状态变化，并根据发出的动作更新存储。我们还使用了 [`withTransaction`](https://netbasal.gitbook.io/akita/general/transactions) ，因为我们希望在完成更新后分派一个动作。

现在，我们可以在我们的图书服务中使用它:

```
import { Injectable } from '@angular/core';
import { BooksStore } from './books.store';
import { AngularFirestore } from '@angular/fire/firestore';
import { syncCollection } from '../syncCollection';

@Injectable({ providedIn: 'root' })
export class BooksService {
  private collection = this.db.collection('books');

  constructor(private booksStore: BooksStore, private db: AngularFirestore) {
  }

  connect() {
    return syncCollection(this.collection, this.booksStore);
  }

  addBook(title: string) {
    this.collection.add({ title });
  }

  removeBook(id: string) {
    this.collection.doc(id).delete();
  }

  editBook(id: string) {
    this.collection.doc(id).update({ title: Math.random().toFixed(2).toString() });
  }
} 
```

我们使用 firebase API 来创建添加、编辑和删除书籍的方法。让我们在我们的图书组件中使用它们:

```
import { Component, OnDestroy, OnInit } from '@angular/core';
import { BooksQuery } from './state/books.query';
import { BooksService } from './state/books.service';
import { untilDestroyed } from 'ngx-take-until-destroy';

@Component({
  selector: 'app-books'
})
export class AppComponent implements OnInit, OnDestroy {
  books$ = this.booksQuery.selectAll();
  loading$ = this.booksQuery.selectLoading();

  constructor(private booksQuery: BooksQuery, private booksService: BooksService) {
  }

  ngOnInit() {
    this.booksService.connect().pipe(untilDestroyed(this)).subscribe();
  }

  addBook(input: HTMLInputElement) {
    this.booksService.addBook(input.value);
    input.value = '';
  }

  removeBook(id: string) {
    this.booksService.removeBook(id);
  }

  editBook(id: string) {
    this.booksService.editBook(id);
  }

  trackByFn(i, book) {
    return book.id;
  }

  ngOnDestroy() {
  }
} 
```

这是组件的模板:

```
<ng-container *ngIf="loading$ | async; else books">
  <h1>Loading...</h1>
</ng-container>

<ng-template #books>
  <input placeholder="Add Book..." #input (keyup.enter)="addBook(input)">

  <ul>
    <li *ngFor="let book of books$ | async; trackBy: trackByFn">
      {{ book.title }}
      <button (click)="editBook(book.id)">Edit</button>
      <button (click)="removeBook(book.id)">Delete</button>
    </li>
  </ul>
</ng-template> 
```

仅此而已。我们唯一需要做的就是像往常一样更新 firebase 集合，让`syncCollection`功能来处理一切。

让我们看看结果:

[![](img/b0ffa7e380415a3aadde674cd6ae1df6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GjdLehZE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qkpwnwb2fg2gp7pdhh5t.gif)
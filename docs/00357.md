# 你不需要关心退订

> 原文：<https://dev.to/thllbrg/you-don-t-need-to-care-about-unsubscribing-4j4a>

**问题:**
不退订 observables 会导致内存泄漏。取消订阅相当简单，但是很容易被忘记，并导致大量的样板代码。

**解决方案:**
通过使用 Angulars 内置`async`管道，我们不需要订阅或退订，它是框架自动为我们处理的。

## 之前/未到期:

**分量**

```
export class AppComponent implements OnInit, OnDestroy {

  recipe;
  componentDestroyed$: Subject<boolean> = new Subject();

  constructor(private recipesService: RecipesService) {  }

  ngOnInit() {
    this.recipesService.get().pipe(takeUntil(this.componentDestroyed$)).subscribe(data => this.recipe = data);
  }

  ngOnDestroy() {
    this.componentDestroyed$.next();
    this.componentDestroyed$.complete();
  }
} 
```

**模板**

```
{{ recipe }} 
```

## 后:

**分量**

```
export class AppComponent implements OnInit {

  recipeWithAsyncPipe$;

  constructor(private recipesService: RecipesService) {  }

  ngOnInit() {
    this.recipeWithAsyncPipe$ = this.recipesService.get();
  }
} 
```

**模板**

```
{{ recipe$ | async }} 
```

自己试试:
[https://stackblitz.com/edit/thllbrg-angular-fika-4](https://stackblitz.com/edit/thllbrg-angular-fika-4)

官方文件:
[https://angular.io/guide/observables-in-angular#async-pipe](https://angular.io/guide/observables-in-angular#async-pipe)
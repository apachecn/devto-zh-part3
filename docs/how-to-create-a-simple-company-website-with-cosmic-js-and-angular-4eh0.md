# 如何用 Cosmic JS 和 Angular 创建一个简单的公司网站

> 原文：<https://dev.to/i_maka/how-to-create-a-simple-company-website-with-cosmic-js-and-angular-4eh0>

**本文将假设 Angular 的一些基本知识，因此它可以专注于从 Cosmic JS 请求和显示数据的特定任务*

### **TL；博士**

看看[资源库](https://github.com/cosmicjs/angular-company-website)和[安装 app](https://cosmicjs.com/apps/angular-company-website)

这个应用程序是关于什么的？

一个典型的公司网站仅仅是一组或多或少简单导航的页面。我们希望创建一个网站，允许用户创建任意数量的页面，并决定导航的结构，而不需要等待代码发布。为此，我们将依靠 Cosmic JS 并创建结构来支持这个项目。在本例中，我们的 bucket 将具有以下对象类型:

*   页数。这是一个简单的对象，没有额外的元字段。
*   导航。它将保存页面集合。
*   预设。这是一个带有属性列表的配置对象，可以帮助我们启动应用程序。它将保存主页和主导航的值，我们将使用它作为我们站点的菜单。

**使用 API**

既然我们已经定义了数据的结构，我们需要创建我们的 Angular 应用程序(我推荐使用 Angular CLI)并创建一个服务来调用 Cosmic JS API。看看从 Cosmic JS:
请求一个页面对象有多简单

```
getPage(slug: string): Observable<Page> {
    if (!this.page$.get(slug)) {
      const url = `api.cosmicjs.com/v1/company-presentation/object/${slug}`;
      const response = this.http.get<Page>(url).pipe(
        tap(_ => console.log(`fetched page: ${slug}`)),
        map(_ => {
          return new Page(_['object']);
        }),
        shareReplay(1),
        catchError(this.handleError<Page>(`getPage: ${slug}`))
      );
      this.page$.set(slug, response);
    }
    return this.page$.get(slug);
  } 
```

*获取一个单独的页面并保存到流中，为会话保持缓存*

请注意，对于嵌套对象，Cosmic JS 不会返回完整的树，因此有时您需要连接调用来充分利用我们的服务，例如:当获取主预置时，您可能希望包含导航页面。

```
getMainPresets(includeNavigation?: boolean): Observable<Preset> {
    if (includeNavigation) {
      return this.getPreset(environment.presets).pipe(
        switchMap(response => {
          return forkJoin([of(response), this.getNavigation(response.mainNavigation._id)]);
        }),
        map(res => {
          res[0].mainNavigation = res[1];
          return res[0];
        })
      );
    } else {
      return this.getPreset(environment.presets);
    }
  } 
```

*这个调用将把 getNavigation 的结果合并到 getPreset 的结果中*

我们的 api 调用需要用一个 read 键进行认证，我们可以在每个方法中包含这一点，但是最简单的方法可能是创建一个拦截器。这将拦截所有 http 请求，所以我们检查它是否是一个 Cosmic JS 请求，并在必要的地方附加 read 键。

```
intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    if (req.url.match(/api.cosmicjs/)) {
      let params = new HttpParams({ fromString: req.params.toString() });
      if (req.method === 'GET') {
        params = params.append('read_key', environment.read_key);
      } else {
        params = params.append('write_key', environment.write_key);
      }
      req = req.clone({
        params: params
      });
    }
    return next.handle(req);
  } 
```

*截取并附加读或写参数*

**路由到页面**

在这一点上，我们的网站什么都不做，它需要做的第一件事就是知道如何加载它的第一页。我们需要为页面创建一个路由模块，并将其设置为在根上加载。一旦进入模块，在它自己的路由上，我们将为参数:slug 设置一个路由来加载页面组件。当没有指定路由时会发生什么？这将是我们最常见的场景，为此，我们设置了一条有警卫的空路线。

```
const routes: Routes = [
  {
    path: '',
    canActivate: [HomepageGuard]
  },
  {
    path: ':slug',
    component: PageComponent
  }
]; 
```

在匹配路线和加载组件之前，将执行一个守卫，所以我们将使用它从 Cosmic JS 中获取主要的预设，并将应用程序重定向到我们建立的主页。

```
canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot) {
    return this.cosmicService.getMainPresets().pipe(
      map(presets => {
        return this.router.parseUrl(`/${presets.homepage.slug}`);
      })
    );
  } 
```

*从 Angular 7.1 开始，canActivate 可以返回布尔值或 UrlTree*

**组件**

现在我们有了一个将加载页面组件的路由配置，这是使用参数列表中的 slug 加载页面的方法:

```
ngOnInit() {
    this.route.paramMap
      .pipe(
        map(paramMap => paramMap.get('slug')),
        switchMap(slug => (slug ? this.cosmicService.getPage(slug) : EMPTY))
      )
      .subscribe(page => (this.page = page));
  } 
```

*现在我们可以使用模板上的页面对象*

这样，我们就有了通过 url 导航的方法，但是我们也应该加载一个菜单，这样用户就可以看到可用的页面。为此，我们将在核心模块上添加一个菜单组件，因为它将在整个应用程序中共享。我们的菜单将由导航列表、徽标和公司名称组成；所有这些都是在预置上定义的，所以菜单会有这样一个调用:

```
ngOnInit() {
    this.cosmicService.getMainPresets(true).subscribe(presets => {
      this.logo = presets.companyLogoUrl;
      this.navigation = presets.mainNavigation;
      this.title = presets.companyName;
    });
  } 
```

*注意，我们特别要求通过将“true”作为参数添加到方法*中来包含导航

所有这些将允许你使用 Angular 和 Cosmic JS 快速创建一个网站，剩下要做的就是创建模板并扩展配置以满足你的需求。
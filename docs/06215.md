# Angular 应用程序的编译时与运行时配置

> 原文：<https://dev.to/angular/compile-time-vs-runtime-configuration-of-your-angular-app-35ml>

当你开发一个更大的应用程序时，你很有可能需要某种配置。这可以从简单地可视化应用程序的版本号到注入自定义主题等。在 Angular 中有不同种类的方法:编译时和运行时配置。让我们来看看他们两个。

[点击此处阅读整篇文章](https://juristr.com/blog/2018/01/ng-app-runtime-config/?utm_source=devto&utm_medium=crosspost)

## TL；速度三角形定位法(dead reckoning)

### 编译期配置

当使用编译时方法时，我们基本上是将配置标志添加到任何 Angular CLI 设置生成的`environment.ts`和`environment.prod.ts`文件中。你可以在`environments`文件夹中找到它们。

基于我们调用的构建命令，Angular 替换了配置文件，基本上对于生产环境，它会用`environment.prod.ts`文件覆盖`environment.ts`文件。因此，在我们的代码中，我们可以像这样简单地导入文件...

```
import { environment } from '../environment/environment';

// do something meaningful with `environment`
console.log(environment); 
```

Enter fullscreen mode Exit fullscreen mode

..用我们的配置做一些有意义的事情。我们还可以配置其他环境(除了开发和生产环境之外)。只要确保适当调整`angular.json`文件以适应这些新环境。

### 运行时配置

编译时间也意味着你需要**为每个环境**重新编译你的应用。这并不总是令人满意的，比如从开发阶段转移到生产阶段。您不希望每次都重新编译(这可能会引入新的错误)。为了实现运行时配置，我们可以利用`APP_INITIALIZER`。这是一个我们可以在`AppModule`上配置的功能，它允许我们返回一个承诺。模块**只有在承诺解决**后才会引导。

```
const appInitializerFn = () => {
  return () => {
    return new Promise((resolve, reject) => {
      ...
    });
  };
};

@NgModule({
  imports:      [ BrowserModule, FormsModule, SomeModule ],
  declarations: [ AppComponent, HelloComponent ],
  bootstrap:    [ AppComponent ],
  providers: [
    {
      provide: APP_INITIALIZER,
      useFactory: appInitializerFn,
      multi: true
    }
  ]
})
export class AppModule {...} 
```

Enter fullscreen mode Exit fullscreen mode

要了解更多关于`APP_INITIALIZER`如何工作的信息，请点击下面的链接查看完整的博客文章😃。

## 获取所有细节..

[阅读更多](https://juristr.com/blog/2018/01/ng-app-runtime-config/?utm_source=devto&utm_medium=crosspost)
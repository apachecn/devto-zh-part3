# 像建筑师一样塑造棱角(第二部分)

> 原文：<https://dev.to/steveblue/build-angular-like-an-architect-part-2-208l>

在博客系列“像建筑师一样构建 Angular”的这一部分中，我们将研究如何使用 angular-devkit 优化产品构建，并通过弄清楚如何实现环境来完善我们的定制构建。

### 重述

在[像架构师一样构建 Angular(第 1 部分)](https://dev.to/steveblue/build-angular-like-an-architect-part-1-3ph2)中，我们看了如何使用最新的架构师 API。通过使用 Architect API 和 RxJS 对构建器进行编码，我们能够使用一个新的生产构建来扩展 Angular CLI，该构建使用 Closure 编译器来优化 Angular。

我们最终得到了一个执行 RxJS 可观察值的函数，如下所示:

```
export function executeClosure(
  options: ClosureBuilderSchema,
  context: BuilderContext
): Observable<BuilderOutput> {
  return of(context).pipe(
    concatMap( results => ngc(options, context) ),
    concatMap( results => compileMain(options, context)),
    concatMap( results => closure(options, context) ),
    mapTo({ success: true }),
    catchError(error => {
      context.reportStatus('Error: ' + error);
      return [{ success: false }];
    }),
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

在本节的开始，让我们使用@angular-devkit 中的一个名为`buildOptimizer`的工具对产品包进行更多的优化。

创建一个名为 optimizeBuild 的新方法，该方法返回一个 RxJS 可观察值，并将该方法添加到`executeClosure`中的`pipe`中。

```
 return of(context).pipe(
    concatMap( results => ngc(options, context) ),
    concatMap( results => compileMain(options, context)),
    concatMap( results => optimizeBuild(options, context)),
    concatMap( results => closure(options, context) ), 
```

Enter fullscreen mode Exit fullscreen mode

在 build_tools 目录下安装`@angular-devkit/build-optimizer`。

```
npm i @angular-devkit/build-optimizer --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

像这样导入`buildOptimizer`。

```
import { buildOptimizer } from '@angular-devkit/build-optimizer'; 
```

Enter fullscreen mode Exit fullscreen mode

本质上，在 Angular 编译器运行之后，out-tsc 中的每个 component.js 文件都需要用 buildOptimizer 进行后处理。这个工具删除了不必要的装饰器，这些装饰器会使包膨胀。

该脚本的算法如下:

*   列出 out-tsc 目录中扩展名为. component.js 的所有文件
*   读取文件名数组中的每个文件
*   调用 buildOptimizer，传入每个文件的内容
*   使用 buildOptimizer 的输出将文件写入磁盘

让我们使用一个名为 glob 的方便的 npm 包来列出具有给定扩展名的所有文件。

在 build_tools 目录下安装 glob。

```
npm i glob --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

将 glob 导入 src/closure/index.ts.

```
import { glob } from 'glob'; 
```

Enter fullscreen mode Exit fullscreen mode

在`optimizeBuild`方法中，声明一个新的`const`，并将其命名为`files`。

```
const files = glob.sync(normalize('out-tsc/**/*.component.js')); 
```

Enter fullscreen mode Exit fullscreen mode

将所有匹配 glob 的文件同步格式化成一个字符串数组。在上面的例子中，`files`等于一个包含所有扩展名为`.component.js`的文件路径的字符串数组。

[![](img/484f9c4499479257991a897f0349cf9c.png)](https://i.giphy.com/media/JwySbnfwZcoko/giphy.gif)

现在我们有了一个需要用`buildOptimizer`进行后处理的文件名数组。我们的函数`optimizeBuild`需要返回一个可观察值，但是我们有一个文件名数组。

本质上，`optimizeBuild`应该在所有文件都被处理后才发出，所以我们需要将文件映射到一个 observable 数组，并使用一个名为`forkJoin`的 RxJS 方法等待所有 observable 都被处理完。构建的下一步是将应用程序与闭包编译器捆绑在一起。这项任务必须等待`optimizeBuild`完成。

```
 const optimizedFiles = files.map((file) => {
    return new Observable((observer) => {
        readFile(file, 'utf-8', (err, data) => {
        if (err) {
            observer.error(err);
        }
        writeFile(file, buildOptimizer({ content: data }).content, (error) => {
            if (error) {
                observer.error(error);
            }
            observer.next(file);
            observer.complete();
        });
    });
    });
});

return forkJoin(optimizedFiles); 
```

Enter fullscreen mode Exit fullscreen mode

用`readFile`从磁盘读取每个文件，用`buildOptimizer`对文件内容进行后处理，用`writeFile`将结果内容写入磁盘。观察者呼叫`next`和`complete`通知`forkJoin`异步动作已经执行。

如果您在运行这个优化之前查看 out-tsc 目录中的文件，那么这些文件将会包含如下装饰符:

```
AppComponent.decorators = [
{ type: Component, args: [{
            selector: 'app-root',
            templateUrl: './app.component.html',
            styleUrls: ['./app.component.css']
        },] },
]; 
```

Enter fullscreen mode Exit fullscreen mode

现在，通过运行`architect build_repo:closure_build`，用`buildOptimizer`移除装饰器。

让我们转到合并环境，这样我们就可以从默认的 Angular CLI 构建中复制该功能。

## 搬运环境

处理环境配置比前面的练习简单得多。首先我们来看问题。

在 src/环境中，默认情况下有两个文件。

*   环境. ts
*   环境产品

默认情况下，environment.prod.ts 如下所示。

```
export const environment = {
  production: true
}; 
```

Enter fullscreen mode Exit fullscreen mode

src/main.ts 在一个新搭建的项目中引用了这个配置。

```
import { environment } from './environments/environment';

if (environment.production) {
  enableProdMode();
} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，环境对象始终是从。/environments/environment，但是每个环境有不同的文件？

[![](img/59a232f4103b247480ea28ee6ff27bd9.png)](https://i.giphy.com/media/a2euXnuLIgVQA/giphy.gif)

解决办法很简单。

在 AOT 编译器运行并将 JavaScript 输出到 out-tsc 目录之后，但在应用程序捆绑之前，我们必须交换文件。

```
cp out-tsc/src/environment/environment.prod.js out-tsc/src/environment/environment.js 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码片段使用 cp Unix 命令将生产环境文件复制到默认的 environment.js。

将 environment.js 文件替换为当前环境后，应用程序被绑定，应用程序中对`environment`的所有引用都对应于正确的环境。

创建一个名为`handleEnvironment`的新函数，并将选项作为参数传入。这个函数和其他函数一样，它返回一个可观察值。

```
export function handleEnvironment(
    options:ClosureBuilderSchema,
    context: BuilderContext
  ): Observable<{}> {

} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在 schema.json.
中将`env`定义为一个选项

```
"env":  {  "type":  "string",  "description":  "Environment to build for (defaults to prod)."  } 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用相同的参数通过 Architect CLI 运行这个构建。

```
architect build_repo:closure_build --env=prod 
```

Enter fullscreen mode Exit fullscreen mode

在我们刚刚创建的方法中，我们可以引用`options`对象上的`env`参数。

```
const env = options.env ? options.env : 'prod'; 
```

Enter fullscreen mode Exit fullscreen mode

为了复制正确的环境，我们可以使用 node 中可用的工具`exec`。

```
import { exec } from 'child_process'; 
```

Enter fullscreen mode Exit fullscreen mode

允许您像在终端中一样运行 bash 命令。

node 附带的类似`exec`的函数是基于承诺的。幸运的是，RxJS 可观测性与承诺是互操作的。我们可以使用`RxJS`中打包的`of`方法将`exec`转化为可观测值。完成的代码如下。

```
export function handleEnvironment(
    options:ClosureBuilderSchema,
    context: BuilderContext
  ): Observable<{}> {

    const env = options.env ? options.env : 'prod';

    return of(exec('cp '+
                normalize('out-tsc/src/environments/environment.' + env + '.js') + '  ' +
                normalize('out-tsc/src/environments/environment.js')
             ));
} 
```

Enter fullscreen mode Exit fullscreen mode

通过对`concatMap`的另一个调用将新方法添加到`executeClosure`。在这一点上应该感觉像针和线。

```
 return of(context).pipe(
    concatMap( results => ngc(options, context) ),
    concatMap( results => compileMain(options, context)),
    concatMap( results => optimizeBuild(options, context)),
    concatMap( results => handleEnvironment(options, context)),
    concatMap( results => closure(options, context) ), 
```

Enter fullscreen mode Exit fullscreen mode

花点时间反思一下你已经成为的构建大师。生产构建的所有步骤都已就绪！

[![](img/9e3d38312b72f6168d2468257f88f59f.png)](https://i.giphy.com/media/3o7TKnvDNYADdLYZIQ/giphy.gif)
# 模拟后端的角形 Http 模拟拦截器

> 原文：<https://dev.to/sanidz/angular-http-mock-interceptor-for-mocked-backend-1h5g>

# 跳到 stackblitz 示例的结尾

# 问题:

显示 api 示例列表

```
<ul>
  <li *ngFor="let user of users"> {{user.name}}-{{user.id}}</li>
</ul> 
```

从 API "[https://jsonplaceholder.typicode.com/users](https://jsonplaceholder.typicode.com/users)"
加载用户

```
constructor(http: HttpClient){
    http.get<[User]>('https://jsonplaceholder.typicode.com/users').subscribe( res => {
        this.users = res;
    });
  } 
```

# 实现同模拟

# 为什么:

假设后端慢了 2 毫秒或不稳定，或者只是暂时不可用，或者客户端 vpn 很慢。模拟后端，用于无故障孤立角度应用程序测试。它可以用于更快的开发或 e2e 测试。

# 替代品:

Json 服务器

## 1。HttpMockRequestInterceptor

http 拦截器的用法很多(修改头、缓存等)，但我们可以用它来实现模拟的 http 调用:)
首先，我们将编写 HttpMockRequestInterceptor 类，它将捕获来自应用程序的所有 http 调用，并返回使用导入加载的 guess what- json 响应(这是 angular7 的功能-您可能需要使用 resolveJsonModule、esModuleInterop 和 allowSyntheticDefaultImports 设置为 true 来修改 tsconfig.ts)。

```
 import { Injectable, Injector } from '@angular/core';
import { HttpEvent, HttpHandler, HttpInterceptor, HttpRequest, HttpResponse } from '@angular/common/http';
import { Observable, of } from 'rxjs';

@Injectable()
export class HttpMockRequestInterceptor implements HttpInterceptor {
    constructor(private injector: Injector) {}

    intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {        
        console.log('Intercepted httpCall' + request.url);
        return next.handle(request);
    }
} 
```

## 2。模块. ts

我们在 module.ts
中注册拦截器

```
 @NgModule({
  imports:      [ ... ],
  declarations: [ .. ],
  providers: [
 {
      provide: HTTP_INTERCEPTORS,
      useClass: HttpMockRequestInterceptor,
      multi: true
      }
  ],
  bootstrap:    [ AppComponent ]
}) 
```

现在，这个问题是，这个模拟拦截器将总是拦截东西，我们不希望这样。我们将在后面处理环境中的模拟服务配置。让我们回到拦截器，返回“[https://jsonplaceholder.typicode.com/users](https://jsonplaceholder.typicode.com/users)”URL 的 json:

# 3 匹配 URL 并返回 JSON

我们将使用名为 users.json
的简单 json 文件

```
 [
 {
   "name": "John",
   "id": 666
 },
 {
   "name": "Ron",
   "id": 55
 },
 {
   "name": "Ron",
   "id": 345
 },
 {
   "name": "Ron",
   "id": 645654
 }
] 
```

让我们导入那个 json 文件，并将其作为 sucessfull 200 HttpResponse 返回。

```
 import { Injectable, Injector } from '@angular/core';
import { HttpEvent, HttpHandler, HttpInterceptor, HttpRequest, HttpResponse } from '@angular/common/http';
import { Observable, of } from 'rxjs';
import * as users from './users.json';

const urls = [
    {
        url: 'https://jsonplaceholder.typicode.com/users',
        json: users
    }
];

@Injectable()
export class HttpMockRequestInterceptor implements HttpInterceptor {
    constructor(private injector: Injector) {}

    intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
        for (const element of urls) {
            if (request.url === element.url) {
                console.log('Loaded from json : ' + request.url);
                return of(new HttpResponse({ status: 200, body: ((element.json) as any).default }));
            }
        }
        console.log('Loaded from http call :' + request.url);
        return next.handle(request);
    }
} 
```

需要注意的几件事:
。我们只匹配 url，不匹配方法。
。Import 用于加载 json 文件，而不是 require 或 http get。
。这里有简单的控制台日志记录，因为匹配的 http 调用在 Brwoser Networks 选项卡中不可见，它们被截获。

## 4 用 start:mock 脚本加载 mockinterceptor

在名为 mock 的 environment.ts 中添加我们稍后将在 app.module.ts 中需要的附加属性，并创建名为 environment.mock.ts:
的附加文件

```
export const environment = {
  production: true,
  mock: true
}; 
```

现在我们需要在 angular.ts 文件中的两个地方添加模拟配置:
demo/architect/build/configuration s

```
"mock": {
              "fileReplacements": [
                {
                  "replace": "src/environments/environment.ts",
                  "with": "src/environments/environment.mock.ts"
                }
              ]
            } 
```

还有内线发球:
示范/建筑师/发球

```
"mock": {
              "browserTarget": "demo:build:mock"
            } 
```

可爱的 jubbly，现在我们可以基于脚本运行模拟或真实后端(本地主机或服务器托管)的应用程序。

package.json 新脚本:

```
 "start:mock": "ng serve --configuration=mock", 
```

最后一步:基于模拟标志有条件地加载 app.module.ts 中的拦截器。

```
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';

import { AppComponent } from './app.component';
import { HelloComponent } from './hello.component';
import { HttpClient, HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';

import { environment } from '../environments/environment';

import { HttpRequestInterceptor } from './interceptor';
import { HttpMockRequestInterceptor } from './interceptor.mock';

export const isMock = environment.mock;

@NgModule({
  imports:      [ BrowserModule, FormsModule, HttpClientModule ],
  declarations: [ AppComponent, HelloComponent ],
  providers: [
 {
      provide: HTTP_INTERCEPTORS,
      useClass: isMock ? HttpMockRequestInterceptor : HttpRequestInterceptor,
      multi: true
      }
  ],
  bootstrap:    [ AppComponent ]
})
export class AppModule { } 
```

运行“npm run start:mock”脚本来模拟您的后端。
享受。

这也是 Stackblitz 演示...

[https://stackblitz.com/edit/angular-mock-http-interceptor?embed=1&&](https://stackblitz.com/edit/angular-mock-http-interceptor?embed=1&&)
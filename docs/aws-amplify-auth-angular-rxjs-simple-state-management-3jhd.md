# AWS Amplify Auth & Angular RxJS 简单状态管理

> 原文：<https://dev.to/beavearony/aws-amplify-auth-angular-rxjs-simple-state-management-3jhd>

最近对 [aws-amplify hub](https://aws-amplify.github.io/docs/js/hub#working-with-the-api) 的更新使得在不使用 [aws-amplify-angular](https://github.com/aws-amplify/amplify-js/tree/master/packages/aws-amplify-angular) 包的情况下监听(AWS Cognito) auth 状态变化变得更加容易。如果我们没有使用这个包的预构建 UI 组件，我们可以通过导入我们需要的模块来节省几百 KB。

但是，即使我们使用 [aws-amplify-angular](https://github.com/aws-amplify/amplify-js/tree/master/packages/aws-amplify-angular) 包，我们也可以看看如何在没有任何状态管理库的情况下，以一种非常有角度的方式管理我们的认证和用户状态，例如 [Redux](https://github.com/reduxjs/redux) 、 [NGRX](https://ngrx.io) 、 [apollo-link-state](https://www.apollographql.com/docs/link/links/state) 、 [MobX](https://github.com/mobxjs/mobx) 、 [Akita](https://github.com/datorama/akita) 或 [NGXS](https://ngxs.gitbook.io/ngxs)

Angular 的一个特点是他们的依赖注入系统。我们可以定义 tree-shake 可注入式，用于我们的任何组件或其他可注入式。这是在一个或多个服务中存储功能模块状态的好地方。

如果我们的应用程序变得越来越复杂，可能有一天，我们会觉得需要使用一个单独的存储来管理我们的状态。我们可能对使用像 [Redux DevTools](https://github.com/reduxjs/redux-devtools) 这样的工具来改善开发人员的人机工程学感兴趣，或者从我们选择的库的明确定义的模式中获益。

但是在这个时候到来之前，或者如果我们决定，我们想要一个以上的商店，这可能是在我们的 Angular 应用程序中使用的一个好模式。

## RxJS 行为主体

Angular 对 [RxJS](https://angular.io/guide/rx-library) 有一个对等依赖，并在他们的一些包中使用它。学习 Angular 通常也需要学习 RxJS。RxJS 提供了一个主体，这个主体是一个可观察物和一个观察者。BehaviorSubject 还存储了最后一个状态，没有初始状态就不能创建。这对于我们的状态管理需求来说是非常理想的，因为订阅者在订阅了可观察对象之后会立即获得最后发出的值。拥有一个初始状态对于许多状态管理库来说是很常见的，它消除了检查 null 或 undefined 的需要。

如果我们想保护国家不受外部变化的影响，我们有一种方法使它成为私有的，只向公众公开可观察的事物。

## 授权服务

要使用 Angular CLI 创建服务，请键入

```
$ ng g s auth 
```

这将在我们的应用程序中生成一个服务`auth.service.ts`。

我们需要在某个地方导入我们的服务，所以 bundler 包含了它。由于这是一个应该一直运行的应用范围的服务，我们可以简单地在`app.module.ts` :
中注入服务

```
@NgModule({
  /*...*/
})
export class AppModule {
  constructor(_auth: AuthService) {
    console.log('starting AppModule');
  }
} 
```

这将在应用程序启动时将 AuthService 实例化为单例。

> 在真实的应用程序中，我们可能希望将服务放在一个单独的模块中(即`auth.module.ts`)并将其注入其构造函数中。在`app.module.ts`中导入那个模块会有同样的效果。

这是我们对`auth.service.ts` :
的实现

```
import { Injectable } from '@angular/core';
import Auth from '@aws-amplify/auth';
import { Hub } from '@aws-amplify/core';
import { BehaviorSubject } from 'rxjs';
import { map } from 'rxjs/operators';

export interface AuthState {
  isLoggedIn: boolean;
  username: string | null;
  id: string | null;
  email: string | null;
}

const initialAuthState = {
  isLoggedIn: false,
  username: null,
  id: null,
  email: null
};

@Injectable({
  providedIn: 'root'
})
export class AuthService {
  private readonly _authState = new BehaviorSubject<AuthState>(
    initialAuthState
  );

  /** AuthState as an Observable */
  readonly auth$ = this._authState.asObservable();

  /** Observe the isLoggedIn slice of the auth state */
  readonly isLoggedIn$ = this.auth$.pipe(map(state => state.isLoggedIn));

  constructor() {
    // Get the user on creation of this service
    Auth.currentAuthenticatedUser().then(
      (user: any) => this.setUser(user),
      _err => this._authState.next(initialAuthState)
    );

    // Use Hub channel 'auth' to get notified on changes
    Hub.listen('auth', ({ payload: { event, data, message } }) => {
      if (event === 'signIn') {
        // On 'signIn' event, the data is a CognitoUser object
        this.setUser(data);
      } else {
        this._authState.next(initialAuthState);
      }
    });
  }

  private setUser(user: any) {
    if (!user) {
      return;
    }

    const {
      attributes: { sub: id, email },
      username
    } = user;

    this._authState.next({ isLoggedIn: true, id, username, email });
  }
} 
```

1.  我们在`AuthState`中定义了状态的公共接口，并创建了一个初始对象`initialAuthState`。
2.  我们决定使用默认的 providedIn 属性来使这个服务树可摇动(可选)
3.  我们定义并初始化一个私有行为主体，并将其公开为一个可观察对象
4.  我们编写一个私有函数`setUser(user: any)`来获取一个认知用户对象，分解它并从中创建下一个状态
5.  我们创建了一种可选的方式来公开状态的一部分，即我们的状态对象的`isLoggedIn`布尔值。
6.  在构造函数中，我们通过在这个可注入的实例化上写`Auth.currentAuthenticatedUser()`并发出用户对象或`initialAuthState`来请求当前用户
7.  我们开始监听名为“auth”的集线器通道，并根据用户是否登录来构造下一个状态

就是这样！我们可以在注入该服务的任何地方订阅状态更改。

## 使用我们的授权状态服务

为了访问我们的状态，我们注入服务并订阅任何公开的可观察对象。

```
// ... imports
@Component({
  selector: 'my-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  isLoggedIn = false;
  user: { id: string; username: string; email: string };

  constructor(private authService: AuthService) {}

  ngOnInit(): void {
    this.authService.isLoggedIn$.subscribe(
      isLoggedIn => (this.isLoggedIn = isLoggedIn)
    );

    this.authService.auth$.subscribe(({ id, username, email }) => {
      this.user = { id, username, email };
    });
  }
} 
```

我希望这有助于了解如何以模块化和简单的方式处理 Angular 中的状态，方法是将它移动到服务中，并在 RxJS BehaviorSubject 的帮助下通知任何感兴趣的人。
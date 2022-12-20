# 测试 NgRx 效果

> 原文：<https://dev.to/fallenstedt/testing-ngrx-effects-2mcl>

[![alex fallenstedt photography](img/f4a1d400721432520f9a4c2ad11050e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QPYrBq8Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6542w0qedk4bb6xakmji.png)

你走在街上喝着咖啡，问自己:“我如何测试我的 [ngrx@7.4.0](https://ngrx.io/) 效果？”。如果你问这个问题是因为你对 jasmine-marbles 的开发者体验不满意，那么这个可能会帮助你。

# 基本效果

```
@Injectable()
export class RouterHistoryEffects {

 @Effect()
 addRouteToHistory = this.actions.pipe(
   ofType(ROUTER_NAVIGATED),
   map((action: RouterNavigatedAction) => action.payload.routerState.url),
   switchMap((url: string) => {
     return of(new fromRouterHistoryActions.AddRouteToHistory(url));
   })
 );

 constructor(private actions: Actions<RouterHistoryActions>) { }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个从某个地方发出的动作。它应该返回一个新的动作。当这被 ngrx 使用时，`addRouteToHistory`订阅动作流，并可以发布到您的动作流。[更多阅读在此](https://medium.com/@tanya/understanding-ngrx-effects-and-the-action-stream-1a74996a0c1c)

知道了这一点，我们的测试应该执行相同的场景。我们应该订阅`addRouteToHistory`并收听事件的发生。

```
import { TestBed, async } from '@angular/core/testing';
import { provideMockActions } from '@ngrx/effects/testing';
import { ReplaySubject } from 'rxjs';

import { RouterHistoryEffects } from './router-history.effects';
import { AddRouteToHistory } from './router-history.actions';
import { ROUTER_NAVIGATED } from '@ngrx/router-store';

describe('RouterHistoryEffects', () => {
 let actions: ReplaySubject<any>;
 let effects: RouterHistoryEffects;

 beforeEach(() => {
   TestBed.configureTestingModule({
     providers: [
       RouterHistoryEffects,
       provideMockActions(() => actions)
     ]
   });

   effects = TestBed.get(RouterHistoryEffects);
 });

 it('should be created', async() => {
   expect(effects).toBeTruthy();
 });

 it('#addRouteToHistory should dispatch fromRouterHistoryActions.AddRouteToHistory with a url', async(async() => {
   actions = new ReplaySubject(1);
   const routerNavigatedAction = {
     type: ROUTER_NAVIGATED,
     payload: {
       routerState: {
         url: '/member'
       }
     }
   };
   actions.next(routerNavigatedAction);
   effects.addRouteToHistory.subscribe((result: AddRouteToHistory) => {
     expect(result.payload).toEqual('/member');
   });
 }));
}); 
```

Enter fullscreen mode Exit fullscreen mode

除了`provideMockActions`，我们有一个创建测试模块的标准操作程序。这个函数([，看起来像这个](https://github.com/ngrx/platform/blob/master/modules/effects/testing/src/testing.ts))接受一个可观测值，或者一个返回可观测值的函数。这里，我们正在创建一个生成 [ReplaySubject](https://www.learnrxjs.io/subjects/replaysubject.html) 的工厂。我们可以发布到 ReplaySubject 流，并收听它。这正是我们的效果所做的:

```
…
 @Effect()
 addRouteToHistory = this.actions.pipe( // do stuff then return an observable )
… 
```

Enter fullscreen mode Exit fullscreen mode

在我们的测试中，我们可以为我们的效果`addRouteToHistory`创建一个订阅，并重放一个被“分派”的动作。

```
 actions = new ReplaySubject(1);
   const routerNavigatedAction = {
     type: ROUTER_NAVIGATED,
     payload: {
       routerState: {
         url: '/member'
       }
     }
   };
   actions.next(routerNavigatedAction);
   effects.addRouteToHistory.subscribe((result: AddRouteToHistory) => {
     expect(result.payload).toEqual('/member');
   }); 
```

Enter fullscreen mode Exit fullscreen mode

希望这能帮助你。根据您的存储是否从您的存储获得状态片段，您可能还需要创建一个模拟存储！我就不赘述细节了，但这与提供模拟动作非常相似:

```
//use provideMockStore to provide an instance of MockStore: 
//https://ngrx.io/api/store/testing/MockStore

// configure testing module in before each

import { provideMockStore, MockStore } from '@ngrx/store/testing';
import { provideMockActions } from '@ngrx/effects/testing';

//...
    TestBed.configureTestingModule({
      providers: [
        YourEffect
        provideMockStore({ initialState }),
        provideMockActions(() => actions)
      ]
   })

    mockStore = TestBed.get(Store);
//...

 it('redirect to privacy policy page', async( () => {
// set testing state
   mockStore.setState({ [ROUTER_HISTORY_FEATURE_KEY]: { previousRoutes: [] } });

// dispatch your action
actions = new ReplaySubject(1);
   actions.next({
     type: ROUTER_REQUEST,
     payload: {
       routerState: {
         url: `/${NavigationRoutes.PrivacyPolicy}`
       },
     }
   });
// listen for stream to emit.
   sub = effects.redirectToHome.subscribe((data) => {
    expect(navigationServiceSpy.navigateToPrivacyPolicy).toHaveBeenCalled();
    expect(data).toBeTruthy(); // or whatever you’re testing
   });

 })); 
```

Enter fullscreen mode Exit fullscreen mode
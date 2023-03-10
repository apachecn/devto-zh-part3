# Angular:如何使用 NgRx v7.4 中引入的动作创建器

> 原文：<https://dev.to/lacolaco/angular-how-to-use-action-creator-introduced-in-ngrx-v7-4-35d4>

本文解释了 NgRx v7.4 中引入的 **Action Creator** 特性以及使用它的实现模式。
Action Creator 尚未包含在 [ngrx.io](https://ngrx.io) 文档中，但以后添加后请参考。

## 动作的定义

让我们回顾一下在实现一个简单的计数器时如何编写 NgRx。
这次计数器定义`Increment`接收并加一个任意数，定义`Reset`重置计数器为动作。

在前面的动作定义中，通常定义动作类型的枚举、拥有它的每个动作类以及该类类型的联合类型。
例如，如果你用动作`Increment`和`Reset`定义`counter.actions.ts`，看起来如下。
`Increment`将计数增加给定的数值，`Reset`是将计数归零的动作。

```
// counter.actions.ts
import {Action} from '@ngrx/store';

export enum ActionTypes {
  Increment = '[Counter] Increment',
  Reset = '[Counter] Reset',
}

export class Increment implements Action {
  readonly type = ActionTypes.Increment;

  constructor (public payload: number) {}
}

export class Reset implements Action {
  readonly type = ActionTypes.Reset;
}

export type ActionsUnion = Increment | Reset; 
```

Enter fullscreen mode Exit fullscreen mode

该文件由动作创建者重写如下:

```
// counter.actions.ts
import {createAction, union} from '@ngrx/store';

export const increment = createAction(
  '[Counter] Increment',
  (payload: number) => ({payload})
);

export const reset = createAction(
  '[Counter] Reset'
);

const actions = union({
  increment,
  reset,
});

export type ActionsUnion = typeof actions; 
```

Enter fullscreen mode Exit fullscreen mode

### `createAction`功能

首先，我们将讨论取代类定义的`createAction`函数。
该函数返回一个**动作创建者**。动作创建器是一个返回动作对象的函数。
换句话说，调度动作从新类的实例变成了函数的返回值。

```
import * as Actions from './actions';

// instance of action class
store.dispatch(new Actions.Increment(1));

// Action Creator
// function returns Action
store.dispatch(Actions.increment(1)); 
```

Enter fullscreen mode Exit fullscreen mode

接受参数的动作将函数传递给`createAction`函数的第二个参数。
这个函数接受一个参数并返回一个部分动作对象。
这与传统 action 类中的构造函数和类字段定义相同。

我们再来看一下`increment`动作。
第二个参数是一个接受数值作为`payload`参数的函数，返回值是一个具有`payload`属性的对象。
这个函数的返回值与第一个参数创建的 action 对象合并，最终会创建 action 对象`{type: '[Counter] Increment', payload}`。

```
// Create an action
const action = Actions.increment(1);

// action object has `type`
console.log(action.type); // => '[Counter] Increment'
// The object returned by the second argument is merged
console.log(action.payload); // => 1 
```

Enter fullscreen mode Exit fullscreen mode

顺便说一下，不再需要 ActionTypes 枚举。您可以在 Reducer 的后续章节中找到更多相关信息。

### `union`功能

`ActionsUnion`型，是一系列动作类型的复合，在减压器、效果等几个地方都需要。
常规的 action 类可以按原样处理类类型的 union 类型，但是在函数的情况下，需要结合函数的返回类型。
辅助它的是 NgRx 的`union`功能。

将所有动作创建者传递给`union`函数，并声明其返回值**而不导出**。
你不想出口的原因是你只想要那种类型。在出口和对外提供的地方没有用处。
一旦你声明了`actions`变量，使用`typeof`将其类型导出为`Union`。

```
// do not export return value
const actions = union({
  increment,
  reset,
});

// export only type
export type ActionsUnion = typeof actions; 
```

Enter fullscreen mode Exit fullscreen mode

## 创建减速器

定义了动作创建者之后，让我们来做 Reducer 对应。
最初使用 action 类和 Enum 时，是下面这个 Reducer。
传递给参数的动作类型是`ActionsUnion`类型，它描述了一个比较`action.type`和`ActionTypes`的枚举字符串的 switch 语句。

```
import {ActionsUnion, ActionTypes} from './actions';
import {State, initialState} from './state';

export function reducer (state = initialState, action: ActionsUnion): State {
  switch (action.type) {
    case ActionTypes.Increment: {
      return {
        ... state,
        count: state.count + action.payload,
      };
    }
    case ActionTypes.Reset: {
      return {
        ... state,
        count: 0,
      };
    }
    default: {
      return state;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是在这个 Reducer 中反映之前动作定义变化的结果。
只有 case 语句发生了变化。
case 语句中指定的动作类型已更改为动作创建者拥有的`type`属性。
这样，因为可以直接从动作创建者处获取，所以不需要在动作定义端的 Enum 中单独获取。

```
import {ActionsUnion, increment, reset} from './actions';
import {State, initialState} from './state';

export function reducer (state = initialState, action: ActionsUnion): State {
  switch (action.type) {
    case increment.type: {
      return {
        ... state,
        count: state.count + action.payload,
      };
    }
    case reset.type: {
      return {
        ... state,
        count: 0,
      };
    }
    default: {
      return state;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 创造效果

使用 NgRx Effects 定义每次添加和重置计数器时输出日志的副作用。
传统动作定义如下:

```
import {Injectable} from '@angular/core';
import {Effect, Actions, ofType} from '@ngrx/effects';
import {tap} from 'rxjs/operators';

import {ActionsUnion, ActionTypes} from './actions';

@Injectable()
export class CounterEffects {

  constructor (private actions$: Actions<ActionsUnion>) {}

  @Effect({dispatch: false})
  logger$ = this.actions$.pipe(
    ofType(ActionTypes.Increment, ActionTypes.Reset),
    tap(action => {
      console.log(action);
    }),
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

与 Reducer 一样，这只影响动作类型的一部分。

```
import { Injectable } from '@angular/core';
import { Effect, Actions, ofType } from '@ngrx/effects';
import { tap } from 'rxjs/operators';

import { ActionsUnion, increment, reset } from './actions';

@Injectable()
export class CounterEffects {

  constructor(private actions$: Actions<ActionsUnion>) { }

  @Effect({ dispatch: false })
  logger$ = this.actions$.pipe(
    ofType(increment.type, reset.type),
    tap(action => {
      console.log(action);
    }),
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

## 调度动作

最后一部分是派遣行动。在传统的动作类中，类实例的创建和分派如下。

```
import * as CounterActions from './state/counter/actions';

@Component({
  selector: 'my-app',
  template: `
     <div>{{ count$ | async }}</div>
     <button (click)="incrementOne()">+1</button>
     <button (click)="reset()">Reset</button>
  `,
})
export class AppComponent {

  count$ = this.store.pipe(
    select(state => state.counter.count),
  );

  constructor(private store: Store<AppState>) { }

  incrementOne() {
    this.store.dispatch(new CounterActions.Increment(1));
  }

  reset() {
    this.store.dispatch(new CounterActions.Reset());
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如上所述，这将更改为调度调用动作创建者函数的返回值。

```
import * as CounterActions from './state/counter/actions';

@Component({
  selector: 'my-app',
  template: `
     <div>{{ count$ | async }}</div>
     <button (click)="incrementOne()">+1</button>
     <button (click)="reset()">Reset</button>
  `,
})
export class AppComponent {

  count$ = this.store.pipe(
    select(state => state.counter.count),
  );

  constructor(private store: Store<AppState>) { }

  incrementOne() {
    this.store.dispatch(CounterActions.increment(1));
  }

  reset() {
    this.store.dispatch(CounterActions.reset());
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这就完成了所有替换。

## 动作创建者的好处

类中定义的动作是不方便的，在实例化之前不能访问`type`,并且大量的代码必须正式编写。

在 Action Creator 中，可以把函数写成函数，这样浪费的代码就大大减少了。功能和可测试性与以前一样，没有特别的缺点。

一旦您将项目的 NgRx 更新到 7.4 版，基本上您应该继续用 Action Creator 替换它。

## 总结

*   已经引入了`createAction`函数来创建动作创建器，它将动作定义为函数而不是类
*   不再需要 ActionType 枚举
*   对减速器、效果和调度端的影响非常小

查看本文中涉及的计数器应用程序实际上是如何工作的。

[https://stackblitz.com/edit/angular-pj4f4p?file = src % 2 fapp % 2 fapp . component . ts](https://stackblitz.com/edit/angular-pj4f4p?file=src%2Fapp%2Fapp.component.ts)
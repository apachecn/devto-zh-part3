# 奶奶烹饪 redux 的食谱

> 原文：<https://dev.to/phenax/grandma-s-recipes-for-cooking-redux-4chb>

欢迎来到我奶奶的厨房。我是你的主人，孙子。我们开始做饭吧，好吗？

声明:这篇文章中引用的东西不是我的观点，它们是事实。如果你不同意，继续给这个帖子 50 个耳光(和拍手的按钮是一样的。只要更积极地点击它，它就会被标记为一巴掌)。建议读者自行判断。

我们遭受编写样板代码和混乱行为的折磨已经太久了。不再是了。这里有一些食谱，让你按照我奶奶希望的方式烹饪 redux。

我们今天要用以下食材烹饪-

*   [Redux Saga](https://github.com/redux-saga/redux-saga)
*   [Redux Utils](https://github.com/phenax/redux-utils)
*   [鳄鱼](https://github.com/evilsoft/crocks)
*   [重新选择](https://github.com/reduxjs/reselect)
*   [拉姆达](https://github.com/ramda/ramda)(可选。根据口味添加)

* * *

## 麻辣动作名称/类型

您不希望在创建动作名称/类型的方式上有任何改变，但是为什么不呢？去检查你的动作类型文件。你会发现这三种状态有一个循环模式。`ACTION_PENDING`、`ACTION_SUCCESS`、`ACTION_FAILURE`(没有失败是因为`PENDING`、`SUCCESS`和`FAILURE`的字符数相同，我奶奶喜欢一致性)。同时，我们也可以从我祖母时代的 REST 体系结构中学到一些东西，并把资源/动作模式带到这里。

`actionTypes`函数使用`@resource/ACTION/STATE`约定。

```
import { actionTypes } from '@phenax/redux-utils';

const types = actionTypes({
  DISHES: {
    LIST: ['PENDING', 'SUCCESS', 'FAILURE'],
    ADD: ['PENDING', 'SUCCESS', 'FAILURE'],
  },
  SESSION: {
    INIT: ['PENDING', 'SUCCESS', 'FAILURE'],
  },
});

types.DISHES.ADD.SUCCESS === '@dishes/ADD/SUCCESS'; // true

// Your actions will then look something like
dispatch({
  type: types.DISHES.ADD._,
  payload: {
    name: 'Chicken that tastes like paneer’,
    ingredients: {
      chicken: { count: 1, unit: 'bird' },
      salt: { toTaste: true },
      pepper: { count: 2, unit: 'shakes' },
      milk: { count: 3, unit: 'seconds of pouring' },
    },
  },
});

// The underescore (`._`) is the dispatch that gets picked up by the sagas. Its a default dispatch i.e. stateless dispatch. 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 减速沙拉

三态模式在减速器中也经常出现。我奶奶一直告诉我“当我翻阅冗长的转换语句时，我的背会痛”。如果你还没有猜到，我们现在也要改变一下。我们必须照顾老人。

```
import { createPartialReducer, mergeReducers } from '@phenax/redux-utils';

const initialState = { loading: false, readyForServing: true, dishes: [], error: '' };

const getLoadingState = state => ({ readyForServing }) => ({
  ...state,
  readyForServing,
  loading: true,
});

const addDishReducer = createPartialReducer(types.DISHES.ADD, (state = initialState, action) => ({
  PENDING: getLoadingState(state),
  SUCCESS: newDish => ({
    ...state,
    readyForServing: true,
    loading: true,
    dishes: [...state.dishes, newDish],
  }),
  FAILURE: e => ({
    ...state,
    readyForServing: false,
    loading: true,
    error: e.message,
  }),
}));

// Your regular reducer can be merged with partials
const regularOldReducer = (state = initialState, action) => {
  switch(action.type) {
    case types.SOMEOTHER.ACTION.PENDING:
      return {
        ...state,
        loading: true,
      };
    case types.SOMEOTHER.ACTION.SUCCESS:
      return {
        ...state,
        loading: false,
        dishes: [],
      };
    case types.SOMEOTHER.ACTION.FAILURE:
      return {
        ...state,
        loading: false,
        dishes: [],
        error: e.message,
      };
  }
};

export default mergeReducers(addDishReducer, regularOldReducer); 
```

Enter fullscreen mode Exit fullscreen mode

partial reducers 允许您根据资源/动作/状态约定将 reducer 分成更小的函数。你也可以用它来展示你的 rad-point-free 特技镜头。

* * *

## 鸭子许诺

这道菜的名字有三种不同的解释。线索，不是错别字(尽管完全吻合)。另一个线索，我们不是在煮鸭子。你是正确的。避开那些承诺，跟着克罗克的`Async`走。(不要与`async/await`混淆，后者只是 C#社区伪装成 w3c 草案作者对我们进行的恶作剧)。下面是如何使用 fetch(或任何其他基于 promise 的 api)并把它变成一个可组合的、可取消的、懒惰的异步任务。奶奶不赞成懒惰，但我们还是会用它，因为天杀的，这是我的烹饪节目！奶奶，滚出我的房间！

对于您来说，这是一个简单的网络超时抓取示例。fetchJson 只是 fetch api 的异步包装器。

```
import Async from 'crocks/Async';
import { prop, filter, map, either, complement } from 'ramda';
import { fetchJson } from '@phenax/redux-utils/async';

// Some other api call that happens after we get the data from the user
// fetchChefInfo :: a -> Async a
const fetchChefInfo = data => Async((rej, res) => setTimeout(() => res(data), 700));

// fetchServableDishes :: Object * -> Async [User]
const fetchServableDishes = params => fetchJson('/dishes', params)
  .race(Async.rejectAfter(2000, new Error('Request Timeout')))
  .map(prop('dishes'))
  .map(compose(
    filter,
    complement,
    either(
      prop('isReady'),
      prop('isBurnt'),
    ),
  ));

let task = fetchServableDishes();

// The request has not been sent yet (it's lazy like crazy! Thank you. Thank you.). You can keep composing and chaining stuff
task = task
  .map(map(decorateDish))
  .chain(fetchChefInfo);

// And when you finally decide that you want to make the api call, you can fork it.
// Nothing is executed till you call .fork
task.fork(
  e => console.error(e),
  activeUsers => {
    // Do stuff
  },
); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 烧烤传奇

Redux saga 是写动作的最好方式。这是事实，不是观点。不用查了。不会改变任何东西，但我们可以添加一些成分来增加它的味道。我们也将使用`Async`而不是`Promise`,因为我奶奶认为你不应该做出你不能遵守/管理的承诺。

```
import { put } from 'redux-saga/effects';
import { callAsync, putResponse } from '@phenax/redux-utils/saga';

export function* add({ payload }) {
  yield put({ type: types.DISHES.ADD.PENDING, payload: {} });
  const response = yield callAsync(saveNewDish, payload);
  yield putResponse(types.DISHES.ADD, response);
};

export function* list({ payload }) {
  yield put({ type: types.DISHES.LIST.PENDING, payload: {} });
  const response = yield callAsync(fetchServableDishes);
  yield putResponse(types.DISHES.LIST, response);
};

export default function* root() {
  yield all([takeLatest(types.DISHES.ADD._, add)]);
  yield all([takeLatest(types.DISHES.LIST._, list)]);
}; 
```

Enter fullscreen mode Exit fullscreen mode

在这里，

```
yield putResponse(types.DISHES.LIST, response);
// is just a shorthand for
yield put(response.cata({
  Success: data => ({ type: types.DISHES.LIST.SUCCESS, payload: data }),
  Failure: error => ({ type: types.DISHES.LIST.FAILURE, payload: error }),
})); 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 选择器面条

选择器允许你从状态中获取数据并记忆操作。我们将为此使用 reselect，我们还将根据口味在其中撒一些 ramda。你也可以使用 lodash，但是你为什么要这样做呢？拉姆达之于洛达什，就像智能手表之于铁罐电话。

奶奶:“在我那个时代，下划线非常流行。就像是…”。

是的，奶奶，我们明白，你老了。现在闭嘴，让我给这些人展示一些选择动作。

```
import { createSelector } from "reselect";
import { pathOr, prop, __, map } from 'ramda';

// selectBurntDishes :: { dishes :: { dishIds :: [String], items :: Object Profile } } -> [String]
export const selectBurntDishes = createSelector(
  pathOr({}, ['dishes', 'items']),
  pathOr([], ['dishes', 'dishIds']),
  (items, dishIds) => dishIds.filter(compose(
    prop('isBurnt'),
    map(prop(__, items)),
  )),
); 
```

Enter fullscreen mode Exit fullscreen mode

现在你可以在任何地方使用它…

在组件的 mapStateToProps 中，

```
const mapStateToProps = state => ({
  burntDishes: selectBurntDishes(state),
});

export default connect(mapStateToProps)(ListBurntDishes); 
```

Enter fullscreen mode Exit fullscreen mode

在你的传奇中，

```
function* mySaga() {
  const burntDishes = yield select(selectBurntDishes);
  // Do stuff with those ids
} 
```

Enter fullscreen mode Exit fullscreen mode

那看起来不漂亮吗？如果你是一个新手，你可能会觉得你刚刚看了一部没有字幕的法国电影，但是相信我，这是值得的。

* * *

## 结论

旧的思想和观点是新思想和观点的来源。奶奶的食谱很棒，它们来自经验，但这并不意味着我们必须一直做同样的东西十亿次。小型(有时是大型)迁移是一件好事。

所以我们总结一下。我们今天学了什么？

*   使用奶奶最喜欢的`@resource/ACTION/STATE`惯例作为你的动作类型
*   老年人讨厌长篇大论
*   鸭子承诺因为`Async`摇滚！
*   传奇是最棒的！！
*   为了更好的明天，记住那些选择器

这些都是我在个人项目中遇到的小而恼人的不便的解决方案。我在 [@phenax/redux-utils](https://github.com/phenax/redux-utils) 集合所有的小帮手。该项目尚未完成，所以图书馆将获得更多的更新。欢迎公关！！

希望这些想法能帮助你写出一些有味道的代码。
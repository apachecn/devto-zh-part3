# 快速开发可维护的 React 应用程序

> 原文：<https://dev.to/mpourismaiel/faster-development-of-a-maintainable-react-application-25hl>

开发应用程序真的很麻烦。在更快的开发、可维护性、性能和前端 web 开发的规模之间，几乎总是一个两难的选择。把每件事都做好并不容易，当然，从来就没有一种正确的方法，有时甚至没有。

自从我回归自由职业以来，我一直在思考这个问题。项目按时交付(甚至比截止日期更快)以及代码和结果都处于良好状态是非常重要的。但是有时会有一些障碍阻止你写出你习惯的解决方案。

例如，如果将 GraphQL 用于 API 或者甚至遵循 REST，事情会简单得多，但是由于后端是匆忙开发的，所以您没有可预测的 API。有时没有设计语言，有奇怪的流程或许多其他因素，这意味着你必须投入额外的时间来弥补它们。

像 Create React App 这样的项目让项目的启动变得更加容易。你可以使用 React Router，Redux，Redux Saga 来管理项目的状态和路由，使用 Bootstrap 或其他一些 UI 库来进行 UI 开发，但除此之外，就没有其他什么了。

为了解决这些问题，我认为如果你花一些时间，在项目开始时写一些代码，将会消除一些进一步的代码重复，这真的很好。

例如，处理 API 是我通常能够用一个(可怕的)传奇来处理的事情。

```
import { Action } from 'redux';

export interface IAction extends Action {
  payload?: {
    base_type?: any;
    data?: any;
    error?: any;
    url?: any;
    callback?: {
      onSuccess?: any;
      onFailure?: any;
    };
    [extraProps: string]: any;
  };
  meta?: any;
}

// Action type presets. These are used to determine if an action is created to make an API call (or the state of the API call) or not.
export const baseType = (type: string): string => base + type;
export const stripBase = (type: string): string => type.indexOf(base) === 0 ? type.replace(`${base}/`, '') : type;

export const requestType = (type: string): string => baseType(type + '+REQUEST');
export const successType = (type: string): string => baseType(type + '+SUCCESS');
export const failureType = (type: string): string => baseType(type + '+FAILURE');

export const postType = (type: string): string => baseType(post + type);
export const putType = (type: string): string => baseType(put + type);
export const getType = (type: string): string => baseType(get + type);
export const deleteType = (type: string): string => baseType(deleteT + type);

function makeRequestWorker(method: 'get' | 'post' | 'put' | 'delete') {
  return function*(action: IAction) {
    const type = stripBase(action.type);
    if (!action.payload) {
      throw Error('payload is undefined');
    }

    try {
      // Create a REQUEST action. Can be used to create loading states.
      yield put({ type: requestType(type), meta: action.meta });

      // Fetch data to the server.
      const { data } = yield call(
        axios[method],
        action.payload.url,
        action.payload.data
      );
      yield put({ type: successType(type), payload: data, meta: action.meta });
      if (action.payload.callback && action.payload.callback.onSuccess) {
        yield call(action.payload.callback.onSuccess);
      }
    } catch (err) {
      yield put({
        type: failureType(type),
        payload: { error: err.message },
        meta: action.meta
      });
      if (action.payload.callback && action.payload.callback.onFailure) {
        yield call(action.payload.callback.onFailure);
      }
    }
  };
}

const matchPattern = (pattern: string) => {
  const regex = new RegExp(pattern);
  return (action: IAction) =>
    regex.test(action.type) && action.type.indexOf('+') === -1;
};

// Following functions look for rest actions and if they find any, they'll run
// the appropriate worker.

export function* watchPosts() {
  yield takeEvery(matchPattern(postType('.*')), makeRequestWorker('post'));
}

export function* watchPuts() {
  yield takeEvery(matchPattern(putType('.*')), makeRequestWorker('put'));
}

export function* watchGets() {
  yield takeEvery(matchPattern(getType('.*')), makeRequestWorker('get'));
}

export function* watchDeletes() {
  yield takeEvery(matchPattern(deleteType('.*')), makeRequestWorker('delete'));
}

export default function* sagas() {
  yield* [
    fork(watchPosts),
    fork(watchPuts),
    fork(watchGets),
    fork(watchDeletes)
  ];
} 
```

这段糟糕的代码检查 API 调用方法的传入动作，用那个方法和`payload.data`到`payload.url`发出请求。它还创建请求、成功和失败操作。

代码可能会令人困惑，但结果是我可以创建动作，并从 reducer 获得我想要的数据，而无需创建任何其他动作创建者。

有很多这样的情况，你写的代码感觉是重复的。我在写 reducers 时也有这种感觉。Reducers 是处理传入数据并管理它们以创建应用程序的新状态(或状态的一部分)的神奇功能。我实际上创建了一个小的[库](https://www.npmjs.com/package/redux-utility-belt)只是为了让编写 Redux 相关代码更快一点。

在每个项目的开始做这种样板工作让我的生活变得更加容易。这样节省下来的时间可以用来发现性能问题、修复 bug、添加更多功能并使应用程序变得更好。

利用别人的图书馆并不总是容易的，即使他们有很好的文档和很棒的社区。如果你认为你做的一些抽象可以在其他项目中使用，你总是可以用它创建一个包。

这些只是例子，我不认为这对其他人的开发流程有多大用处。但是欢迎所有的建议。

让我知道你是否有任何“样板”代码，使生活变得更容易一点。
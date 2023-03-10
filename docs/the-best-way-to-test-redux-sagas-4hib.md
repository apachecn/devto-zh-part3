# 测试 Redux 传奇的最佳方式

> 原文：<https://dev.to/phil/the-best-way-to-test-redux-sagas-4hib>

TL；dr:要测试一个传奇，整体运行(使用`runSaga()`)比分步运行(使用`gen.next()`)更有**的方式，方式**

在我的团队中，我们目前在 React/Redux 应用程序中使用`redux-saga`来处理异步调用。这些传奇可以使用 [ES6 生成器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)调用 API 和调度动作。下面是一个虚构的例子，在这个例子中，我们加载了一个概要文件。在`yield`陈述之后，你可以看到在我们团队的传奇故事中出现的 3 个副作用:

*   `select`“指示中间件调用商店上提供的选择器”
*   `put`“指示中间件向商店分派动作”
*   `call`指示中间件调用给定的函数

你可以在 [API 参考](https://redux-saga.js.org/docs/api/)中找到完整的描述。

这个博客中的所有代码片段都可以在[这个示例库](https://github.com/philherbert/saga-testing-examples)中找到。

```
import {call, put, select} from 'redux-saga/effects';
import {isAuthenticated} from './selectors';
import {loadProfileFailure, loadProfileSuccess} from './actionCreators';
import {getProfile} from './api';

export function* loadProfileSaga(action) {
  // use a selector to determine if the user is authenticated
  const authenticated = yield select(isAuthenticated);
  if (authenticated) {
    // call the API and dispatch a success action with the profile
    const profile = yield call(getProfile, action.profileId);
    yield put(loadProfileSuccess(profile));
  } else {
    // dispatch a failure action
    yield put(loadProfileFailure());
  }
} 
```

# 按部就班测试传奇是垃圾

为了测试 sagas，到目前为止，我们的方法是调用生成器函数来获取迭代器对象，然后手动调用`.next()`来遍历`yield`语句，并断言每个`yield`的值。

为了测试 saga 在用户未通过身份验证的情况下是否会调度失败操作，我们可以断言第一个`gen.next()`——即第一个`yield`——调用了选择器。

然后，为了假装选择器返回 false，我们需要**将一个假装的返回值从选择器传递到下面的`gen.next()`** 。这就是为什么我们要在下面的测试中调用`gen.next(false).value`。如果没有对生成器的深入了解，这种语法就是陌生的和不透明的。

```
it('should fail if not authenticated', () => {
  const action = {profileId: 1};
  const gen = loadProfileSaga(action);

  expect(gen.next().value).toEqual(select(isAuthenticated));
  expect(gen.next(false).value).toEqual(put(loadProfileFailure()));
  expect(gen.next().done).toBeTruthy();
}); 
```

接下来，让我们测试用户被认证的情况。没有必要断言第一个`yield`是一个`select()`，因为我们在之前的测试中已经这么做了。为了避免重复的断言，我们可以在断言之外编写`gen.next()`来跳过它。但是孤立的看测试，这个 **`gen.next()`只是一个魔咒**，目的并不明确。像前面的测试一样，我们可以调用`gen.next(true).value`来假装选择器已经返回`true`。

然后，我们可以测试接下来的`yield`是 API 调用，**将一些假装的返回值`getProfile()`传递到接下来的`gen.next()`** 中，并断言使用相同的返回值调度成功动作。

```
it('should get profile from API and call success action', () => {
  const action = {profileId: 1};
  const gen = loadProfileSaga(action);

  const someProfile = {name: 'Guy Incognito'};

  gen.next();
  expect(gen.next(true).value).toEqual(call(getProfile, 1));
  expect(gen.next(someProfile).value).toEqual(put(loadProfileSuccess(someProfile)));
  expect(gen.next().done).toBeTruthy();
}); 
```

## 为什么分步测试不好？

### 非直观测试结构

在 saga-land 之外，我们编写的 99%的测试大致遵循一种安排-行为-断言的结构。对于我们的例子，应该是这样的:

```
it('should fail if not authenticated', () => {
  given that the user is not authenticated

  when we load the profile

  then loading the profile fails
}); 
```

对于传奇故事，我们测试的条件可能是像`yield call`或`yield select`这样的副作用的结果。这些效果的结果作为参数传递给紧随其后的`gen.next()`调用**，它本身通常在断言**中。这就是为什么上面的第一个示例测试包括这两行:

```
 // this is the call that we want to "stub"
                        //                  ↓
expect(gen.next().value).toEqual(select(isAuthenticated));
expect(gen.next(false).value).toEqual(put(loadProfileFailure()));
    //            ↑
    //  this is the return value (!) 
```

因此，上面的示例 saga 测试更像这样，而不是安排-动作-断言:

```
it('should fail if not authenticated', () => {
    create the iterator
    for each step of the iterator:
      assert that, given the previous step returns some_value, 
      the next step is a call to someFunction()
}); 
```

### 难以测试的底片

对于示例 saga，如果用户没有经过身份验证，那么测试我们不调用 API 是合理的。但是如果我们一步一步地测试每一个`yield`，并且我们不想对传奇的内部结构做出假设，那么唯一彻底的方法就是遍历每一个`yield`，并且断言它们都没有调用 API。

```
expect(gen.next().value).not.toEqual(call(getProfile));
expect(gen.next().value).not.toEqual(call(getProfile));
...
expect(gen.next().done).toBeTruthy(); 
```

我们想要断言`getProfile()`从未被调用，但是我们必须检查每个`yield`不是对`getProfile()`的调用。

### 测试与实现之间的耦合

我们的测试精确地复制了我们的产品代码。我们必须浏览这个传奇的`yield`陈述，断言它们产生正确的东西，并且作为副产品，断言它们以某种固定的顺序被调用。

测试是脆弱的，重构或扩展传奇是非常困难的。

如果我们对副作用重新排序，我们需要修正所有的`expect(gen.next(foo).value)`断言，以确保我们将正确的返回值传递到正确的`yield`语句中。

如果我们在一个传奇的顶部附近分派一个带有新的`yield put()`的额外动作，那么所有的测试都必须在某处添加一个额外的`gen.next()`，以跳过那个`yield`，并移动断言“向下一个屈服”。

我经常盯着一个失败的测试，反复尝试在不同的地方插入`gen.next()`，盲目地戳直到它通过。

# 更好的办法是经营整个传奇

如果我们可以设置测试的条件，指示 saga 运行所有内容并完成其业务，然后检查预期的副作用是否已经发生，会怎么样？这就是我们测试应用程序中其他代码的大致方式，没有理由我们不能对 sagas 也这样做。

这里的金券是我们的效用函数`recordSaga()`，它使用`redux-saga`的`runSaga()`来启动中间件之外的给定传奇，给定动作作为参数。选项对象用于定义传奇副作用的行为。在这里，我们只使用了满足`put`效果的`dispatch`。给定函数将分派的动作添加到一个列表中，该列表在 saga 完成执行后返回。

```
import {runSaga} from 'redux-saga';

export async function recordSaga(saga, initialAction) {
  const dispatched = [];

  await runSaga(
    {
      dispatch: (action) => dispatched.push(action)
    },
    saga,
    initialAction
  ).done;

  return dispatched;
} 
```

有了这个，我们可以模拟一些函数来设置测试的条件，作为一个整体来运行 saga，然后断言被调度的动作或被调用的函数的列表来检查它的副作用。太神奇了！一致！熟悉！

*注意:可以将一个存储传递给`runSaga()`来运行选择器，就像在[文档中的例子](https://redux-saga.js.org/docs/advanced/Testing.html)一样。然而，我们发现，与其用正确的结构构建一个假的商店，不如去掉选择器更容易。*

这是必要的设置，可以放在`describe()`块中。我们使用`jest`来屏蔽 saga 导入的函数。

```
api.getProfile = jest.fn();
selectors.isAuthenticated = jest.fn();

beforeEach(() => {
  jest.resetAllMocks();
}); 
```

对于我们的第一个测试，我们可以使用存根选择器设置测试的条件，运行 saga，然后断言它调度的操作。我们还可以断言 API 调用从来没有进行过！

```
it('should fail if not authenticated', async () => {
  selectors.isAuthenticated.mockImplementation(() => false);

  const initialAction = {profileId: 1};
  const dispatched = await recordSaga(
    loadProfileSaga,
    initialAction
  );

  expect(dispatched).toContainEqual(loadProfileFailure());
  expect(api.getProfile).not.toHaveBeenCalled();
}); 
```

在我们的第二个测试中，我们可以模拟 API 函数的实现来返回一个概要文件，然后断言使用正确的概要文件调度了`loadProfileSuccess()`动作。

```
it('should get profile from API and call success action if authenticated', async () => {
  const someProfile = {name: 'Guy Incognito'};
  api.getProfile.mockImplementation(() => someProfile);
  selectors.isAuthenticated.mockImplementation(() => true);

  const initialAction = {profileId: 1};
  const dispatched = await recordSaga(
    loadProfileSaga,
    initialAction
  );

  expect(api.getProfile).toHaveBeenCalledWith(1);
  expect(dispatched).toContainEqual(loadProfileSuccess(someProfile));
}); 
```

## 为什么整体考比较好？

*   **熟悉的测试结构**，匹配我们应用程序中所有其他测试的 Arrange-Act-Assert 布局。
*   更容易测试否定，因为 saga 实际上会调用函数，所以我们拥有模拟的全部能力。
*   **与实现**分离，因为我们不再测试`yield`语句的数量或顺序。我认为这绝对是这种方法更可取的主要原因。**我们没有测试代码的内部细节，而是测试了它的公共 API**——也就是它的副作用。

在 [`redux-saga`文档](https://redux-saga.js.org/docs/advanced/Testing.html)中提到了测试传奇的两种方法，但是我很惊讶竟然讨论到了循序渐进的方法。从整体上测试一个传奇故事在概念上是熟悉的，而且不那么脆弱。

* * *

受到这个 github 问题的极大启发。
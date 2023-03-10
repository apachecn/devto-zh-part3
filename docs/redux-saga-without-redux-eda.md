# redux-没有 redux 的 saga

> 原文：<https://dev.to/tqm1990/redux-saga-without-redux-eda>

这篇文章基于[我用日语写的博文](https://qiita.com/tqm1990/items/5e6cb017d4f6675636c7)。

这是我第一篇用英语写的 tec 文章。我知道我的文章有很多语法错误，但这是我的挑战之一。

# 为什么？

我想要一个像 redux-saga 一样的并发处理，但不想依赖 redux。
然后，我试着用 redux-saga 不用 redux。

# 怎么做

redux-saga 有 runSaga(选项，saga，...args)作为任何人都可能不知道的外部 API。

api 用于从 redux-saga 的上下文中调用，以运行 saga。
见下图:
【https://redux-saga.js.org/docs/api/】T2

让我们开始写小代码。

```
// helper.js
const sagaOptionSingleton = (() => {                                                                                              

    let channelStd = stdChannel();
    let emitter = new EventEmitter();

    emitter.on("action",channelStd.put);

    return {
        channel:channelStd,
        dispatch:output => {
            emitter.emit("action",output);
        },
        getState:() => {}
    };
 })();

export function runSagaWithFixedOpt(saga){                                                                                                            
  runSaga(sagaOptionSingleton,saga);
} 
```

重要的一点是 stdChannel 已经被赋予了选择权。我认为 API 文档没有描述 stdChannel 是一个接口，该接口缓冲 saga 在 saga 上下文中的一些动作。它应该由单例模式实现。

因此，我编写了一个自执行函数来创建用于设置选项的 sagaOptionSingleton。
最后，redux-saga 没有依赖 redux！

因此,“放”作为 redux-saga 的一个副作用，对 redux 的商店没有任何影响。
如果我们想写一些东西到商店，
我们使用 store.dispatch()。

我得到了一个纯粹的 redux！

但是，如何在 react-redux 中从 mapDispatchToProps 通知事件呢？因为那个原因，它不能这样做。
相反，我要写一个包装函数，如下:

```
// helper.js
export function sendToSaga(data){
    function* sendSomethingToSaga(){    
       yield put(data);
    }
    runSagaWithFixedOpt(sendSomethingToSaga);
} 
```

它试图通过脱离传奇背景运行传奇并执行 put 操作的方式发送一个事件。
然后，
我为 mapDispatchToProps 写了如下代码:

```
const mapDispatchToProps = dispatch => {
  return {
    increment: () => {
      sendToSaga({ type: 'INCREMENT' });// Send a event to saga
    }
  }
} 
```

# 结论

我们可以不用 redux 就用 redux-saga。
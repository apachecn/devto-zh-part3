# 用获取 API 对暂停做出反应

> 原文：<https://dev.to/charlesstover/react-suspense-with-the-fetch-api-374j>

*丹·阿布拉莫夫，在回应 React 开发者询问为什么悬念没有响应获取 API 时:*

[![](img/4a992c0e2b2bfe7e79871b5f942fd916.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q0b-SwVL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ck9fdk2726r4xr0iqofz.png)

从传奇人物丹·阿布拉莫夫(Dan Abramov)本人那里，我们得到了诸如“目前还没有[与 React 悬念兼容的数据获取解决方案]”和“[React Cache]将是第一个”以及“悬念仅限于代码分割”这样的珍宝。

如果我有一件事要告诉丹尼尔“凯西·卡达布拉”阿布拉莫夫，除了他的作品给我留下了深刻的印象，那就是:

[![](img/d0b469b3516d9acaea00174d0f6b79d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_JG3YqRM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n62dtg2hkf31w9aq7k56.png)

让我们来揭示幕后的魔法是反应悬念。出于教育目的，我将介绍我是如何创建这个包的。

# 闭嘴，把包给我！💰

如果你只是来寻求解决办法的，我不怪你。你可以在 NPM 上找到 [`fetch-suspense`，在](https://www.npmjs.com/package/fetch-suspense)[GitHub 库](https://github.com/CharlesStover/fetch-suspense)上找到关于你生活的最广泛的文档。

```
import useFetch from 'fetch-suspense';

const MyComponent = () => {

  // "Look! In the example! It's a fetch() request! It's a hook!"
  //   "No! It's kind of like both at the same time."
  const serverResponse = useFetch('/path/to/api', { method: 'POST' });

  // The return value is the body of the server's response.
  return <div>{serverResponse}</div>; }; 
```

Enter fullscreen mode Exit fullscreen mode

# 悬念是怎么起作用的？🔮

React 库中内置了许多新的 React 特性，而不是外部包，这是因为与支持 React 的引擎紧密耦合带来了性能优势，称为 *React Fiber* 。

由于 React Fiber 与悬念和挂钩等功能的直接集成，您无法在 React 16.5 中创建悬念的逐字副本。但是，您也许可以制作一个性能较差的聚合填充。我将使用一些多填充的例子，这样你可以概念化悬念发生了什么。

```
class Suspense extends React.Component {

  constructor(props) {
    super(props);
    this.state = {
      error: null
    };
  }

  componentDidCatch(e) {
    this.setState({ error: e });
  }

  render() {
    if (this.state.error) {
      return this.props.fallback;
    }
    return this.props.children;
  }
}

/*
<Suspense fallback={<Loading />}>
  <ErrorThrower />
</Suspense>
*/ 
```

Enter fullscreen mode Exit fullscreen mode

这是一个古老的类组件:一个远古时代反应发展的化石遗迹。`componentDidCatch`方法是每当子组件抛出错误时触发的方法。这允许您用一个漂亮的用户界面来替换未被捕获的 JavaScript 错误，或者在应用程序出错时实现重要的逻辑。

上面所做的是制造悬念。由于本地状态没有错误，悬疑的孩子也安装了。在这种情况下，安装了`<ErrorThrower />`组件，它抛出一个错误。

该错误会出现在暂停实例中，`componentDidCatch`方法会接收到该错误。它通过将错误保存到其状态来处理该错误，使其重新呈现。

既然它已经在其本地状态中呈现了一个错误，它不再呈现它的子对象 prop，结果也不再呈现`<ErrorThrower />` devil-child。相反，它呈现它的`fallback`道具，我们已经将其设置为一个漂亮的`<Loading />`模态。

这就是悬念现在的工作方式，除了不是抛出错误， *JavaScript 承诺*被抛出。当悬念捕捉到一个承诺时，*会重新渲染*，显示后备道具，而不是之前抛出承诺的孩子。当承诺解决时，它*再次重新呈现*；这一次不再显示`fallback`道具，而是试图重新渲染原始的孩子，假设孩子们现在已经准备好被渲染，而不是像他们没有意义一样到处扔承诺。

一个实现可能看起来像这样:

```
class Suspense extends React.Component {

  constructor(props) {
    super(props);
    this.state = {
      promise: null
    };
  }

  componentDidCatch(e) {

    // Drake meme where he says no to errors here.
    if (e instanceof Error) {
      throw e;
    }

    // Drake meme where he says yes to promises here.
    if (e instanceof Promise) {
      this.setState({
        promise: e
      }, () => {

        // When the promise finishes, go back to rendering the original children.
        e.then(() => {
          this.setState({ promise: null });
        });
      });
    }

    // This line isn't compatible with the Drake meme format.
    else {
      throw e;
    }
  }

  render() {
    if (this.state.promise) {
      return this.props.fallback;
    }
    return this.props.children;
  }
}

/*
<Suspense fallback={<Loading />}>
  <PromiseThrower />
</Suspense>
*/ 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意的是，在回退发生之前，最初的孩子*试图呈现*。它从未成功过。

# 这怎么适用于取钩子？🎣

到目前为止，您应该已经收集到的是，获取挂钩将需要*抛出承诺*。确实如此。这个承诺就是获取请求。当 Suspense 接收到抛出的获取请求时，它会退回到渲染它的`fallback`属性。当获取请求完成时，它会尝试再次呈现组件。

这里有一个棘手的小问题——抛出获取请求的组件只尝试了*的*渲染，但没有成功。其实根本不是`fallback`的一部分！它没有实例。它从未安装。它没有状态(甚至没有反应钩子状态)；它没有组件生命周期或效果。那么当它*再次尝试*渲染时，它如何知道这个获取请求的响应呢？悬念没有传递它，它没有被实例化，不能有数据附加到它上面。

# 天哪，你是如何解决这个难题的？🤔

我们用记忆来解决它！

“喜欢那个新奇的新功能吗？”

“是的！”(在概念上)

“不行！”(更确切地说)

它不使用`React.memo`，它根据属性来记忆 React 组件。相反，我使用一个无限深度的数组来记忆传递给 fetch 的参数。

如果一个请求进来获取之前已经请求过的数据(第一次尝试失败后第二次尝试实例化一个承诺)，那么它只是返回最终从第一次请求的承诺中解析出来的数据。如果这是一个新的请求，那么我们获取它，将它缓存在内存化数组中，并抛出获取承诺。通过将当前请求与内存数组中的所有条目进行比较，我们知道我们以前是否调度过这个请求。

```
const deepEqual = require('deep-equal');

interface FetchCache {
  fetch?: Promise<void>;
  error?: any;
  init: RequestInit | undefined;
  input: RequestInfo;
  response?: any;
}

const fetchCaches: FetchCache[] = [];

const useFetch = (input: RequestInfo, init?: RequestInit | undefined) => {
  for (const fetchCache of fetchCaches) {

    // The request hasn't changed since the last call.
    if (
      deepEqual(input, fetchCache.input) &&
      deepEqual(init, fetchCache.init)
    ) {

      // If we logged an error during this fetch request, THROW the error.
      if (Object.prototype.hasOwnProperty.call(fetchCache, 'error')) {
        throw fetchCache.error;
      }

      // If we received a response to this fetch request, RETURN it.
      if (Object.prototype.hasOwnProperty.call(fetchCache, 'response')) {
        return fetchCache.response;
      }

      // If we do not have a response or error, THROW the promise.
      throw fetchCache.fetch;
    }
  }

  // The request is new or has changed.
  const fetchCache: FetchCache = {
    fetch:

      // Make the fetch request.
      fetch(input, init)

        // Parse the response.
        .then(response => {

          // Support JSON.
          if (Object.prototype.hasOwnProperty.call(response.headers, 'Content-Type')) {
            return response.json();
          }

          // Not JSON.
          return response.text();
        })

        // Cache the response for when this component
        //   attempts to render again later.
        .then(response => {
          fetchCache.response = response;
        })

        // Cache the error for when this component
        //   attempts to render again later.
        .catch(e => {
          fetchCache.error = e;
        }),
    init,
    input
  };

  // Add this metadata to the memoization array.
  fetchCaches.push(fetchCache);

  // Throw the Promise! Suspense to the rescue!
  throw fetchCache.fetch;
}; 
```

Enter fullscreen mode Exit fullscreen mode

# 听起来像是内存泄漏💧

它可以是一个特性*或*一个 bug！

但是如果您认为这是项目中的一个错误，您可以通过为 fetch 请求提供一个以毫秒为单位的生命周期来使缓存无效。向`useFetch`钩子传递第三个参数(一个数字)将告诉它在那几毫秒后从内存化数组中删除元数据。我们实现起来很简单:

```
// NEW: lifespan parameter
const useFetch = (
  input: RequestInfo,
  init?: RequestInit | undefined,
  lifespan: number = 0
) => {

  // ...

  const fetchCache: FetchCache = {
    fetch:

      // Make the fetch request.
      fetch(input, init)
        .then( /* ... */ )
        .then( /* ... */ )
        .catch( /* ... */ )

        // Invalidate the cache.
        .then(() => {

          // If the user defined a lifespan,
          if (lifespan > 0) {

            // Wait for the duration of the lifespan,
            setTimeout(
              () => {

                // Find this fetch request and kill it
                //   from the memoization array.
                const index = fetchCaches.indexOf(fetchCache);
                if(index !== -1) {
                  fetchCaches.splice(index, 1);
                }
              },
              lifespan
            );
          }
        }),
    // ...
  };
  // ...
};

// ... 
```

Enter fullscreen mode Exit fullscreen mode

当获取完成时，我们已经更新了元数据，滴答。生命周期计时器发生在承诺的`catch`之后是很重要的，因为我们希望它即使发生错误也能设置。

# 结论🍬

当丹·阿布拉莫夫告诉你你不能做某事时，你就去做。

如果你喜欢这篇文章，请随意给它一颗心或一只独角兽。又快又简单，还免费！如果你有任何问题或相关的好建议，请在下面的评论中留下。

要阅读更多我的专栏，你可以在 [LinkedIn](https://www.linkedin.com/in/charles-stover) 、 [Medium](https://medium.com/@Charles_Stover) 和 [Twitter](https://twitter.com/CharlesStover) 上关注我，或者[在 CharlesStover.com](https://charlesstover.com/)上查看我的作品集。
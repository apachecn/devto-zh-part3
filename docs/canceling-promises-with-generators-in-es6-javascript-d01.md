# 用 ES6 Javascript 中的生成器取消承诺

> 原文：<https://dev.to/tuanphungcz/canceling-promises-with-generators-in-es6-javascript-d01>

在我之前关于的博文中，我解释了 ES6 Javascript 中生成器的基础知识。如果你没有读过，你可以在这里看看👉[了解 ES6 Javacsript 中的生成器](https://dev.to/phung_cz/understanding-generators-in-es6-javascript-7fm)

你们中的许多人要求发电机的真实使用案例，所以我将展示我遇到的一个问题。

# 简介

但是为了解释这个问题，我必须说几句我们正在开发的产品 **Mews Navigator** 。

> Navigator 允许您在线办理登机手续，安全地存储您的信用卡详细信息，并让您完全控制您想要共享的信息。

现在，想象一下，你正在通过应用程序进行在线登记，并且正在进行支付步骤。

所以一旦你点击下一步按钮，你会看到一个加载器，然后是你的支付卡列表，非常直接，对吗？

[![Navigator payment step in online check-in](img/2ad7cd1826840049d23f07f514ef0783.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lB4cRC2O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://s3.gifyu.cimg/May-17-2019-00-31-08.gif)

## 渲染付款路线

实际上，引擎盖下的情况要复杂一些。在呈现组件之前，需要解决几个步骤。

```
// Let's say user goes to this url:
// www.mews.li/navigator/check-in/payment/:reservationId

// 1\. This will check if the user is signed in.
// If yes go render <Dashboard /> if not go to <SignIn />
authAction(); // async

// 2\. We need to fetch the reservations
fetchReservations(); // async

// 3\. We need to check if `reservationId` and
// route itself is valid (If all checks pass go to next one)
isReservationIdValid({ reservations, currentReservation }); // sync

// 4\. Fetch paymentcards
fetchPaymentCards(); // async

// 5\. Fetching hotel entitites
fetchHotels(); // async

// 6\. Some of hotels uses PCI proxy vault, if it does,
// we need to initialize PCI proxy script.
doesHotelUsePciProxy({ hotels, hotelId }); // sync

// 7\. Fetch and init the script
initPciProxy(); // async 
```

Enter fullscreen mode Exit fullscreen mode

在呈现组件之前，我们有一些检查和一些 API 获取。

> 问题是，如果这些检查中的任何一个可能失败，并且基于哪些检查失败，我们将重定向到一个特定的案例。

那么如何在不使用任何外部库的情况下解决这个问题呢？还记得上次我给你们看这个[例子](https://dev.to/phung_cz/understanding-generators-in-es6-javascript-7fm)吗？

```
function* avengersGenerator() {
  yield "Hulk"; // Pausing the execution
  yield "Thor";
  yield "Iron man";
  return "Ultron"; // Exiting of finishing the generator
  yield "Spiderman";
}

const iterator = avengersGenerator();

iterator.next(); 
```

Enter fullscreen mode Exit fullscreen mode

[查看 codesandbox 中的源代码](https://codesandbox.io/s/7jkr9y3o81)

看一下`return`的说法。这将停止执行并忽略在`return`语句之后的所有内容。

这给了我们重复承诺和取消承诺链中任何地方的可能性。

## 概念证明

让我们为我们的用例创建一些足够通用的东西来解决路由中的这种情况。要点是:

*   能够处理同步和异步功能(API 调用)
*   某些检查一失败，代码就返回重定向。
*   足够通用，所以我们也可以重用其他路线。

所以我打开代码沙箱，想出了这个解决方案👉 [Codesandbox](https://codesandbox.io/s/understanding-generators-8oe8q)

正如您在控制台中看到的，我们有多个操作和一些检查。我们可以绕过应该失败的检查，代码的其余部分不会执行。

[![Navigator payment step in online check-in](img/86a52cacf9c7cffba1245238a974e665.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Fwc2tUZX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://s3.gifyu.cimg/generator-example.gif) 
[查看 codesandbox 中的源代码](https://codesandbox.io/s/understanding-generators-8oe8q)

这是代码中付款步骤路线的实现示例。

```
function* paymentRouteGenerator() {
  yield authAction();
  yield fetchReservations();
  yield isReservationIdValid();

  yield fetchPaymentCards();
  yield fetchHotels();
  yield doesHotelUsePciProxy({ hotelId });
  yield initPciProxy();
}

const CHECK_IN_PAYMENT_ROUTE = {
  name: Route.CheckInPayment,
  path: "/:reservationId",
  action: resolveAction(
    generatorWrapper(paymentRouteGenerator),
    renderComponent(() => <CheckInPaymentStep />)
  )
}; 
```

Enter fullscreen mode Exit fullscreen mode

我必须为我们的生成器编写一个处理程序。这是`magic`发生的地方。我已经在下面的评论中解释了每一步。

```
const generatorWrapper = generator => context => {
  // 1\. Creating an iterator
  const iterator = generator(context);

  // 3\. This function except yielded as a argument
  const handle = yielded => {
    const handleWithRedirectCheck = route => {
      // 4\. Here is where the magic happens, we check if there is a redirect, if yes,
      // it would redirect (cancel) and will not execute the rest of the generator
      if (get("redirect", route)) {
        return route;
      }
      // Otherwise continue
      return handle(iterator.next());
    };
    // Exit if we are at the end of the generator
    if (yielded.done) {
      return;
    }

    // Handling the async case if action/check is a promise
    if (isPromise(yielded.value)) {
      return yielded.value.then(handleWithRedirectCheck);
    }
    // If its not a promise, we can call handleWithRedirectCheck directly
    return handleWithRedirectCheck(yielded.value);
  };

  // 2\. Handling the iterator
  return handle(iterator.next());
}; 
```

Enter fullscreen mode Exit fullscreen mode

[查看 codesandbox 中的源代码](https://codesandbox.io/s/understanding-generators-8oe8q)

现在，我只是在玩它，所以如果你有任何更好的解决方法，一定要告诉我。😉

* * *

## 感谢阅读

让我知道你对这个**发电机系列**的看法。如果你爱它，你知道该怎么做！分享给你的朋友和同事。

如果你希望我在下一篇文章中涉及一些话题，请在这里的 [dev.to](https://dev.to/phung_cz) 或 twitter [@phung_cz](https://twitter.com/phung_cz) 上给我发消息，或者如果你有任何建议，欢迎在下面发表评论。

下次见，继续黑✌

* * *

看一下我们正在建造什么 [@ Mews systems](https://github.com/MewsSystems/developers) 我们也在[招聘开发人员](https://github.com/MewsSystems/developers)和其他职位的人员。如果你有任何问题，请联系我。
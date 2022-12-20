# JavaScript 中的函数式编程

> 原文：<https://dev.to/apium_hub/functional-programming-in-javascript-430b>

Javascript(及其生态系统)是一种发展非常迅速的语言。新的库和框架以疯狂的速度出现，提供新的架构和功能。在过去的文章中，我们已经看到了 [TypeScript 为我们准备了什么](https://dev.to/apium_hub/top-typescript-advantages-4lpa)以及[如何轻松地从一个旧项目](https://dev.to/apium_hub/a-simple-way-to-migrate-from-javascript-to-typescript-574h-temp-slug-2755564)迁移到这个 JS 超集。今天的文章将略有不同:这次我想展示几个关于**的例子，我们如何在 JavaScript** 中应用函数式编程。

为此，我们将使用 [Ramda-fantasy](%E2%80%9Dhttps://github.com/ramda/ramda-fantasy%E2%80%9D) ，一个提供大量单子的强大抽象库，以及另一个名为 [Ramda](%E2%80%9Dhttps://ramdajs.com/%E2%80%9D) 的库，它将帮助我们构建功能管道并实现不变性。不过，在我们开始之前，提醒一下:这些不是唯一提供这些功能的库；搜索足够长的时间，你可以很容易地找到完全有效的替代品。

## Javascript 函数式编程

尽管**函数式编程可以极大地改善应用程序的代码**，但它的原理在一开始可能会有点挑战性。由于详细解释所有这些概念将花费大量时间，我们决定使用两个实际的代码示例来介绍这些概念，如果它们引起了您的兴趣，那么我们强烈建议您继续研究 Javascript 中的函数式编程。

### [![GSAS](img/2dbe1733eacd9e3c7573ae14c6e4cb61.png)T4】](https://gsas.io)

### 1——也许是单子

在第一个例子中，我们将找到一种避免验证变量是否为空的方法。让我们想象一下，在我们的应用程序中，我们可以找到以下格式的用户:

```
const someUser = {
 name: 'some_name',
 email: 'some@email.com',
 settings: {
  language: 'sp'
 }
}; 
```

我们有一个函数，可以用用户设置的语言返回欢迎消息。

```
const allGreetings = {
 'en': 'Hello!',
 'sp': 'Hola!',
 'fr': 'Bonjour!'
};

const getGreetingForUser = (user) => {
 //to be implemented
} 
```

让我们来看看遵循命令式模型的“getGreetingForUser”函数的实现:

```
const getGreetingForUser = (user) => {
 if (!user) {
   return allGreetings.en;
 }
 if (user.settings && user.settings.language) {
   if (allGreetings[user.settings.language]) {
     return allGreetings[user.settings.language]
   } else {
     return allGreetings.en;
   }
 } else {
   return allGreetings.en;
 }
};
console.log(getGreetingForUser(someUser)); 
```

如您所见，我们必须检查用户是否已经存在，他/她是否有语言设置，以及所述语言是否在准备好的欢迎消息中。如果出现问题，我们用默认语言返回消息，在我们的例子中是英语。

现在，让我们看看同一个函数，但这次我们将在其实现中使用函数式编程:

```
const getGreetingForUser = (user) => {
  return RamdaFantasy.Maybe(user)
    .map(Ramda.path(['settings', 'language']))
    .chain(maybeGreeting);
};

const maybeGreeting = Ramda.curry((greetingsList, userLanguage) => {
  return RamdaFantasy.Maybe(greetingsList[userLanguage]);
})(allGreetings);
console.log(getGreetingForUser(someUser).getOrElse(allGreetings.en)); 
```

这个解决方案一开始可能看起来很混乱，但是一旦你理解了我们使用的概念，就不是这样了。

为了处理潜在的空或未定义的情况，我们将使用 Maybe 单子。这允许我们在对象周围创建一个包装器，并为空对象分配一个默认行为。

让我们比较一下这两种解决方案:

```
//Instead of verifying if the user is null
 if (!user) {
   return allGreetings.en;
 }
//We’ll use:
 RamdaFantasy.Maybe(user) //we add user to the wrapper 
```

———————-

```
//Instead of:
 if (user.settings && user.settings.language) {
   if (allGreetings[user.settings.language]) {
//We’ll use:
 <userMaybe>.map(Ramda.path(['settings', 'language'])) //If data exists, map will use it 
```

——————–

```
//Instead of returning the default value in the else:
 return indexURLs['en'];
//We’ll use:
 .getOrElse(allGreetings.en) //getOrElse will either return someuser’s valuer or the default value that we specified. 
```

### 2-任一单子

当我们知道存在空错误时的默认行为时，Maybe Monad 非常有用。

但是如果我们有一个抛出错误的函数，或者我们链接了多个抛出错误的函数，并且我们想知道哪一个出错了，我们可以使用任一单子。

现在，假设我们想计算一个产品的价格，考虑增值税和可能的折扣。我们已经有了这个代码:

```
const withTaxes = (tax, price) => {
 if (!_.isNumber(price)) {
 return new Error("Price is not numeric");
 }
 return price + (tax * price);
};
const withDiscount = (dis, price) => { 
  if (!_.isNumber(price)) { 
    return new Error("Price is not numeric"); 
  } 
  if (price < 5) 
    return new Error("Discounts not available for low-priced items"); 
  } 
  return price - (price * dis); 
}; 
const isError = (e) => e && e.name === 'Error';
const calculatePrice(price, tax, discount) => { 
//to be implemented 
} 
```

让我们来看看遵循命令式模型的“calculatePrice”函数的实现:

```
const calculatePrice = (price, tax, discount) => {
  const priceWithTaxes = withTaxes(tax, price);
  if (isError(priceWithTaxes)) {
    return console.log('Error: ' + priceWithTaxes.message);
  }
  const priceWithTaxesAndDiscount = withDiscount(discount, priceWithTaxes);
  if (isError(priceWithTaxesAndDiscount)) {
    return console.log('Error: ' + priceWithTaxesAndDiscount.message);
  }
  console.log('Total Price: ' + priceWithTaxesAndDiscount);
}
//we calculate the final price of a product that’s worth 25, with 21% VAT and a 10% discount.
 calculatePrice(25, 0.21, 0.10) 
```

现在，让我们看看如何使用这两个单子重写这个函数。

要么有两个构造函数，左和右。我们想要实现的是将异常存储到左边的构造函数中，将正常结果(快乐路径)存储到右边的构造函数中。

首先，我们将改变已经存在的 withTaxes 和 withDiscount 函数，这样如果有错误，它们返回 Left，如果一切正常，它们返回 Right:

```
const withTaxes = Ramda.curry((tax, price) => {
  if (!_.isNumber(price)) {
    return RamdaFantasy.Either.Left(new Error("Price is not numeric")); //we assign the error to Left
  }
  return RamdaFantasy.Either.Right(price + (tax * price)); //we assign the result to Right
});
const withDiscount = Ramda.curry((dis, price) => {
  if (!_.isNumber(price)) {
    return RamdaFantasy.Either.Left(new Error("Price is not numeric")); //we assign the error to Left
  }
  if (price < 5) { 
    return RamdaFantasy.Either.Left(new Error("Discounts not available for low-priced items")); //we assign the error to Left 
  } 
  return RamdaFantasy.Either.Right(price - (price * dis)); //we assign the result to Right 
}); 
```

然后，我们为右边的情况(显示价格)创建一个函数，为左边的情况(显示错误)创建另一个函数，并使用它们来创建任一单子:

```
const showPrice = (total) => { console.log('Price: ' + total) }; 
const showError = (error) => { console.log('Error: ' + error.message); }; 
const eitherErrorOrPrice = RamdaFantasy.Either.either(showError, showPrice); 
```

最后，我们只需要执行单子来计算最终价格:

```
//we calculate the final price of a product that’s worth 25, with 21% VAT and a 10% discount.
 eitherErrorOrPrice(
   RamdaFantasy.Either.Right(25)
     .chain(withTaxes(0.21))
     .chain(withDiscount(0.1))
) 
```

## 结论:Javascript 函数式编程

正如我们所看到的，一旦我们用 Maybe 和 monads 分解了代码，就不会觉得那么复杂了。如果使用正确，它们可以使我们的代码更容易阅读和维护。

唯一不方便的是我们需要克服的初始障碍，但这可以通过观察一些例子和做一些测试来完成。

我们希望通过这篇文章，您可以开始探索 JavaScript 和/或您使用的任何语言中的函数式编程。

JavaScript 中的[函数式编程这个帖子最早出现在](https://apiumhub.com/tech-blog-barcelona/functional-programming-javascript/) [Apiumhub](https://apiumhub.com) 上。
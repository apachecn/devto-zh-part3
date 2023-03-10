# 用聚乙烯改进 Javascript 函数式编程

> 原文：<https://dev.to/danielescoz/improving-javascript-functional-programming-with-polyethylene-47e5>

如果你使用过 Javascript 和数组，你肯定用过一些和它们打包在一起的功能实用程序:`filter`、`map`、`reduce`等等。它们在很多情况下都是非常有用的工具，但是也有一些明显的缺点:

*   对函数方法的每次调用都返回一个新数组。这会创建不必要的中间数组，浪费时间和内存。
*   这些实用程序仅适用于阵列。几年前这不是问题，但是随着`Symbol.iterator`和`for...of`的引入，现在这已经不够了。
*   不支持任何异步操作。没有回调，没有承诺，没有事件，什么都没有:你的代码必须是同步的，你的数据必须已经在内存中。

多亏了`for..of`,我们可以通过自己重新实现方法来解决所有这些问题，迎合每一种情况，但是这首先破坏了拥有功能实用程序的意义。我们能做什么？

聚乙烯来拯救我们了。聚乙烯可以解决上述所有问题，还有一些你不知道的问题。我们一个一个来看，后面我再展开。

但是首先，声明:我是聚乙烯的作者，所以记住我在这里说的一切。

此外，您将在这里看到的所有代码都将假设您正在导入聚乙烯，如下所示:

```
const Poly = require('polyethylene'); 
```

Enter fullscreen mode Exit fullscreen mode

这就是你需要知道的，让我们进入正题！

## 保存在数组副本上

因为聚乙烯对象是纯粹的生成器，所以没有时间和空间花费在存储一系列函数调用的中间结果上。这可以加快长数组的处理速度。

我们来举个例子。假设我们有一个包含人名、国家代码和年龄的列表。我们想知道生活在西班牙和 T2 的人的平均年龄

```
const people = [{name: 'Dani', country: 'ES', age: 27}, /* more people */];

const {age, num} = people
  .filter(person => person.country === 'ES') // filter by country
  .map(person => person.age) // we're only interested in their age
  .reduce( // find total age and number of people
    (acc, age) => ({age: acc.age + age, num: acc.num + 1}),
    {age: 0, num: 0}
  );

const avgAge = age / num; // we have the average now! 
```

Enter fullscreen mode Exit fullscreen mode

> *注意:我知道`map`阶段是不必要的，但它是为了说明的目的*

如果我们运行这段代码，我们会发现数据集中所有西班牙人的平均年龄。简单吧？如果我们的数据集不是一个人，甚至不是几百人，而是几千或几百万人，问题就出现了。因为我们在每一步都创建数组，所以我们必须花费时间和空间来存储和填充所有这些数组。我们可以通过一个简单的步骤使代码适应聚乙烯:将数组包装在聚乙烯对象中:

```
const Poly = require('polyethylene');
const people = [{name: 'Dani', country: 'ES', age: 27}, /* more people */];

const {age, num} = Poly.from(people)
  .filter(person => person.country === 'ES') // filter by country
  .map(person => person.age) // we're only interested in their age
  .reduce( // find total age and number of people
    (acc, age) => ({age: acc.age + age, num: acc.num + 1}),
    {age: 0, num: 0}
  );

const avgAge = age / num; // we have the average now! 
```

Enter fullscreen mode Exit fullscreen mode

唯一的*变化是，当开始我们的函数链时，我们将数组包装成`Poly.from(people)`。这将产生一个聚乙烯`Iterable`物体，可用于功能链。然而，不同之处在于，永远不会创建中间数组。*

在这样一个玩具例子中，当对大约一百万人进行测量时，我注意到时间减少了大约 10%。然而，我通过重复同样的 1000 个人 1000 次来创建数据集，将其存储在一个数组中，然后只使用聚乙烯。但事实证明，我们也可以用聚乙烯做到这一点！

```
/* Array-only version */
const repeatedPeople = Array(1000).fill().flatMap(() => somePeople)

/* Polyethylene version */
const repeatedPeople = Poly.range(1000).flatMap(() => somePeople) 
```

Enter fullscreen mode Exit fullscreen mode

在这两种情况下，我们都将得到一个一百万人的可迭代数组，但是在第二种情况下，没有创建一个包含一百万个条目的数组。然后，我重复了我的实验，并增加了重复次数:

| 数量 | One thousand | Five thousand | ten thousand | Fifty thousand | One hundred thousand |
| --- | --- | --- | --- | --- | --- |
| 排列 | 212 毫秒 | 1123 毫秒 | 2190 毫秒 | 10350 毫秒 | **崩溃** |
| 聚酯纤维（polyester 的简称） | 84 毫秒 | 380 毫秒 | 749 毫秒 | 3671 毫秒 | 7446 毫秒 |

如您所见，聚乙烯在处理非常大的数据集时速度更快。在这种情况下尤其如此，因为对于数组，我们需要首先构建数据集，然后处理它。你也可以看到，有 1 亿个条目，数组版本就崩溃了:它耗尽了内存。聚乙烯版本可能需要很长时间，但它不会因此而崩溃。

请注意，这并不总是正确的，对于小型阵列，由于生成器的开销以及可能的缓存，聚乙烯实际上会更慢。性能不是聚乙烯的目标，只是一个好的副作用。

## 在除数组以外的可迭代对象中使用函数实用程序

现在我们进入了没有聚乙烯就不行的领域。在这种情况下，它在非数组的可迭代对象上做功能性的工作。

为了举例说明这一点，我们将使用数学。假设我们想要找到前 100 个[快乐数字](https://en.wikipedia.org/wiki/Happy_number) :

```
const first100HappyNums = Poly.range(1, Infinity)
  .filter(isHappy) // assume we already have an `isHappy` function
  .take(100)
  .toArray(); 
```

Enter fullscreen mode Exit fullscreen mode

让我们一步一步来:

*   `Poly.range(1, Infnity)`迭代`1`和`Infinity`之间的所有数字。正如你可以想象的，这是一个无限的迭代，由于后来的限制，我们可以处理它
*   假设`isHappy`函数工作正常，`.filter(isHappy)`将只留下那些满意的数字。这仍将是无限的，但密度要小得多。
*   `.take(100)`将导致只有前 100 个元素的有限迭代。因为我们已经只有快乐的数字，这将是第一个 100 快乐的数字。
*   将最终收集所有元素并返回一个数组。

如您所见，用函数式实用程序来做这件事对于数组来说是不可能的。因此，聚乙烯填补了功能上的空白。

不过，你不需要进行无限的迭代来完成这项工作。`Poly.from`适用于任何 iterable，所以你可以使用一个`Set`、`Buffer`或任何其他实现 iterator 接口的对象。

但是，我们只是触及了聚乙烯的皮毛...

## 使用`async`回调和异步迭代

我们只使用了同步函数，但是聚乙烯也可以将`async`函数作为回调来处理。不过，要做到这一点，我们需要首先通过调用链中的`.sacync()`将 Iterable 转换为 AsyncIterable。从那时起，一切都是异步的。

我们来举个例子。假设我们有一个城市列表，我们想知道他们的天气预报。我将使用 [`request-promise`](https://www.npmjs.com/package/request-promise) 给[天气预报](https://www.metaweather.com/api/)打电话，所以你也可以试试这个，而不必在任何地方注册。

首先，让我们定义函数来查询我们的 API:

```
const reqProm = require('request-promise');

async function searchLocation (query) {
  return reqProm({
    uri: 'https://www.metaweather.com/api/location/search',
    qs: {query},
    json: true,
  });
}

async function getWeather (id) {
  const response = await reqProm({
    uri: `https://www.metaweather.com/api/location/${id}`,
    json: true,
  });

  return response.consolidated_weather;
} 
```

Enter fullscreen mode Exit fullscreen mode

假设我们想要打印列表中每个城市今天的最低和最高温度；如果我们的城市查询匹配多个位置，我们将多次打印它。如果我们不得不在没有聚乙烯的情况下做这件事，我会这样做:

```
const today = new Date().toISOString().split('T')[0];
const cities = ['madrid', 'san']; // 'san' will yield 11 results

for (const city of cities) {
  const searchResult = await searchLocation(city);

  for (const location of searchResult) {
    const weatherList = await getWeather(location.woeid);
    const todaysWeather = weatherList.find(w => w.applicable_date === today);
    console.log('%s: %s, %s', location.title, todaysWeather.min_temp, todaysWeather.max_temp);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

还不错，尽管如果我们需要更多的步骤，事情会变得复杂。
聚乙烯让我们以一种更简化的方式来做这件事，尽管我们要提到一个警告:

```
const today = new Date().toISOString().split('T')[0];
const cities = ['madrid', 'san'];

Poly.from(cities)
  .async()
  .flatMap(searchLocation)
  .flatMap(async (loc) => (await getWeather(loc.woeid))
    .map(w => ({city: loc.title, ...w}))
  )
  .filter(res => res.applicable_date === today)
  .forEach(res => console.log('%s: %s, %s', res.city, res.min_temp, res.max_temp)); 
```

Enter fullscreen mode Exit fullscreen mode

唯一奇怪的是在第二个`.flatMap`中，我们需要在嵌套地图中注入城市名称，以便以后使用。由于代码的自然嵌套，我们在前面的例子中不需要这样做。这是为了表明聚乙烯并不完美，有时我们需要修改代码以使其工作。

正如您所看到的，我们已经能够为`flatMap`调用使用`async`函数。我们也可以在《T2》或《T3》中使用它们。感谢`.async()`调用，所有这些都是可能的，如果我们不使用它，我们的迭代器将是同步的，什么都不会工作。

但这还不是全部，聚乙烯最好的事情之一是它能够直接与异步可迭代对象一起工作。我非常喜欢的一个例子是在页面中从 Reddit 加载数据。假设我们想要列出给定子编辑中排名前 100 的帖子，其中*不是*的粘性帖子，而*是*的文本帖子(类型`self`)。一种方法可能是:

```
const reqProm = require('request-promise');

async function getRedditPage (subreddit, {limit = 50, before, after} = {}) {
  return reqProm({
    uri: `https://reddit.com/r/${subreddit}.json`,
    qs: {limit, before, after},
    json: true,
  });
}

const WANTED = 50;
const posts = [];
let after = null;

while (posts.length < WANTED) {
  const page = await getRedditPage('factorio', {limit: 100, after});

  posts.push(...page.data.children.filter(post => !post.data.stickied && 
  post.data.post_hint === 'self'));
  after = page.data.after;
}

posts.slice(0, WANTED)
  .forEach((post, i) => console.log('[%s]', post.data.name, post.data.title)) 
```

Enter fullscreen mode Exit fullscreen mode

这有点麻烦，因为我们需要循环和所有添加到数组中的东西才能工作。但主要的问题是，很难使它可重用，因为由于过滤器，我们加载的项目总数是未知的，所以我们需要一页一页地进行。

使用 Polyethylene，我们可以创建一个函数，首先列出该子编辑中的所有帖子，然后过滤并打印它们。我们可以用`iterate`来表示这个:

```
function listSubreddit (subreddit) {
  return Poly.iterate(async ({done, after}) => {
    if (done) {
      return {done, posts: []};
    }

    const result = await getRedditPage(subreddit, after);
    return {
      after: result.data.after,
      posts: result.data.children,
      done: after == null,
    };
  }, {done: false})
    .flatMap(({posts}) => posts)
    .map(post => post.data);
}

listSubreddit('factorio')
  .filter(post => !post.stickied && post.post_hint === 'self')
  .take(100)
  .forEach((post, i) => console.log('[%s]', post.name, post.title)); 
```

Enter fullscreen mode Exit fullscreen mode

这需要一些解释。`Poly.iterate`方法通过无限重复调用传递的函数来创建 iterable，将最后一个元素作为参数传递(将第二个参数传递给`iterate`作为初始值)。我们使用这些属性传回`after`字段和一个`done`标志，该标志指示页面是否用尽，同时向前传递`posts`。然后，我们展平帖子并获取它们的数据属性。

然后可以为任何子编辑调用该函数，您将得到一个包含所有帖子的列表，简单明了。我们称之为，用我们的条件过滤，只取前 100 个并打印出来。很简单。

## 超越功能实用程序:预取/预载

但是等等，还有呢！

我们的最后一招是预加载和预取。您可以将这两个选项传递到异步迭代的任何阶段，神奇的事情就会随之发生:

*   如果`preload`开启，该阶段的第一个元素将尽快生成。如果 iterable 对象需要一段时间进行迭代，这将确保它立即可用。这在大多数情况下不是很有用，因为您可能会立即进行迭代。
*   如果`prefetch`开启，在处理当前元素之前*将请求迭代的下一个元素。这意味着，如果在一个阶段之后有一个长时间的处理，下一个元素将是可用的，因为它将被并行产生。*

这两个选项可以加快链上的聚合处理时间，因为它们允许并行化，但默认情况下是不活动的，因为如果使用限制阶段，它们*将*请求比必要更多的元素。

* * *

那是一篇很长的文章。

这是聚乙烯。这是我不久前开始的一个玩具项目，但我认为它真的很有用，尤其是异步部分。我仍在考虑改进，欢迎每个人贡献想法、建议、错误报告、批评，当然还有代码。

在 [npm](https://www.npmjs.com/package/polyethylene) 和 [GitHub](https://github.com/Darkhogg/polyethylene#readme) 中找到聚乙烯。
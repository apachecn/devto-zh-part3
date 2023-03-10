# 用五行 JavaScript 代码将地理定位添加到 HTML 表单中

> 原文：<https://dev.to/ascorbic/add-geolocation-to-an-html-form-in-five-lines-of-javascript-og9>

地址是你可能创建的 HTML 表单中最常见的类型，大多数都有糟糕的 UX 设计。我们将逐步改进我们的表单以修复一个最常见的错误，并可能在此过程中学习一些 fetch 和 async/await。不可否认，对于三行代码来说，这已经很多了，所以如果你只是想要 tl，就滚动到底部；博士代码。

除非您只为一个国家服务，否则您将需要表单中的国家选择元素。任何创建了国家选择的人可能都会问自己这样一个问题:应该预先选择哪个选项，以及这些选项应该按什么顺序排列。

显而易见的答案，也是大多数情况下选择的答案，是这个:

[![Choose your country](img/92b9f91da9e0c124e984c096979c787e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1KDeipwc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/szpfzjhw8bb2mpsgjyux.png)

按字母顺序排列，默认选项在顶部。这具有逻辑性的好处。找到合适的国家相对容易，虽然有点慢。您可能知道您可以通过键入前几个字母在 select 元素中找到一个选项，但是您的大多数用户不知道。就算他们有，还是很烦(不，我不要阿联酋)。

您可能会看到的另一个选项是:

[![US at top](img/1cd35dddd2b0cb32716d89b7e5851473.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h4J-87C2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4pyrm65p58dhhuxfdugi.png)

将顶部的选项替换为您选择的国家。或许还可以添加一些你最常用的选择。这样做的好处是节省了一些滚动，但仍然很烦人，你仍然需要选择将哪些选项放在顶部。如果你的用户不是最好的选择之一，如果有什么比字母排序更烦人的话。其他选项包括保持字母顺序，但预先选择你最常见的国家。可怜的加拿大人。

现在可能有人在喊“GeoIP！”这很好。然而，任何实际实现过它的人都知道，虽然理论上很简单，但安装正确的模块并保持 MaxMind 数据库最新实际上是正确的。你在野外看到的为数不多的预选形式就是证明。

有更好的办法！我们可以使用渐进增强的原则来为大多数用户选择正确的选项，而不会破坏其他人的体验，也不会延迟加载。最棒的是，我们只用三行 JavaScript 就可以做到，而且不需要在服务器上安装任何东西。这要感谢 [freegeoip.app](https://freegeoip.app/) 的慷慨，它提供了一个免费的地理定位 API。这使用了 MaxMind 的 GeoLite 数据库，它不具备完整的付费数据库的准确性，但对于我们的目的来说已经足够好了，因为我们只需要国家级的数据。我们将获取 JSON 文件的[，提取`country_code`，然后选择正确的元素。](https://freegeoip.app/json/)

在您使用 jQuery 之前，让我们看看另一种方式，因为[您可能不需要它](https://css-tricks.com/now-ever-might-not-need-jquery/)。使用标准的 XmlHttpRequest 很烦人，但是[他们让 fetch 发生了](https://twitter.com/alyssanicoll/status/924036963962904578)。也不要害怕承诺，因为 async/await 让异步编程像编写同步代码一样简单。我假设您有一个 id 为“countries”、值为 ISO 国家代码的选择。例如

```
<select id="countries">
    <option>Choose your country</option>
    <option value="AF">Afghanistan</option>
    <option value="AX">Åland Islands</option>
    <option value="AL">Albania</option>
    <option value="DZ">Algeria</option>
    <option value="AS">American Samoa</option>
    <option value="AD">Andorra</option>
    <option value="AO">Angola</option>
    <!-- ... -->
</select> 
```

Enter fullscreen mode Exit fullscreen mode

### 进行性增强

渐进增强的原则表明，我们不应该破坏功能较弱的浏览器的体验，而是应该为支持它的浏览器添加功能。我们将在页面加载后加载 GeoIP API，如果成功，我们将选择正确的选项。

```
(async () => {
  const result = await fetch("//freegeoip.app/json/");
  const json = await result.json();
  document.getElementById("countries").value = json.country_code;
})(); 
```

Enter fullscreen mode Exit fullscreen mode

这是一个*立即调用的异步函数表达式*。它将立即返回，因此不会阻塞页面呈现。

如果你没有使用过 async/await，一开始可能会觉得不熟悉。为了分解它，我们定义了一个异步箭头函数，我们立即调用它。这是因为我们还不能使用顶级的 await:它必须在一个标记为 async 的函数中。使函数异步的语法很简单:只需将关键字`async`放在单词`function`之前，或者在箭头函数中，放在参数之前。你可能会认为这个语法是一个[生命](https://en.wikipedia.org/wiki/Immediately-invoked_function_expression):我们把函数放在括号里，然后立即调用它。

`fetch`方法返回一个承诺，但是通过使用`await`，我们可以忽略这一点，并编写代码，就好像它在承诺完成后返回结果一样。它实际上并没有在那个点阻塞，但是根据我们的控制流，我们可以把它当作阻塞来对待。

```
const result = await fetch("//freegeoip.app/json/"); 
```

Enter fullscreen mode Exit fullscreen mode

`result`本身并不保存数据:我们需要调用它的一个数据方法来检索它。我们将使用`json()`，它将内容解析为 JSON 数据并返回一个对象。这也返回了一个承诺，但是我们可以通过`await`忽略它。

```
const json = await result.json(); 
```

Enter fullscreen mode Exit fullscreen mode

关于`await`的一件非常好的事情是，如果它收到了不是承诺的东西，那也没关系。在这种情况下，它只是继续正常。这对[记忆](https://en.wikipedia.org/wiki/Memoization)来说太棒了。您可能会想到这样一种情况，在第一次加载时，从远程 API 获取一些数据，但是在本地缓存，并在将来调用时立即返回。有了 await，你不需要担心在`Promise.resolve()`中包装它，因为你可以返回原始对象，它会很好地处理它。类似地，在进行单元测试时，您可以很容易地模仿 API 调用。

此时，我们有了保存位置数据的对象。我们只需要国家代码，它被存储为`country_code`，因此我们可以设置`<select>`值。

```
document.getElementById("countries").value = json.country_code; 
```

Enter fullscreen mode Exit fullscreen mode

现在，您应该已经选择了您的国家。

渐进式改进的巧妙之处在于，如果它在旧浏览器中不工作，也没什么大不了的。然而，你可以通过使用`<script type="module">`来避免错误，它被旧的浏览器所忽略。所有支持模块的[浏览器也支持箭头函数，fetch 和 async/await。](https://caniuse.com/#feat=es6-module) 

```
<script type="module">
(async () => {
  const result = await fetch("//freegeoip.app/json/");
  const json = await result.json();
  document.getElementById("countries").value = json.country_code;
})();
</script> 
```

Enter fullscreen mode Exit fullscreen mode

在这个密码本里有一出戏[。或许试试虚拟专用网。](https://codepen.io/ascorbic/pen/BrQNXa/)
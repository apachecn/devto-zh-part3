# 自然 Pokedex 重构

> 原文：<https://dev.to/misnina/natural-pokedex-refactor-29f5>

我说的项目:[这里](https://misnina.github.io/gnosis-pokedex/)
原文:

[![misnina image](img/24ef28173812b0d9122a8fb420bec457.png)](/misnina) [## 小项目反思:自然 Pokedex (React)

### 尼娜 3 月 30 日 195 分钟阅读

#beginners #react #100daysofcode](/misnina/small-project-reflection-natural-pokedex-react-3oj8)

### 哪里出了问题？

我觉得有点不好意思！似乎我在上次的构建中非常错误地管理了 API，并且过度提取了 API。我得到了关于所有问题的非常翔实的评论，我开始尝试解决所有这些问题。

[![guico33 profile image](img/2bf50f73802c88db25c9e7e332620436.png) ](/guico33) [ guico33 ](/guico33) • [<time datetime="2019-03-31T10:14:43Z"> Mar 31 '19 </time>](https://dev.to/guico33/comment/9n23) 

你的应用程序的一个大问题是，当有人登陆你的页面时，你实际上向 poke api 发送了数百个请求(每个 pokemon 有两个请求)。这对于一个真正的应用程序来说是不可思议的，即使在这里，它也可能对 poke api 服务器造成损害。
你通常希望一个请求有多个口袋妖怪(像`"https://pokeapi.co/api/v2/pokemon?offset=20&limit=20"`，`offset`和`limit`参数对分页很有用)。

除此之外，守则本身还有改进的余地，其中包括:

*   变量`entries`，`filteredEntries`...不属于那里。在 react 中，如果 UI 依赖于某些数据，那么这些数据应该处于状态。现在你的应用程序可能看起来工作正常，但你实际上很幸运，UI 以某种方式与数据保持同步，因为只有状态或道具的变化才会触发 rerender (UI 更新)。在 react 应用程序的顶层，您通常会发现与 UI 无关的静态数据。

*   你应该遵循最小状态([reactjs.org/docs/thinking-in-react...](https://reactjs.org/docs/thinking-in-react.html))的原则，即在状态中存储最少量的数据，不存储可以计算的数据。例如，在这里，您将存储 state 中的条目，但不会存储过滤后的条目。相反，存储过滤器本身，并根据数据和过滤器计算过滤后的条目。这样更容易保持同步，否则您可能会更新状态中的过滤器，但忘记更新过滤的数据，等等。

*   js 中的承诺是异步解析的，这里不知道哪一个会最后解析(即使对 pokemon 199 的请求是最后发出的，响应可能会比 pokemon 1 更早返回)，因此`setMainLoading` fn 是有缺陷的。在这里，为了检查是否所有数据都已加载，您可以验证每个条目都已填充，或者在每个响应到来时增加一个计数器，这样当它达到 199 时，您就知道所有内容都已加载。

### 从(几乎)零开始

我意识到我需要从最顶层的父节点开始处理所有的信息，并向下传递属性，而不是让条目本身处理数据调用。令人尴尬的是，这个评论没有提到这样一个事实，即每次重新加载组件时，数据调用都是*重取的，也就是每次进行搜索时。我不知道我怎么会错过这个，我真的应该意识到让每个组件在搜索中重新呈现是愚蠢的，老实说只是浪费很多时间。*

所以让我们开始使用状态，并且只使用状态！我可以存储我从顶层收集的所有信息，**只**调用数据存储一次。offset 和 limit 对于将来的其他 api 来说是一个很好的提示，但不幸的是，这个 pokedex 不在 API 的排序列表中，为了按类型排序，我需要匹配我收集的数据，而不仅仅是当时显示的数据。不管怎样，我决定去掉类型，因为这是我从原始索引条目中得到的唯一属性，而不是物种索引，所以我也去掉了初始调用的总过滤器。

```
fetchPokemon = (name, id) => {
    axios.get(`https://pokeapi.co/api/v2/pokemon-species/${name.toLowerCase()}/`)
      .then( res => {
        const info = res.data;
        let eggSecond;
        info.egg_groups[1] ?
          eggSecond = info.egg_groups[1].name : eggSecond = null;

        let enEntries = info.flavor_text_entries.filter(entry => {
          return entry.language.name === 'en'
        });
        const data = {
          name: name,
          id: id,
          color: info.color.name,
          eggGroups: [
            info.egg_groups[0].name,
            eggSecond,
          ],
          genus: info.genera[2].genus,
          shape: info.shape.name,
          flavorText: enEntries[0].flavor_text,
        }

        this.setState(prevState => {
          const allEntryInfo = [...prevState.allEntryInfo, data];
          return {allEntryInfo};
        });

        this.setState({ counter : this.state.counter + 1 })
      }).catch (err => console.log);
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

### 取少

因为我们将自己存储数据并调用它，所以最好在放入数据之前对其进行格式化。关于状态的一件事是它们应该是不可变的，所以我做了一些计算来做到这一点，我为此做了一些努力，但我想我现在明白了。

当您设置 State 时，您可以将当前状态的快照作为变量传递给函数。因此，您将这个新状态设置为一个包含所有先前条目的数组，加上一个包含我们收集的数据的新状态，然后返回这个新设置以供 setState 接收。价差(...)在我第一次看到它时是一个棘手的问题，但它基本上意味着它可以是零或任何数量的项，这很方便！除此之外，我们还有另一个状态变量，它在每次通过一个条目时计数。

一旦我们有了数据，我们就可以继续实际渲染，并计算出*何时*进行渲染。因为 setState 每次被调用时都会重新呈现，所以我们实际上是在重新呈现一个空白页面 200 次，我不知道这是否有实际的性能问题，但这有点令人讨厌。为此，我们可以编辑 shouldComponentUpdate 特性。

```
 shouldComponentUpdate() {
    if (this.state.counter >= 199) {
      return true;
    } else {
      return false;
    } 
```

Enter fullscreen mode Exit fullscreen mode

如果计数器达到 199，那么组件应该在收到新的属性或状态时更新。很简单，现在让我们添加到我们的 fetchPokemon 调用中，来创建一个包含我们需要渲染的 JSX 元素的对象。

```
const entry = <Entry
          id={id}
          name={name}
          key={name}
          color={data.color}
          egg1={data.eggGroups[0]}
          egg2={data.eggGroups[1]}
          genus={data.genus}
          shape={data.shape}
          flavorText={data.flavorText}
          setSorting={this.setSorting}
        />;
      this.setState(prevState => ({
        allEntryComponents: {
          ...prevState.allEntryComponents,
          [name] : entry
        }
      })) 
```

Enter fullscreen mode Exit fullscreen mode

### 出了故障

我把这个对象的名字作为键是有原因的。原因是这样的。

[![](img/edbbad155ddf41c45995f190f4f8d4c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1je-jBNk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/23lygwnzfwqgvejqra9x.PNG)

这确实是真的，调用确实会乱序，并在不同的时间返回。这是一个混乱，但我不知道一种方法来迫使他们按顺序进行，这值得等待吗？我们可以稍后对它们进行排序，但是对于组件，我们需要对它们进行命名，以便知道哪个口袋妖怪存储在哪里，因为一旦它们被排序为一个元素，我们就无法访问它们的 id 号。

```
 sortBy(type, value) {
    const { allEntryInfo, allEntryComponents } = this.state;
    let sorted = [];
    if (type === 'all') {
      GPOKEDEX.forEach(pokemon => {
        sorted.push(allEntryComponents[pokemon]);
      });
    } else if (type === "eggGroups") {
      let typeSorted = allEntryInfo.filter(pokemon => {
        return pokemon[type][0] === value || pokemon[type][1] === value ;
       });
       typeSorted
         .sort((a, b) => a.id - b.id)
         .forEach(pokemon => {
           sorted.push(allEntryComponents[pokemon.name])
         });
    } else {
      let typeSorted = allEntryInfo.filter(pokemon => {
       return pokemon[type] === value;
      });
      typeSorted
        .sort((a, b) => a.id - b.id)
        .forEach(pokemon => {
          sorted.push(allEntryComponents[pokemon.name])
        });
    }
    return sorted;
  } 
```

Enter fullscreen mode Exit fullscreen mode

当我们按数字排序时，我们可以更明智地选择不同类型的搜索方式。首先，对所有人来说，我们只是从 GPOKEDEX 清单中获取订单，并将其推送到 allEntryComponents 中相应的名称。对于其他的，当设置 sortBy 时，我们取参数 sortType 和 sort value’，意思是我们想要做 sort by(‘color’，‘green’)，例如。这些也存储在顶级状态中，因此组件在被调用时会重新呈现，并在标题中显示您正在查看的内容。对于 egg 组，唯一的区别是我们或比较两个值，因为它们可以匹配第一或第二名。

当我们试图对组件进行排序时，我们首先使用信息位置，这样我们就可以获得内部数据。我们进行过滤，只获取类型与值集匹配的那些。我们有两个东西，sort 和 forEach。Sort 将每个条目与其他条目进行比较，并查看 id 是高于还是低于它们中的每一个，然后按正确的顺序返回它们。现在我们有了数据，按类型过滤，按 ID 排序，但是现在我们需要相关的组件！对于数组中的每个元素，我们再次将对应的带有 pokemon 名称的条目组件推送到排序后的数组中。

现在我们有进展了！

[![](img/4e10a2dce421b5ade5513e405c4f361a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--devTF_x1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xsew1j0ukq2r7fgwuogp.PNG)

### 等。

我遇到了一些问题，一个丢失的值，或者说一个对象中丢失的级别，所以它返回一个对象，React 真的不喜欢这样，但不能确定它在哪一行，所以我花了一些时间来弄清楚。因为我做了所有的造型，就这样了！我只需要重置一些东西，因为 react 就是 react，所以我可以重用很多组件。

因此，现在我们不再调用大量令人尴尬的 api 调用(每次重新加载时调用 400 次)，而是在页面上的整个时间内调用 200 次。此外，搜索速度非常快，因为他们已经加载！

### 接下来是什么？

我仍然无法理解 Redux，但我看到了一些关于排序信息的东西，可能在本地存储中，所以它甚至不需要调用 200，直到您清除您的数据。我需要对它做更多的研究。
# PKMN 蛋以接受的一面移动

> 原文：<https://dev.to/misnina/pkmn-egg-moves-with-a-side-of-acceptance-2a>

项目[在这里](https://misnina.github.io/egg-move-chainer/)

嘿，好久不见了！我很抱歉我没有得到回复我的帮助线程的精彩回复，但感觉我现在已经太迟了，所以我只想在这里说谢谢你。我一直在慢慢地做另一个小项目，中间时断时续。看起来我可能有点沮丧，但我会努力克服它。

## 什么是养殖链/为什么要进行这个项目

如果你已经知道口袋妖怪繁殖链，或者只是不关心细节，请跳过这一部分。

口袋妖怪可以互相繁殖，但是只有特定的群体可以这样做。口袋妖怪也有能力通过与不同物种的繁殖来学习特殊的动作，这些被称为鸡蛋动作。有时，需要几代口袋妖怪传承一招，才能为你想要的口袋妖怪找到合适的蛋群组合，这些被称为繁殖链。例如，Eevee 能够知道移动的愿望。Eevee 的饲养群(场)里没有一个口袋妖怪可以只靠升一级就学会许愿。那么 Eevee 是怎么学习愿望的呢？我们寻找一个口袋妖怪，他有第二个蛋群，像一个彩蛋，他学习愿望作为一个蛋移动，当在领域群中时，也在第二个仙女蛋群中。从那里，有多个仙女团体口袋妖怪可以学习愿望和繁殖与彩虹糖。由于漩涡和 Eevee 不能繁殖，我们得到了一个调解人。

因此，我们最终得到这样一个链:= >

**女轻佻** & **男轻佻**谁在 45 级时学会了许愿- >谁便拥有了**男轻佻**谁知道许愿出生谁就孕育了一个**女 Eevee** - > **Eevee 以许愿为始动的孩子。**

我在高中的笔记本上写下了繁殖链，计划对一只小口袋妖怪采取一系列令人满意的行动。所以我的想法将是一个工具，可以帮助这个创作。具体来说，我开始让程序为你制作链条，但我们会看到为什么没有发生。

## 目标

*   解析口袋妖怪信息，不要用 api，因为太多的口袋妖怪和数据(完成)
*   显示蛋以吸引人的方式移动(完成)
*   匹配口袋妖怪，适合多个鸡蛋移动组(完成)
*   在应用程序中制作繁殖链的能力(没有)

## 数据

首先，我们想弄清楚如何处理我们的数据。PokeApi 是可用的，但我检查了 700 多个口袋妖怪的细节，Api 在上市时非常复杂。PokeAPI 不只是有一个动作列表，它有来自游戏所有版本的数据，以及它是如何学习的。出于这个原因，我带了一个. json 文件，里面有我的朋友 Dakota 提供给我的更基本的口袋妖怪信息，她在她的项目中使用了这些信息。在这里，移动被分成在什么水平上学习的移动，和一个鸡蛋移动的列表，这对于我们将要做的所有数据解析是完美的。

## 工作之外的反应

我知道我必须进行大量的原始数据解析，所以我启动了一个普通的 javascript 文件，它与 React 没有任何关系。小到从包含关卡和移动的对象中获取移动集列表，大到比较多达四个数组以在学习特定移动的蛋组中找到口袋妖怪。虽然有一个很长的未使用的函数，但它最终大约有 250 行长。有很多要解析的！我学到的东西:设置对象！这是一组唯一的值，因此它有利于消除数组中的重复值，并通过对集合的内容执行 spread 操作将其转换回来。

我在未使用的函数中做得更多，但是也有一些轻微的递归发生。问题是对于小口袋妖怪来说，他们无法繁殖，所以被列在未被发现的传说组中，所以沿着他们的页面不会很有用，因为它充满了无法繁殖的口袋妖怪。因此，如果未被发现，它会寻找口袋妖怪的下一次进化，如果找不到，它只会返回一个空数组，这表明它会显示一条消息。

```
forEggMoves(move, pokemonName) {
  const relatedPokemonGroup1 = Search.byEggAndMove(move, Pokedex[pokemonName].egg_groups[0]);

  if (Pokedex[pokemonName].egg_groups[1]) {
    const relatedPokemonGroup2 = Search.byEggAndMove(move, Pokedex[pokemonName].egg_groups[1]);

    let combine = relatedPokemonGroup1.concat(relatedPokemonGroup2);
    return sortByID([...new Set(combine)]);

  } else if (Pokedex[pokemonName].egg_groups[0] === "Undiscovered") {
    if (Pokedex[pokemonName].evolutions) {
      let name = searchableName(Pokedex[pokemonName].evolutions[0][0]);
      return Search.forEggMoves(move, name);
    } else {
      return [];
    }
  } else {
    return sortByID(relatedPokemonGroup1);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 相对较轻的顶层构件

这一次，顶层组件的重量非常轻。它执行导入工作，但除此之外，它还让 pokemon 条目和 pokedex 使用从 react 路由器的 url 获得的 pokemon 名称来完成所有工作。

```
class App extends Component {
  render() {
    return (
      <div className="app flex column h-center">
        <Header
          searchByPokemon={this.searchByPokemon}
        />
        <Route
          path={`${this.props.route}/:pokemon`}
          render={({ match }) => {
            return <PKMNEntry
              key={`${Pokedex[match.params.pokemon].display_name}`}
              searchName={Pokedex[match.params.pokemon].name}
              name={Pokedex[match.params.pokemon].display_name}
              eggGroup1={Pokedex[match.params.pokemon].egg_groups[0]}
              eggGroup2={Pokedex[match.params.pokemon].egg_groups[1]}
              eggMoves={Pokedex[match.params.pokemon].egg_moves}
              learnedMoves={Pokedex[match.params.pokemon].moveset}
              searchEggMoves={Search.forEggMoves}
              searchByPokemon={this.searchByPokemon}
              isEggChain={Search.isEggChain}
              createMultiEggChain={Search.createMultiEggChain}
            />
          }
          } />
      </div>
    );
  }

  searchByPokemon = (pokemon) => {
    window.scrollTo(0, 0);
    this.props.history.push(`${searchableName(pokemon)}`);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个项目中的路由器实际上相当棘手！寻找口袋妖怪的主要方法是在页面顶部的选择栏。原来选择栏不喜欢在里面有链接，实际上只关于 onChange 事件。*(注意:把改变放在选择上，而不是选项上，firefox 可以接受选项，但是其他的都是很难学到的)*

为此，我们需要深入研究历史。从我的理解来看，react router 首先是一个历史包的包装器，但我们仍然可以对它做一些有力的工作。

```
import React from 'react';
import ReactDOM from 'react-dom';
import { Router } from 'react-router-dom';
import { createBrowserHistory } from 'history';
import './style.scss';

import App from './App';

let history = createBrowserHistory();

ReactDOM.render(
  <Router
    history={history}
    basename={process.env.PUBLIC_URL}
  >
    <App
      route={process.env.PUBLIC_URL}
      history={history}
    />
  </Router>
  , document.getElementById('root')); 
```

Enter fullscreen mode Exit fullscreen mode

根据我的测试，并试图让我的网站与 github 页面一起工作，保存它的历史和 basename 的路由器是无用的，没有帮助，但我仍然把它们放在那里。最初，我让 App 成为“/”处的“home”路线，以在 props 中保存历史，但这给 github pages 项目造成了很多冲突，因此我们只是将路线和历史作为 props 传递到 App 组件本身来使用它。路由器有点敏感，所以在大型的“分页”项目中，我需要更多地使用它。

## 显示信息

[![](img/10de1365bfdb3406a52a122b0a4bd99e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AoqY-5qy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ppz3oho947ln9b9th4k1.PNG)

设计最重要的部分是以快速的方式访问信息。我们可以放各种各样的信息，但最终这是一个关于获得移动集的工具，所以我们列出了学习的移动，然后显示口袋妖怪可以帮助学习鸡蛋移动。

对于这个项目，state 的实际唯一用途是一个实时更新程序，它通过右边的复选框在多个移动的蛋组中找到口袋妖怪。这样做的目的是用一个链获得你需要的尽可能多的鸡蛋移动，所以你可以用它来快速比较谁在哪个组。在更详细的鸡蛋移动列表中，用红色圈出的口袋妖怪需要一条链来完成鸡蛋移动。

[![](img/5e54c7e2936aa90fcf0b20711294ab1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BhHHPlXY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ddf56rhpi8b4dt2gi43h.PNG)

## 无链器

我不得不承认，我达到了一个可能超出我能力的地步。我在这个项目上已经坐了一个多星期，试图找到一个解决方案，但我找不到任何对我有用的东西。我做了一个函数，可以返回移动的最快路径，但通常我们需要考虑我们拥有的特定口袋妖怪和多个移动集。要做到这一点，我需要停止输入，并在链的每个阶段获得用户反馈。在这一点上，我认为更容易点击口袋妖怪并进入页面查看信息，然后在链的每个阶段填写论坛，并不得不撤销和重做每个步骤。也很难尝试更换链条的中间部分。因为这个原因，我决定不做链匠了。计算所有的链是一个可怕的任务，很容易陷入一个循环，我认为我还没有准备好，并且另一个选项对它应该是一个简单的显示来说很麻烦。

## 接下来是什么

我真的不知道！我有几个其他的项目，但我并不喜欢它们。我还没有找到工作，但令人兴奋的消息是我得到了一个口袋妖怪粉丝网站的志愿者职位，我经常帮助他们设计和其他更大的计划，所以这真的很酷，希望我能在那里学到很多东西。再次感谢你留下的我没有回复的任何评论，在这令人尴尬的几天后，我想我会很快振作起来的！
# 关于 reactjs、Apollo 和 GraphQL 的爱和感情第 1 部分

> 原文：<https://dev.to/lampewebdev/love-and-affection-about-reactjs-apollo-and-graphql-part-1-51fg>

**爱情**💟还有**暴怒**😠可以靠得这么近。

这是我在与 Apollo、GraphQL 和 reactjs 合作第一个项目时的一系列感受。此外，您将**学习**如何设置它！

我一直有这种**爱**💟还有**激情**💪新的闪亮的东西。第一次看到 GraphQL 也是这样。我想学习这个新的闪亮的东西🤩！这是一个多云的雨天🌧星期六，所以我对自己说，这是开始一个新项目的**完美**时间。

## 设置

我从我的老好朋友`create-react-app`开始。

```
 npx create-react-app react-apollo

#or

yarn create react-app react-apollo 
```

那很容易。你有一个反应前端。现在改变目录并启动前端。

```
cd react-apollo

yarn start

#or

npm start 
```

让我们将 Apollo 和 GraphQL 添加到前端。
在谷歌上搜索了一下，我找到了`apollo-boost`。
`README.md`看起来不错，所以我安装了它。

```
npm I apollo-boost graphql react-apollo -S

#or

yarn add apollo-boost graphql react-apollo 
```

好的，这个看起来**不错**。只是一个 boost 包，基本的 GraphQL 包和 react 集成包。干净！

### 创建阿波罗客户端

让我们设置它，也就是让我们创建一个 Apollo 客户端。于是`README.md`建议了下面这段代码。

```
import React from 'react';
import { render } from 'react-dom';
import ApolloClient from 'apollo-boost';
import { ApolloProvider } from 'react-apollo';

// Pass your GraphQL endpoint to uri
const client = new ApolloClient({ uri: 'https://nx9zvp49q7.lp.gql.zone/graphql' });

const ApolloApp = AppComponent => (
  <ApolloProvider client={client}>
    <AppComponent />
  </ApolloProvider> );

render(ApolloApp(App), document.getElementById('root')); 
```

通读这段代码后，我和`eslint`发现了三个问题🤔。`App`没有被导入，`render()`也被渲染到根目录。好了，这里没有用`create-react-app`复制和粘贴，我们现在还没有 Apollo 的端点😩。谷歌再次出手相救🙏。搜索`open graphql endpoints`我找到了口袋妖怪的一个端点！很好。URI 是`https://graphql-pokemon.now.sh`。

好了，问题一解决了。让我们开始改变我们的`create-react-app`。

首先，用下面的代码创建一个文件`src/apollo.jsx`。

```
import React from 'react';
import ApolloClient from 'apollo-boost';
import { ApolloProvider } from 'react-apollo';

// Pass your GraphQL endpoint to uri
const client = new ApolloClient({ uri: 'https://graphql-pokemon.now.sh/' });

export default AppComponent => (
  <ApolloProvider client={client}>
    <AppComponent />
  </ApolloProvider> ); 
```

现在修改 index.js 文件，使其看起来像这样:

```
import { render } from 'react-dom';
import './index.css';
import ApolloApp from './apollo';
import App from './App';
import * as serviceWorker from './serviceWorker';

render(ApolloApp(App), document.getElementById('root'));
serviceWorker.unregister(); 
```

我们导入了`ApolloApp`并将`App`包装在`ApolloApp`中。是的，命名不是最棒的。保存并检查是否一切都还在工作！看看终端和`localhost:3000`是否开心😊。

### 我们先查询一下

pokemon GraphQL 端点非常有限。我们只能按姓名或 id 查询😯。
创建一个文件`src/PokemonByName.js`并添加以下代码。

```
import React from 'react';
import { gql } from 'apollo-boost';
import { Query } from 'react-apollo';

const GET_POKEMON = gql`
query pokemon($name: String!)  {
  pokemon(name: $name) {
    id
    number
    name
  }
}
`
export default ({ name }) => (
  <Query query={GET_POKEMON} variables={{ name }}>
    {({ loading, error, data }) => {
      if (loading) return null;
      if (error) return `Error!: ${error}`;
      if(!!data.pokemon){
        return (
          <div>
            {data.pokemon.name} {data.pokemon.number}
          </div>
        );
      }
      return (
        <div>
          {name} not Found
        </div>
      );
    }}
  </Query> ); 
```

😵呸，这么多代码！我们正在创建一个名为 GET_POKEMON 的查询。`gql`是一种模板语言。`$name`是`String`类型的变量，并且是必需的。因为有一个`!`，所以需要。该查询将返回一个包含内部 id、Pokedex 编号和 pokemon 名称的对象。我们还创建了一个组件，在那里我们实际发送 GraphQL 查询。我们把口袋妖怪的名字作为道具。如果它还在加载，我们就不渲染任何东西。如果我们得到一个错误，我们会显示一个错误，然后我们会检查我们是否真的找到了一个口袋妖怪。如果是，则呈现名称和编号，如果不是，则呈现一条消息，表明没有找到具有该名称的口袋妖怪。

让我们真的在屏幕上看到一些东西！
所以改文件`src/app.js`。到下面。

```
import React, { Fragment } from 'react';
import PokemonByName from './PokemonByName';

import './App.css';

const App = () => (
  <Fragment>
    <PokemonByName name={'pikachu'} />
    <PokemonByName name={'charmander'} />
    <PokemonByName name={'bulbasaur'} />
  </Fragment> )

export default App; 
```

我们正在导入我们的新组件，然后我们将它添加到我们的应用程序组件三次。现在只需保存一切，看看你的浏览器如何在你的屏幕上呈现口袋妖怪！美丽的😍！

因此，现在我们知道如何将 GraphQL 端点集成到我们的应用程序中。

下次我们将尝试创建我们自己的 GraphQL 端点！我不知道那会(或不会)怎么样。

**感谢阅读！**

说你好！[insta gram](https://www.instagram.com/lampewebdev/)|[Twitter](https://twitter.com/lampewebdev)|[LinkedIn](https://www.linkedin.com/in/michael-lazarski-25725a87)|[Medium](https://medium.com/@lampewebdevelopment)
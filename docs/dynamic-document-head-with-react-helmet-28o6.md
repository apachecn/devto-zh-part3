# 带反应头盔动态文档头

> 原文：<https://dev.to/guimg/dynamic-document-head-with-react-helmet-28o6>

头盔是一个 React 组件，允许我们管理对文档头的所有更改。例如，假设我们正在做一个社交网络。在主页面上，我们可能希望网站标题类似于*“我的社交网络”*，但如果我们去一个用户的个人资料标题应该是这样的:*“用户名/Id -我的社交网络”*。

# 用法举例

```
import React from 'react';
import { Helmet } from 'react-helmet';
import { Switch, Route } from 'react-router-dom';

import PageOne from 'containers/PageOne';
import PageTwo from 'containers/PageTwo';

const App = () => (
  <div className="app">
    <Helmet>
      My App
      <meta charSet="utf-8" />
      <meta name="description" content="A React.js application" />
    </Helmet>
    <Switch>
      <Route exact path="/" component={PageOne} />
      <Route path="/page-2" component={PageTwo} />
    </Switch>
  </div> );

export default App; 
```

这里有一个非常基础的应用程序的例子。正如我们所见，页面的标题将是*我的应用*。但是这个 app 的路线不一样。假设我们想要改变`/page-2`路线的标题和描述，但是我们想要保持字符集编码。我们可以很容易地这样做:

```
import React from 'react';
import { Helmet } from 'react-helmet';

export default class PageTwo extends React.Component {
  <div className="page-two">
    <Helmet>
      Page 2
      <meta name="description" content="This is a different description for this route." />
    </Helmet>
    <h1>Page 2</h1>
  </div> ); 
```

因为我们只在第二个组件中定义了`title`和`description`，所以它们将是应用程序本身覆盖的唯一两个值。字符集不会改变，因为我们没有定义一个新的。

这个例子的输出标题是:**第 2 页**

# 使用模板和默认标题

我们可以更进一步，利用头盔提供给我们的特性。例如`titleTemplate`和`defaultTitle`。我们将重复上面的例子，但现在我们将在应用程序的头部建立这两个道具。

```
import React from 'react';
import { Helmet } from 'react-helmet';

const App = () => (
  <div className="app">
    <Helmet titleTemplate="%s - My App" defaultTitle="My App">
      <meta name="description" content="A React.js aapplication" />
    </Helmet>
    <h1>My App</h1>
  </div> );

export default App; 
```

在这一点上，如果我们改变到另一个组件，我们不改变标题，因为默认标题是**我的应用**这将是输出。让我们像以前一样用`PageTwo`组件来改变它。

```
import React from 'react';
import { Helmet } from 'react-helmet';

export default class PageTwo extends React.Component {
  <div className="page-two">
    <Helmet>
      Page 2
      <meta name="description" content="This is a different description for this route." />
    </Helmet>
    <h1>Page 2</h1>
  </div> ); 
```

现在输出标题将是**第 2 页-我的应用程序**，因为我们建立了模板。`%s`被我们传递给 title 标签的字符串所替代。

这是一个非常简单的教程，但我认为它对那些不知道这个组件的人很有帮助。希望你喜欢它！如果你有任何疑问，请在下面的评论中留下。你可以在他们的 [Github](https://github.com/nfl/react-helmet) 中阅读整个文档。
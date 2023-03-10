# 用于通用布线的反应挂钩

> 原文：<https://dev.to/revskill10/a-react-hook-for-universal-routing-6dk>

我正在使用`@reach/router`，喜欢它的简单。
但是我喜欢使用 React Hook 来获取页面参数和查询字符串，所以我为它做了一个简单的钩子。

首先，我们需要一个用于页面组件的`pageContext`。

`pageContext.ts` :

```
import React, { createContext } from 'react';

const pageContext = createContext({} as any);

export const withPageContext = (Component: any) => (props: any) => {
  return (
    <pageContext.Provider value={{...props}}>
      <Component {...props} />
    </pageContext.Provider>
  );
};

export default pageContext; 
```

这还包括一个将`props`注入 pageComponent 的 HOC，以便稍后使用`useContext` hook。

其次，我们需要将这个`pageContext.Provider`包装到所有的路由组件:
:`Route.tsx`:

```
import React, { 
  FunctionComponent 
} from 'react';
import { 
  RouteComponentProps 
} from '@reach/router';
import {
  LoadableComponent
} from '@loadable/component';
import { withPageContext } from './pageContext';

type Props = { component: FunctionComponent | LoadableComponent<{}> } & RouteComponentProps;

const Route: FunctionComponent<Props> = ({ component, ...rest }) => {
  const Component = withPageContext(component);
  return (
    <Component {...rest} />
  );
};

export default Route; 
```

最后，我们需要一个`useRouter`钩子:
`useRouter.ts`

```
import pageContext from './pageContext';
import { useContext } from 'react';
import { parse, parseUrl } from 'query-string';
const isServer = typeof(window) === 'undefined';

const useRouter = () => {
  const { location, ...rest } = useContext(pageContext);
  const queryParams = isServer ?  
    parseUrl(location.pathname).query : 
    parse(location.search);

  const query = {
    ...queryParams,
    ...rest,
  };

  return { query };
};

export default useRouter; 
```

在这里，我将所有的`props`和`query string`映射到一个由`useRouter`返回的`query`属性中，我们完成了！

# 用法:

首先，使用我们新的`Route`组件声明您的路线:

`App.tsx` :

```
import React from 'react';
import Route from './Route';
import { 
  Router 
} from '@reach/router';
import loadable from '@loadable/component';

const User = loadable(() => import('components/User'));

const App = () => {
  return (
    <Router>      
      <Route component={User} path="/users/:userId" />
    </Router>
  );
};

export default App; 
```

其次，在我们的`User`组件中使用`useRouter`:

`User.tsx` :

```
import React from 'react';

const User = () => {
  const { query } = useRouter();
  const { userId } = query;
  return <div>You requested {userId}</div>; }; 
```

## 灵感:

这是受`react-router`和`nextjs`的启发。
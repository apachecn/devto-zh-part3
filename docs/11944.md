# 如何在 React 应用上添加动态标题

> 原文：<https://dev.to/luispa/how-to-add-a-dynamic-title-on-your-react-app----3l0j>

在这篇文章中，我将向您展示如何创建一个简单的组件来在您的 web 应用程序中添加动态标题行为。

这里你有一个回购和一个适用的例子: [GitHub 回购](https://github.com/LuisPaGarcia/react-dynamic-title)

## 简历

1.  添加`react-helmet`依赖项。
2.  为标题编写动态组件。
3.  将动态组件添加到路由器或页面中。

### 添加`react-helmet`依赖项。

如果你使用`yarn`

```
$ yarn add react-helmet 
```

如果你使用`npm`

```
$ npm i react-helmet 
```

### 写动态成分为标题。

你可以为这个例子写一个独立的组件，像这样:

```
// TitleComponent.jsx

import React from 'react';
import Helmet from 'react-helmet';

const TitleComponent = ({ title }) => {
    var defaultTitle = '⚛️ app';
    return (
        <Helmet>
            {title ? title : defaultTitle}
        </Helmet>
    );
};

export { TitleComponent }; 
```

在这个例子中，我们只是写了一个独立的组件，它可以接收一个标题，如果你不发送一个`prop`作为标题，这个标题将是默认标题。

## 为您的路线添加动态组件。

我们有多种方法将此组件添加到您的应用程序中，这主要取决于您对路由的决定(如果您使用 Gatsby 或 Next.js，您可以 ovoid 配置路由器，但如果您使用 create-react-app 或 react boiler 模板，您可以将其添加到您的路由器中。

### 将此组件添加到您的路线中(使用路由器):

```
 // routes.js

import React from 'react';
import { Route } from 'react-router';
import { TitleComponent } from './TitleComponent.jsx';

// withTitle function
const withTitle = ({ component: Component, title }) => {
    return class Title extends Component {
        render() {
            return (
                <React.Fragment>
                    
                    <Component {...this.props} />
                </React.Fragment>
            );
        }
    };
};

// Example pages 
const Index = () => <h1>This is the IndexComponent!</h1>;
const Persons = () => <h1>This is the PersonsComponent!</h1>;
const Dogs = () => <h1>This is the DogsComponent!</h1>;
const Food = () => <h1>This is the FoodComponent!</h1>;

// Adding title
const IndexComponent = withTitle({ component: Index, title: 'Index' });
const PersonsComponent = withTitle({ component: Persons, title: '🧠 Persons' });
const DogsComponent = withTitle({ component: Dogs, title: '🐶 Dogs' });
const FoodComponent = withTitle({ component: Food, title: '🌮 Food' });

// Your router
export default (
    <Route>
        <Route path="/" component={IndexComponent} />
        <Route path="/persons" component={PersonsComponent} />
        <Route path="/dogs" component={DogsComponent} />
        <Route path="/food" component={FoodComponent} />
    </Route>
); 
```

### 将此组件添加到您的页面(使用 Next.js、Gatsby、After.js):

使用`withTitle`功能:

```
// pages/pageOne.jsx

import React from 'react';
import { TitleComponent } from './TitleComponent.jsx';

// withTitle function
const withTitle = ({ component: Component, title }) => {
    return class Title extends Component {
        render() {
            return (
                <React.Fragment>
                    
                    <Component {...this.props} />
                </React.Fragment>
            );
        }
    };
};

const PageOne = () => (
    <React.Fragment>
        <h1> Page 1 </h1>
        // Some content...
    </React.Fragment>
);

export default withTitle({ component: PageOne, title: 'Page One!' }); 
```

将``直接添加到您的页面:

```
// pages/pageOne.jsx

import React from 'react';
import { TitleComponent } from './TitleComponent.jsx';

const PageOne = () => (
    <React.Fragment>
        
        <h1> Page 1 </h1>
        // Some content...
    </React.Fragment>
);

export default PageOne; 
```

这里你有一个回购和一个适用的例子: [GitHub 回购](https://github.com/LuisPaGarcia/react-dynamic-title)

仅此而已。感谢阅读和快乐编码！
# 如何测试 React 高阶组件(加 Redux)

> 原文：<https://dev.to/austinnchristensen/how-to-test-react-higher-order-components-plus-redux-3l9j>

当实践适当的测试驱动开发时，意识到你不知道如何测试你需要写的代码可能是一个主要的障碍。我自己也遇到了这个问题，搜索了谷歌，根本找不到关于这个话题的任何资源，所以我决定分享一下我在路上学到的东西。

根据 Redux 状态，这个特定的 HOC 用于检查用户是否通过了身份验证，如果没有通过身份验证，则将用户重定向到登录路由。

## 写作第一关考试

我们马上就知道了一些关于我们将要测试的组件以及我们的测试需要做什么的事情。

1.  它连接到 Redux，所以它需要一个模拟商店
2.  我们需要一个测试组件
3.  我们可以*大概*只是浅层渲染这个组件。

知道了这么多信息，我们可以编写这个测试:

```
import React from 'react';
import { shallow } from 'enzyme';
import configureStore from 'redux-mock-store';
import withAuthentication from './withAuthentication';

const mockStore = configureStore();
test('redirects to Login when isAuthenticated is false', () => {
    const Component = () => {
        return <h1>test values</h1>;
    };
    const ConditionalComponent = withAuthentication(Component);
    const store = mockStore({
        auth: {
            isAuthenticated: false
        }
    });
    const wrapper = shallow(<ConditionalComponent store={store} />).dive();
    const redirectComponent = wrapper.filterWhere(
        (item) => item.prop('to') === '/' && item.name() === 'Redirect'
    );
    expect(redirectComponent).toHaveLength(1);
}); 
```

### 什么鬼东西。潜水()！？

好问题！

这个概念不止一次让我犯错，但是。dive()实际上是在说，“嘿，我们有一个包装器，让我们更深入地研究它的子组件，然后包装器将引用那个组件……”

因此，在常规的 shallow()上，我们正在进入我们的 HOC 的顶层，但是根据定义，HOC 是一个返回另一个组件的函数(或组件)，所以我们需要*深入到内部组件！*

一旦我们实施特设委员会，这一切都将变得更有意义。

[潜水文档可以在这里找到](https://airbnb.io/enzyme/docs/api/ShallowWrapper/dive.html)

### 其余测试

既然我们已经过去了。dive()，expect()语句看起来也很奇怪。据我所知，这是测试一个返回函数的函数式 HOC 的唯一方法。将会有一个子元素，其属性为='/'，名称为 Redirect，看起来应该很熟悉，因为这只是描述组件的另一种方式:

```
<Redirect to='/' /> 
```

## HOC 为首次测试

编写这个测试“迫使”我们编写大量的代码，以便通过第一个测试，包括将我们的 Redux 存储连接到我们的组件。

让我们从编写函数(' s)的框架开始:

```
const withAuthentication = (Component) => {
    const wrapped = ({ isAuthenticated, ...rest }) => (
        isAuthenticated ? null: <Redirect to='/login' />
    );
    return wrapped;
}; 
```

一旦我们有了一个框架，我们就可以添加所有的 Redux 状态管理:

```
const mapStateToProps = (state) => ({
    isAuthenticated: state.auth.isAuthenticated
});

const composedWithAuthentication = compose(
    connect(mapStateToProps, null),
    withAuthentication
);
export default composedWithAuthentication; 
```

### 作曲

虽然在这种情况下 compose 不是完全必要的，但是一旦你开始向一个应用程序添加更多的 HOC，就可以非常方便地用逗号分隔这些函数，并让括号和所有东西为你管理。

### 分派

因为这个 HOC 不需要分派，所以最简单的方法就是不实现这个函数，将 null 传递给 connect()函数，然后在 HOC 的 prop spread 操作符中处理不传递的问题。

```
// By naming dispatch here, we're removing it ...rest,
// which will prevent it from being passed down to
// a component once we implement that feature
const wrapped = ({ isAuthenticated, dispatch, ...rest }) => (
    isAuthenticated ? null : <Redirect to='/login' />
); 
```

至此，我们的测试应该通过了。

## 写第二个测试

我们的第二个测试看起来和第一个很像，除了几行和一个额外的。潜水()

```
test('displays provided Component when isAuthenticated is true', () => {
    const Component = () => {
        return <h1>test values</h1>;
    };
    const ConditionalComponent = withAuthentication(Component);
    const store = mockStore({
        auth: {
            isAuthenticated: true
        }
    });
    const wrapper = shallow(<ConditionalComponent store={store} />).dive().dive();
    expect(wrapper.find('h1')).toHaveLength(1);
}); 
```

号外。dive()在这里是必要的，因为我们不仅需要进入第二个函数，而且还需要进入它所呈现的实际组件，以便测试它是否确实被呈现到 DOM 中。

## 进行第二次测试通过

利用我们已经拥有的东西，通过这个测试是相当容易的，因为我们只需要更新我们的三元语句，看起来像这样:

```
isAuthenticated ? <Component {...rest} /> : <Redirect to='/login' /> 
```

## 添加属性类型

正确测试组件的最后一步是向函数中添加属性类型，如下所示:

```
const withAuthentication = (Component) => {
    const wrapped = ({ isAuthenticated, dispatch, ...rest }) => (
        isAuthenticated ? <Component {...rest} /> : <Redirect to='/login' />
    );
    wrapped.propTypes = {
        isAuthenticated: PropTypes.bool,
        dispatch: PropTypes.func
    };
    return wrapped;
};

withAuthentication.propTypes = {
    Component: PropTypes.element
}; 
```

# 最终文件

最后，我们的 withAuthentication.test.js 文件将如下所示:
*注意:*我添加了一个 describe 语句来包装测试，并将 mockStore 上移。

```
// withAuthentication.test.js
import React from 'react';
import { shallow } from 'enzyme';
import configureStore from 'redux-mock-store';
import withAuthentication from './withAuthentication';

const mockStore = configureStore();
describe('withAuthentication', () => {
    test('redirects to Login when isAuthenticated is false', () => {
        const Component = () => {
            return <h1>test values</h1>;
        };
        const ConditionalComponent = withAuthentication(Component);
        const store = mockStore({
            auth: {
                isAuthenticated: false
            }
        });
        const wrapper = shallow(<ConditionalComponent store={store} />).dive();
        const redirectComponent = wrapper.filterWhere(
            (item) => item.prop('to') === '/' && item.name() === 'Redirect'
        );
        expect(redirectComponent).toHaveLength(1);
    });

    test('displays provided Component when isAuthenticated is true', () => {
        const Component = () => {
            return <h1>test values</h1>;
        };
        const ConditionalComponent = withAuthentication(Component);
        const store = mockStore({
            auth: {
                isAuthenticated: true
            }
        });
        const wrapper = shallow(<ConditionalComponent store={store} />).dive().dive();
        expect(wrapper.find('h1')).toHaveLength(1);
    });
}); 
```

我们最终的组件将会是这样的:

```
// withAuthentication.js
import React from 'react';
import { Redirect } from 'react-router-dom';
import { connect } from 'react-redux';
import { compose } from 'redux';
import PropTypes from 'prop-types';

const withAuthentication = (Component) => {
    const wrapped = ({ isAuthenticated, dispatch, ...rest }) => (
        isAuthenticated ? <Component {...rest} /> : <Redirect to='/login' />
    );
    wrapped.propTypes = {
        isAuthenticated: PropTypes.bool,
        dispatch: PropTypes.func
    };
    return wrapped;
};

withAuthentication.propTypes = {
    Component: PropTypes.element
};

const mapStateToProps = (state) => ({
    isAuthenticated: state.auth.isAuthenticated
});

const composedWithAuthentication = compose(
    connect(mapStateToProps, null),
    withAuthentication
);
export default composedWithAuthentication; 
```

## 总结

一旦你理解了一个 HOC 的测试是什么样子，整个过程就会变得简单很多。希望这篇文章有所帮助，如果你有任何问题或反馈，请让我知道！
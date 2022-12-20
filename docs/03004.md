# 全栈 JavaScript 中的 Redux

> 原文：<https://dev.to/mxoliver/redux-within-full-stack-javascript-4p7j>

大约一个月前，我开始了我的第一个全栈 JavaScript 应用程序的第一次冲刺。以前，我曾在单页面应用程序中使用 React，并使用 Node.js 和 Express 构建过服务器端应用程序，使用 EJS 模板创建视图，但是除了对流程有一个理论上的理解之外，我从未实际体验过客户端和服务器如何连接和通信。进入 Pocket Therabuddy，这是一个全栈 NERD (node，express，react，database)应用程序和心理健康工具，旨在将更好的数据放在用户手中，并意识到要控制他们的行为如何以积极和消极的方式影响他们的情绪。

Pocket Therabuddy 是我为期九个月的训练营的顶点项目。对我们的顶点的唯一规定是，我们必须在整个规划和构建过程中遵循敏捷方法，否则我们在我们的技术堆栈、我们想要构建什么以及我们想要如何构建它方面有完整的自由范围。从我正式毕业到现在已经有几个星期了，但是这个项目是我打算长期继续下去的事情。在我当老师的时候，现在作为一名开发人员，我总是强调以项目为中心的学习，因为它让你接触到你从未想过要学习的问题和主题。这让我想到了本文的关键，在构建这个项目的过程中，我学到了 Redux 在全栈 JavaScript 应用程序中的作用。

简而言之，Redux 是一个状态管理工具，但在跟踪状态时，它可以负责相当多的重要逻辑过程，包括与服务器通信。

```
import { authHeader } from '../helpers/authHeader';

export const habitService = {
    addHabit,
    getHistory
}

async function addHabit(habit) {

    const requestOptions = {
        method: 'POST',
        headers: {'Content-Type': 'application/json'},
        body: JSON.stringify({habit: habit})
    }

    const response = await fetch(`${process.env.REACT_APP_API_URL}/api/habittracker/add_habit`, requestOptions);
    localStorage.setItem('habits', JSON.stringify(response));
    const res = handleResponse(response);
    return res;
}

async function getHistory(user) {
    const requestOptions = {
        method: 'GET',
        headers: authHeader()
    }

    const response = await fetch(`${process.env.REACT_APP_API_URL}/api/habittracker/${user.id}/history`, requestOptions);
    const res = handleResponse(response);
    return res;
} 
```

这是一个代码片段，包含对服务器的 GET 请求和 POST 请求。总而言之，有趣的部分是 redux 如何处理这些请求。

```
 function fetchHistory(id){
    return dispatch => {
        dispatch(request());

        habitService.getHistory(id).then((habits) => {
            dispatch(success(habits));

        },
        error => {
            request(failure(error.toString()));
            request(alertActions.error(error.toString()));
        })
    }

    function request() { return { type: habitConstants.HABIT_HISTORY_REQUEST }};
    function success(habits) { return { type: habitConstants.HABIT_HISTORY_SUCCESS, habits }};
    function failure(error) { return { type: habitConstants.HABIT_HISTORY_FAILURE, error }}; 
} 
```

这是上面 GET 请求的动作创建者，这个动作可以在任何带有`habitActions.getHistory(id)`的 react 组件中调用，它将分派请求，然后调用前面 GET 请求的`habitService.getHistory`函数。如果请求成功，它将把来自服务器的 JSON 响应传递给`dispatch(success(habits))`,后者可以在组件中作为 this.props.habits 访问。失败时，它将发送错误。

这个过程的最后一部分是如何在组件中访问 redux 动作创建者和响应。Redux 有一个`mapStateToProps`方法和一个`dispatchState`方法，尽管我个人发现`mapStateToProps`几乎可以用于任何事情。

```
 function mapStateToProps(state) {
    const { user } = state.authentication;
    const { habits } = state.fetchHabits;
    return {
        user,
        habits
    }
} 
```

这里的函数将获取当前登录的用户(身份验证，fetchHabits 是处理这些状态的 redux reducers 的名称)以及从服务器请求返回的任何习惯。这个函数应该位于你的 React 类之外，在上面或者下面。

```
 componentDidMount(){        
   const { dispatch, user } = this.props;           
   dispatch(habitActions.fetchHistory(user.response));    
} 
```

这是调度调用，它将触发我们上面看到的动作创建者和服务请求，这将把`this.props.habits`设置为从服务器返回的数据。

最后一个关键但非常简单的部分是 npm 包`react-redux`,它将为您提供连接功能

`import { connect } from 'react-redux'`

然后在组件的底部，将 redux `mapStateToProps`函数连接到类组件的名称，并像往常一样导出它。

```
 const HabitHistory = connect(mapStateToProps)(ConnectedHabitHistory);
export default HabitHistory; 
```

如果你想亲自体验一下 Pocket Therabuddy，它已经部署在这里[了](https://pocket-therabuddy.herokuapp.com)，你可以在这里找到 GitHub repo [，里面有更多关于在你的本地服务器上运行它的信息和即将推出的功能。请继续关注关于 d3 和使用 React 的数据可视化的文章(我在制作 Pocket Therabuddy 时学到的另一件事)。](https://github.com/MxOliver/Pocket-Therabuddy)

— MxOliver
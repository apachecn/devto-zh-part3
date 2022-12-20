# React 路由器——如何将历史对象传递给组件

> 原文：<https://dev.to/kozakrisz/react-router---how-to-pass-history-object-to-a-component-3l0j>

我正在进行一个项目，有一段时间我没有使用 React。我必须解决这种情况:我有一个组件，它被深深地嵌入到一个路由组件中(这个路由在一个浏览器路由器组件下)。我需要使用路由器历史中的历史对象。我开始记起我用旧 React 路由器和间接历史操纵的糟糕冒险。

所以，我心情不好，打开官方文档的时候。当我能在 3 分钟内解决我的任务时，这是一个很大的惊喜。

有一个神奇的高阶组件，名字叫 **withRouter** 。它也发生在 [react-router-dom](https://www.npmjs.com/package/react-router-dom) 包中。在它的帮助下，您可以将整个历史对象作为属性传递给组件。它们包含一些关于路由的有用数据。

```
import React, { PureComponent } from 'react';
import { withRouter } from 'react-router-dom';

class PassToMeMyRouterHistory extends PureComponent {

  redirectToHome = () => {
   const { history } = this.props;
   if(history) history.push('/home');
  }

  render() {
    const { history } = this.props;

    return(
      (history)
      ?
        <div onClick={this.redirectToHome}>You can go to Home 🥳</div>
      :
        <div>Oh, we did not get pathname! 🤔</div>
    );
  }

}

export default withRouter(PassToMeMyRouterHistory); 
```

Enter fullscreen mode Exit fullscreen mode

正如你在上面看到的，在我们让 *withRouter* 组件完成它的工作后，我们可以很容易地获得与历史相关的必要数据和功能。历史对象有几个属性，你可以在[文档](https://reacttraining.com/react-router/web/api/history)中查看。

赞美诗:如果位置发生变化，当您需要此操作时，需要在组件中处理重新呈现逻辑。 *withRouter* 为您提供支持，但不订阅更改！
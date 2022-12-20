# Dojo 路由器简介

> 原文：<https://dev.to/odoenet/intro-to-the-dojo-router-1hc0>

当我们[从 dojo cli 查看模板应用程序](https://learn-dojo.com/dojo-cli-template-app/)时，我们快速浏览了一下 Dojo 路由器。模板应用程序提供了您需要了解的关于 Dojo 路由器的几乎所有内容。但是让我们更深入地看看[路由](https://dojo.io/tutorials/1030_routing/)。

## 定义路线

模板应用程序很好地提供了一种清晰的方式来配置您的路线。

```
// src/routes.ts
export default [
  {
    path: "home",
    outlet: "home",
    defaultRoute: true
  },
  {
    path: "about",
    outlet: "about"
  },
  {
    path: "profile/{username}",
    outlet: "profile"
  }
]; 
```

Enter fullscreen mode Exit fullscreen mode

路线被定义为对象的数组。每个 Route 对象都有一个 [RouteConfig 接口](https://github.com/dojo/framework/blob/master/src/routing/interfaces.d.ts)，其属性可以由您定义。在上面的代码片段中，我做了一个改动。我已经将个人资料路线的路径设置为**个人资料/{用户名}** 。这意味着我需要为该路由定义一个参数，我们一会儿会讲到，但首先让我们看看路由配置的选项。

```
// dojo/framework/src/routing/interfaces.d.ts
export interface RouteConfig {
  path: string;
  outlet: string;
  children?: RouteConfig[];
  defaultParams?: Params;
  defaultRoute?: boolean;
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是使用 TypeScript 和 Dojo 的美妙之处，您可以查看代码的类型和接口，并使用它们作为您应该如何使用这些工具的指南。唯一需要的属性是路径和出口。我们在配置中看到定义的另一个属性是 defaultRoute，您可能已经猜到它是应用程序的默认路由。谁说给事物命名很难？！

如果有嵌套路由，将使用 children 属性。您还可以定义一些默认参数，如果您有一个依赖于参数的路径，并且您的路径需要它们正确运行，这将非常有用。

## 出口

我们需要查看的第一部分路由是[出口](https://dojo.io/docs/index.html#doc--dojo__framework__v4_0_0__src__routing__README_md___outlets)。Outlet 是一个高阶组件，您可以使用它来包装属于指定路线一部分的小部件。

```
// src/App.ts
...
export default class App extends WidgetBase {
  protected render() {
    return v("div", { classes: [css.root] }, [
      w(Menu, {}),
      v("div", [
        // Outlet is where routes go
        // the decide which widgets
        // match each route
        w(Outlet, {
          key: "home",
          id: "home",
          renderer: () => w(Home, {})
        }),
        w(Outlet, {
          key: "about",
          id: "about",
          renderer: () => w(About, {})
        }),
        w(Outlet, {
          key: "profile",
          id: "profile",
          renderer: () => w(Profile, { username: "Dojo User" })
        })
      ])
    ]);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

查看插座，您可以看到我们定义了插座的 id，以匹配我们定义的路由配置。路由中呈现的实际小部件不必与 id 匹配，但是正如您所看到的，这样做是很好的实践。请保持代码的可读性。

出路很简单。因为它们为路线呈现小部件，所以它们还可以将任何 URL 参数作为属性传递给小部件。

## 链接和参数

在我们深入研究 URL 参数之前，首先我们需要讨论如何创建一个到需要参数的路由的链接。我们可以在 Dojo 中使用一个特定的组件来定义这些参数，这个组件就是 [Link](https://dojo.io/docs/index.html#doc--dojo__framework__v4_0_0__src__routing__README_md___link) 组件。

```
// src/widgets/Menu.ts
w(
  Link,
  {
    to: 'profile',
    key: 'profile',
    classes: [css.link],
    activeClasses: [css.selected],
    params: {
      username: 'odoe'
    }
  },
  ['Profile']
) 
```

Enter fullscreen mode Exit fullscreen mode

Link 组件是专门为在应用程序中创建到路由和静态路径的链接而设计的。他们为普通的锚标签提供一些糖，你可以在你的应用中利用这些糖。在这种情况下，我将为我们为路线定义的**用户名**参数提供一个值。这意味着 is 将把对象 **{用户名:' odoe' }** 传递给我的 Outlet，然后我可以用它来传递给我的子小部件。

```
// src/App.ts
w(Outlet, {
  key: 'profile',
  id: 'profile',
  renderer: ({ params }: MatchDetails) => {
    return w(Profile, { username: params.username });
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

当您在 Dojo 路由器中将参数传递给一个 URL 时，您的 render 方法会被传递这些参数，以便您根据需要在应用程序中使用。现在，虽然这种方法工作得很好，但是您可以更明确地使用您的路径参数。

您可以在路径中定义查询参数，并将其用于更高级的用途。让我们更新路由配置。

```
// src/routes.ts
export default [
  ...
  {
    path: "profile/{param}?{value}",
    outlet: "profile"
  }
]; 
```

Enter fullscreen mode Exit fullscreen mode

也许我们在后端 API 中有不同的搜索用户的方式。我们可以按姓名或 id 搜索。

```
// src/widgets/Menu.ts
w(
  Link,
  {
    to: 'profile',
    key: 'profile',
    classes: [css.link],
    activeClasses: [css.selected],
    params: {
      param: 'name',
      value: 'odoe'
    }
  },
  ['Profile']
) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们可以更新我们的 Outlet，将正确的信息传递给子小部件。

```
// src/App.ts
w(Outlet, {
  key: 'profile',
  id: 'profile',
  renderer: ({ params, queryParams }: MatchDetails) => {
    const user = users.find((user: User) => {
      return user[params.param] == queryParams.value;
    }) as User;
    return w(Profile, { username: `${user.name}  ${user.lastName}` });
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经构建了一个相当通用的方法，可以将参数和值传递给我们的 Outlet，并能够搜索正确的用户名以在我们的小部件中使用。我们可以通过**名称**值或 **id** 值进行搜索。

## 默认参数

到目前为止，我们一直在链路中定义参数，但也许我们想直接在路由中定义一些默认参数。

```
// src/routes.ts
export default [
  ...
  {
    path: 'profile/{param}?{value}',
    outlet: 'profile',
    defaultParams: {
      param: 'id',
      value: '2'
    }
  }
]; 
```

Enter fullscreen mode Exit fullscreen mode

对于我们的默认路由，我们可以决定使用值为 2 的 id 进行搜索。当您开始处理 URL 参数时，一切都是字符串，所以如果您想使用实数，您需要在您的应用程序中做一些额外的净化，但是我认为我们已经深入设置了 Dojo 路由器供初学者使用。非常感谢安东尼·古布勒帮助我解决了一些路由器问题，这是一个很大的帮助。

## 总结

如您所见，Dojo 路由器在定义路由和参数方面非常灵活。根据后端 API 的定义方式，您可以创建一些非常强大且可伸缩的应用程序！

请务必[订阅时事通讯](https://learn-dojo.com/sign-up/)，了解最新内容！
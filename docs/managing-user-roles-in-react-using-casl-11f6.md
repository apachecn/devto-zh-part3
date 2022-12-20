# 使用 CASL 管理 React 中的用户角色！

> 原文：<https://dev.to/dwalsh01/managing-user-roles-in-react-using-casl-11f6>

还贴在我的[博客](https://dwalsh.surge.sh)！

因此，在决定认证系统时，最好从**用例**开始。对我来说，它是作为我学位的一部分在一个团队软件项目中实现的。

我们必须实现一个拨款申请系统，它需要为不同类型的用户提供不同的用户界面。

我们系统中的用户角色是:

*   研究员

*   评论者

*   管理

## 库

### CASL

从网上的一些研究中，我找到了 CASL(有一个漂亮的 T2 反应堆包)。CASL(读作*城堡*)被作者描述为:

> 同构授权 JavaScript 库，限制给定用户可以访问的资源。

从阅读这个包来看，它似乎非常适合我的用例。

### Redux

实际上不需要介绍，每个使用 React 的人都知道 Redux。这是我对用户信息的存储和应用程序中 API 调用的各种响应最满意的地方。

## 实现

我要在你有一个 [functional redux store](https://medium.com/backticks-tildes/setting-up-a-redux-project-with-create-react-app-e363ab2329b8) 的前提下继续。

### 安装包

首先，您必须安装必要的 CASL 软件包。为此运行:

```
npm i @casl/react @casl/ability 
```

Enter fullscreen mode Exit fullscreen mode

### 范围界定可以

对于这个部分，我将使用 **2 个文件**、`ability.js`和`Can.js`进行操作。这两个文件我都放在了一个`config`文件夹里。关于文件结构的帮助，请参见丹·阿布拉莫夫的这篇有用的文章。

为什么要范围`Can`？好吧，如果你不确定范围，你必须通过我们在每个`Can`调用中检查的`ability`(例如`<Can I="create" a="Post" ability={ability}>`，其中`ability`是我们在`ability.js`文件中定义的能力，或者你放置能力的地方)。

当您在应用程序中定义了几个功能，或者您想要限制一个特定的`Can`组件使用另一个实例来检查功能时，就实现了作用域。

我从[文档](https://github.com/stalniy/casl/tree/master/packages/casl-react) :
中取出我们`Can.js`文件的实现

```
// Can.js
import { createCanBoundTo } from "@casl/react"
import ability from "./ability"

export default createCanBoundTo(ability) 
```

Enter fullscreen mode Exit fullscreen mode

我们导入我们的`ability`(在下一节中定义)并限定这个特定的`Can`组件来处理这些功能。

### 为用户角色定义能力

```
// Can.js
import { createCanBoundTo } from "@casl/react"
import ability from "./ability"

export default createCanBoundTo(ability) 
```

Enter fullscreen mode Exit fullscreen mode

正如您在上面看到的，我们导入了`ability`，这是定义所有用户权限的地方。现在让我们来看看那个文件。我将把它分成几个部分，然后在最后向您展示整个文件。

```
//ability.js
import { Ability, AbilityBuilder } from "@casl/ability"
import store from "../store/index"

// Defines how to detect object's type
function subjectName(item) {
  if (!item || typeof item === "string") {
    return item
  }
  return item.__type
}

const ability = new Ability([], { subjectName }) 
```

Enter fullscreen mode Exit fullscreen mode

好吧，这是怎么回事？`subjectName`函数接收对象，并返回该对象的属性`__type`,如果它存在的话。否则，如果传递的项是一个字符串，它将简单地返回该字符串，依此类推(也就是说，如果你传递`subjectName('Admin')`，它将返回`'Admin'`)。

```
//ability.js
import { Ability, AbilityBuilder } from "@casl/ability"
import store from "../store/index"

// Defines how to detect object's type
function subjectName(item) {
  if (!item || typeof item === "string") {
    return item
  }
  return item.__type
}

const ability = new Ability([], { subjectName }) 
```

Enter fullscreen mode Exit fullscreen mode

这是什么？嗯，这是[定义`Ability`实例的两种方式之一](https://www.npmjs.com/package/@casl/react#2-defining-abilities)。我们在这里做的是定义一个空的`Ability`实例，它将使用提供的`subjectName`来帮助决定将什么规则附加到特定用户。

接下来，我们将引入 redux store 来获取当前登录的用户，如果有的话:

```
//ability.js
...
const ability = new Ability([], { subjectName });

let currentAuth;
store.subscribe(() => {
  const prevAuth = currentAuth;
  currentAuth = store.getState().currentUserReducer;
  if (prevAuth !== currentAuth) {
    ability.update(defineRulesFor(currentAuth));
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们订阅了对`store`的修改，当商店**更新**的`currentUserReducer`对象时，我们将用商店中的当前用户调用`ability.update(defineRulesFor(currentAuth))`方法。作为参考，这里是我的`currentUserReducer`对象:

```
//CurrentUserReducer
const initialState = {
  isLoggedIn: null,
  user: null,
  role: "",
  errorMsg: "",
} 
```

Enter fullscreen mode Exit fullscreen mode

但是等等，`defineRulesFor`功能是什么？嗯，我们自己实现这个。这里，我们将根据当前用户的角色返回他们的规则。下面是我们的函数:

```
//ability.js
// this is just below store.subscribe()

function defineRulesFor(auth) {
  const { can, rules } = AbilityBuilder.extract()
  if (auth.role === "researcher") {
    can("view", "Proposal")
    can("view", "Draft")
    can("apply", "Proposal")
    can("view", "Profile")
    can("view", "Teams")
  }
  if (auth.role === "admin") {
    can("add", "Proposal")
    can("view", "Proposal")
    can("accept", "Application")
    can("reject", "Application")
    can("view", "PendingReviews")
  }
  if (auth.role === "reviewer") {
    can("review", "Proposal")
  }
  return rules
} 
```

Enter fullscreen mode Exit fullscreen mode

我们用 CASL 的`AbilityBuilder`来定义用户的能力。我们调用`extract()`方法只是为了让事情更加清晰(*避免嵌套*)。否则它会看起来像这样:

```
function defineRulesFor(auth) {
  return AbilityBuilder.define((can, cannot) => {
    if (user.role === "researcher") {
      can("view", "Proposal")
      can("view", "Draft")
      can("apply", "Proposal")
      can("view", "Profile")
      can("view", "Teams")
    }
  })
  //etc.
} 
```

Enter fullscreen mode Exit fullscreen mode

所以这只是我个人的偏好，两个都很好，我只是觉得第一个选项更容易阅读。你所要做的(如果你选择选项 1)就是在这个函数的末尾**返回规则**。

现在，让我们以`researcher`角色为例来解释这是怎么回事。我们说，如果用户是研究人员，我们希望他们能够:

*   查看提议
*   查看草稿
*   申请提案
*   查看个人资料
*   查看团队

`can`函数将把这些能力添加到这个用户的`rules`中，一旦我们为用户定义了规则，我们就在函数的结尾返回它们。

一旦完成，我们现在必须确保`export`我们先前定义的`ability`(并相应地更新规则)。

```
//abilty.js
function defineRulesFor(auth) {
  ...
  if (auth.role === "reviewer") {
    can("review", "Proposal")
  }
  return rules
}
export default ability; 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经介绍了我如何为每个角色指定基于角色的规则。让我们开始在 UI 中实现它们吧！

### 界面中的检查规则

我将在这里给出两个例子，一个是用户点击侧边栏中出现的菜单项，这将把他们带到特定的路线，另一个是只有当你有正确的角色时才呈现路线。

#### 侧栏

我们现在使用之前定义的`Can`组件(参见上面的`Can.js`文件)来有条件地呈现组件。下面是`SidebarRoutes`组件，它呈现出`ListItemLink`的路径和文本，显示在菜单项上:

```
//SidebarRoutes.jsx
//Other imports here
import Can from '../../config/Can';

...

const SidebarRoutes = ({ classes }) => (
  <List className={classes.root}>
    <ListItemLink text="Home" />
    <Can I="view" a="Profile">
      {() => <ListItemLink route="profile" text="Profile" />}
    </Can>
    <NestedProposals />
  </List>
); 
```

Enter fullscreen mode Exit fullscreen mode

我们导入`Can`组件，并检查**是否可以查看概要文件**。如果这是**真的**，那么它将渲染`ListItemLink`，否则它将不会渲染它。

我对`NestedProposals`组件中的各种规则做了同样的事情，下面可以看到其中的一个片段:

```
//NestedProposals.jsx
...
<Can I="add" a="Proposal">
    {() => (
        <ListItemLink
        route="admin/proposals/add"
        text="Add Proposals"
        className={classes.nested}
        />
    )}
</Can>
<Can I="review" a="Proposal">
    {() => (
        <ListItemLink
        route="proposals/respond"
        text="Respond To Applications"
        className={classes.nested}
        />
    )}
</Can>
... 
```

Enter fullscreen mode Exit fullscreen mode

本质上是一样的。我检查用户角色是否允许他们做某些事情，如果允许，我将呈现链接。

#### 路线

因此，我将再次给出我的`routes.jsx`文件的一个片段。就是这里:

```
//routes.jsx
...
const Routes = () => (
  <Switch>
    <Route exact path="/" component={GridCards} />

    <Route
      path="/profile"
      render={props => (
        <Can I="view" a="Profile">
          {() => <Profile {...props} />}
        </Can>
      )}
    />
</Switch>
... 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们利用 React Router 的`render`属性来检查当前用户的规则，并进行适当的渲染。正如您所看到的，一旦您正确定义了规则，实现起来几乎是一样的。

## 结束

感谢您的阅读！我会感谢任何关于我写作的意见(积极的/消极的),以提高我的写作水平。任何想法/疑问，请随时拍摄我在 Twitter 上的 DM。

## 完整的`ability.js`文件

```
/* eslint-disable no-underscore-dangle */
import { Ability, AbilityBuilder } from "@casl/ability"
import store from "../store/index"

// Defines how to detect object's type
function subjectName(item) {
  if (!item || typeof item === "string") {
    return item
  }
  return item.__type
}

const ability = new Ability([], { subjectName })

let currentAuth
store.subscribe(() => {
  const prevAuth = currentAuth
  currentAuth = store.getState().currentUserReducer
  if (prevAuth !== currentAuth) {
    ability.update(defineRulesFor(currentAuth))
  }
})

function defineRulesFor(auth) {
  const { can, rules } = AbilityBuilder.extract()
  if (auth.role === "researcher") {
    can("view", "Proposal")
    can("view", "Draft")
    can("apply", "Proposal")
    can("view", "Profile")
    can("view", "Teams")
  }
  if (auth.role === "admin") {
    can("add", "Proposal")
    can("view", "Proposal")
    can("accept", "Application")
    can("reject", "Application")
    can("view", "PendingReviews")
  }
  if (auth.role === "reviewer") {
    can("review", "Proposal")
  }
  return rules
}

export default ability 
```

Enter fullscreen mode Exit fullscreen mode
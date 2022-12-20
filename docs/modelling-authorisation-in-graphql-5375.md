# GraphQL 中的建模授权

> 原文：<https://dev.to/sgwilym/modelling-authorisation-in-graphql-5375>

在本文中，我们将探讨如何根据请求者(即授权者)在 GraphQL 模式上公开不同级别的信息，只使用 GraphQL 内置的模式定义功能。

最终，我们将拥有一个智能的、授权的类型，可以在图的任何级别重用，而不需要额外的努力。

* * *

GraphQL 中的授权通常被视为失败状态，要么在 GraphQL 响应的`errors`字段中报告授权失败，要么在最坏的情况下完全丢弃整个响应。

但是根据是谁要求回复不同类型的回复*也不算例外。仅仅因为我们不被允许看到*的一切*并不意味着我们不应该看到*的任何东西*，这应该反映在我们的图式中。GraphQL 为我们提供了表达我们想要公开的不同访问层所需的一切。*

## 用户裸露全部

让我们从一个可以查询其用户信息的服务开始:

模式

```
type  Query  {  users:  [User!]!  }  type  User  {  nickname:  String!  email:  String!  billingAddress:  String!  } 
```

查询

```
{  users  {  nickname  email  billingAddress  }  } 
```

响应

```
{  "data":  {  "users":  [  {  "nickname":  "Jenny Me",  "email":  "jenz@itsame.com",  "billingAddress":  "123 Open Lane"  },  {  "nickname":  "Freddy Friend",  "email":  "fred@buds.com",  "billingAddress":  "22a Sharing Avenue"  },  {  "nickname":  "mr. private",  "email":  "bill@respectmysolitude.biz",  "billingAddress":  "36bis Ivory Tower"  }  ]  }  } 
```

不好:有些信息是我们作为开发者和我们的用户都不希望任何人能够查询的，比如他们的`email`或`billingAddress`。

我们如何向未经授权的用户隐瞒这些信息？

## 字段可空的用户

我们可以采取的第一种方法是在现场解决过程中检测未经授权的操作。

```
// As an Apollo Server style resolver
User: {
    // ...
    billingAddress: (user, args, ctx) => {
        if (user.id === ctx.currentUser.id) {
            return user.billingAddress;
        }
        return null;
    }
    // ...
} 
```

现在，如果有人请求一个他们不允许看到的字段，他们将得到 null。

模式

```
type  Query  {  users:  [User!]!  }  type  User  {  nickname:  String!  # These fields are all nullable now  email:  String  billingAddress:  String  } 
```

查询

```
{  users  {  nickname  email  billingAddress  }  } 
```

响应

```
{  "data":  {  "users":  [  {  "nickname":  "Jenny Me",  "email":  "jenz@itsame.com",  "billingAddress":  "123 Open Lane"  },  {  "nickname":  "Freddy Friend",  "email":  "fred@amicus.com",  "billingAddress":  null  },  {  "nickname":  "mr. private",  "email":  null  "billingAddress":  null  }  ]  }  } 
```

我们以 Jenny Me 的身份登录，所以我们可以看到我们所有的信息，包括`billingAddress`。第二个用户 Freddy Friend 是我们的朋友，所以我们也可以看到他们的`email`。第三个用户 private 先生不想和我们有任何关系，所以我们只能看到他们的`nickname`。

在这里，我们设法只暴露了允许 Jenny Me 查看的信息，但是这种基于模式的授权的粗略方法有许多缺点。

第一个是，通过使一切都可以为空，您的客户不能确保数据的存在，即使在他们非常确定*应该*可用的情况下:想象一个期待`User`的`BillingAddressForm`组件，严格地说，它可能有也可能没有`billingAddress`。客户端现在需要处理字段可能为空值的所有情况。

第二个缺点是，这种方法给模式的字段解析器增加了授权逻辑的负担。以前只需要返回值的解析器现在需要在内部进行各种检查。

很快，这两个问题将使我们的客户端和服务器代码库变得更加费力。

## 多种用户

我们真正想要的是能够*知道*我们将得到我们所要求的数据——而不仅仅是根据数据的有无进行猜测。

对于我们的用户类型，我们有三个授权级别:

*   公开信息:任何人都可以看到用户的
*   好友专用信息:好友之间也可以看到彼此的`email`。
*   仅当前用户信息:只有用户自己能够获得自己的`billingAddress`。

查询一个用户可以得到三种不同的结果，所以让我们在 GraphQL 中用 union 类型来建模:

模式

```
type  Query  {  users:  [User!]!  }  type  User  {  nickname:  String!  }  type  FriendUser  {  nickname:  String!  # Email isn't nullable anymore!  email:  String!  }  type  CurrentUser  {  nickname:  String!  email:  String!  billingAddress:  String!  }  # Wrap all our users in a User union type  type  User  =  PublicUser  |  FriendUser  |  CurrentUser 
```

查询

```
{  users  {  __typename  ...  on  PublicUser  {  nickname  # PublicUser has no email field, so you can't even request it!  }  ...  on  FriendUser  {  nickname  email  }  ...  on  CurrentUser  {  nickname  email  billingAddress  }  }  } 
```

响应

```
{  "data":  {  "users":  [  {  "__typename":  "CurrentUser",  "nickname":  "Jenny Me",  "email":  "jenz@itsame.com",  "billingAddress":  "123 Open Lane"  },  {  "__typename":  "FriendUser",  "nickname":  "Freddy Friend",  "email":  "fred@amicus.com",  },  {  "__typename":  "PublicUser",  "nickname":  "mr. private",  }  ]  }  } 
```

这在许多方面改进了我们以前的可空方法。

现在，当我们查询我们的`users`字段时，我们可以确定像`email`或`billingAddress`这样的字段将返回一个结果——在我们的客户端代码中不再检查 null。

因为我们对返回的每种用户都有不同的类型，所以我们也可以通过检查 typename:
轻松地让我们的客户端服务于不同的 ui

```
const UserDescription = ({user}) => {
    if (user.__typename === "CurrentUser") {
        return <div>Hey, it's you!</div>;
    } else if (user.__typename === "FriendUser") {
        return <div>`It's your friend ${user.nickname}, email them at ${user.email}!`</div>
    } else {
        return <div>`Someone called ${user.nickname}.`</div>
    }
} 
```

我们还删除了大多数需要在字段解析器中进行授权检查的情况——取而代之的是，我们在联合类型的解析类型方法中检查返回哪种结果:

```
// In an Apollo Server style resolvers file
User: {
    resolveType: (user, args, ctx) => {
        if (user.id === ctx.currentUser) {
            return "CurrentUser";
        } else if (ctx.currentUserHasFriend(user.id)) {
            return "FriendUser"
        }
        return "PublicUser"
    }
} 
```

但是，同样，这种解决方案不会很好地扩展。

您可能已经注意到了我们不同用户类型中的重复:

```
type  User  {  nickname:  String!  }  type  FriendUser  {  nickname:  String!  email:  String!  }  type  CurrentUser  {  nickname:  String!  email:  String!  billingAddress:  String!  } 
```

这不仅仅是因为重复，而是因为对这些字段的每一个添加、删除或更改都需要在我们所有的类型之间进行协调。如果你把`nickname`改名为`name`，会引起很多混乱，但只是在`PublicUser`上。如果我们有八个级别的授权，或者这些类型被分成不同的文件，一个大团队不一定知道谁在更改什么，会怎么样？

更糟糕的是，查询这些类型不太符合人体工程学:

```
{  users  {  __typename  ...  on  PublicUser  {  nickname  }  ...  on  FriendUser  {  nickname  email  }  ...  on  CurrentUser  {  nickname  email  billingAddress  }  }  } 
```

同样，重复是乏味的(而且只有三种类型)，但是如果我们编写的客户端代码期望这些字段出现在我们所有的`User`类型上，我们也会遇到麻烦——这种风险随着我们类型上的字段数量的增加而增加。

## 用户与界面

如果我们能让我们不同的`User`类型*承诺*他们一定会有特定的领域，那就好了。幸运的是，我们可以用 GraphQL 接口做到这一点。

让我们为我们想要授予的每个访问级别创建一个接口，并将它们分配给相应的类型:

```
interface  PublicUserInfo  {  nickname:  String!  }  interface  FriendInfo  {  email:  String!  }  interface  PrivilegedInfo  {  billingAddress:  String!  }  type  User  implements  PublicUserInfo  {  nickname:  String!  }  type  FriendUser  implements  PublicUserInfo  &  FriendInfo  {  nickname:  String!  # If the nickname were missing here GraphQL would tell us about it.  email:  String!  }  type  CurrentUser  implements  PublicUserInfo  &  FriendInfo  &  PrivilegedInfo  {  nickname:  String!  email:  String!  billingAddress:  String!  }  type  User  =  User  |  FriendUser  |  CurrentUser 
```

现在，如果我们添加、删除或更改一个字段，我们所有不同的类型都会发生这种变化，GraphQL 会警告我们哪些类型不符合这些接口。

这一变化也意味着我们可以以更符合人体工程学的方式重写我们的查询:

```
{  users  {  __typename  # You can write fragments for interfaces, cool!  ...  on  PublicInfo  {  nickname  }  ...  on  FriendInfo  {  email  }  ...  on  PrivilegedInfo  {  billingAddress  }  }  } 
```

而回应，再次:

```
{
    "data": {
        "users": [
            {
                "__typename": "CurrentUser",
                "nickname": "Jenny Me",
                "email": "jenz@itsame.com",
                "billingAddress": "123 Open Lane"
            },
            {
                "__typename": "FriendUser",
                "nickname": "Freddy Friend",
                "email": "fred@amicus.com",
            },
            {
                "__typename": "PublicUser",
                "nickname": "mr. private",
            }
        ]
    }
} 
```

# 到处都是用户

通过将授权的联合类型与其每个成员的匹配接口相结合，我们获得了以下好处:

*   用户只能获得他们有权接收的信息。
*   请求的字段总是返回我们期望的数据，所以不必检查`null`。
*   我们已经能够将许多授权检查从字段解析器转移到联合类型解析器。对于依赖于顶级值的授权检查，如当前用户，这种影响可能是全面的(感谢 Andrew Ingram 指出这并没有完全消除在字段解析器中进行授权检查的需要)。
*   通过`__typename`确定任何返回类型的授权级别非常简单。
*   相信我们的类型将拥有返回相同类型数据的公共字段集——graph QL 将通过我们的接口确保这一点。

多个接口和类型定义的组合可能看起来像许多样板文件，但是它在 GraphQL 服务器上的其他地方保存了许多行代码，并且为查询它的客户端创建了极好的体验，尤其是在使用类型的情况下。

最重要的是，我们的用户类型可以在模式中的任何地方使用，而不必编写任何新的授权代码。想象一下，如果我们想给`PublicInfo`添加一个新的`friends`字段:

模式

```
interface  PublicUserInfo  {  nickname:  String!  friends:  [User!]!  }  interface  FriendInfo  {  email:  String!  }  interface  PrivilegedInfo  {  billingAddress:  String!  }  type  User  implements  PublicUserInfo  {  nickname:  String!  }  type  FriendUser  implements  PublicUserInfo  &  FriendInfo  {  nickname:  String!  # If the nickname were missing here GraphQL would tell us about it.  email:  String!  }  type  CurrentUser  implements  PublicUserInfo  &  FriendInfo  &  PrivilegedInfo  {  nickname:  String!  email:  String!  billingAddress:  String!  }  type  User  =  User  |  FriendUser  |  CurrentUser 
```

查询

```
{  users  {  __typename  ...  on  PublicInfo  {  nickname  friends  {  __typename  ...  on  PublicInfo  {  nickname  }  }  }  ...  on  FriendInfo  {  email  }  ...  on  PrivilegedInfo  {  billingAddress  }  }  } 
```

响应

```
{  "data":  {  "users":  [  {  "__typename":  "CurrentUser",  "nickname":  "Jenny Me",  "email":  "jenz@itsame.com",  "billingAddress":  "123 Open Lane"  "friends":  [  {  "__typename":  "FriendUser",  "nickname":  "Freddy Friend",  }  ]  },  {  "__typename":  "FriendUser",  "nickname":  "Freddy Friend",  "email":  "fred@amicus.com",  "friends":  [  {  //  Hey,  we  could  render  a  little  crown  over  our  name  in  Freddy's  friend  list!  "__typename":  "CurrentUser",  "nickname":  "Jenny Me",  }  ]  },  {  "__typename":  "PublicUser",  "nickname":  "mr. private",  "friends":  []  }  ]  }  } 
```

为此必须添加的唯一代码是`PublicInfo`上`friends`字段的解析器——不需要授权代码。

这种方法可以应用于任何类型，而不仅仅是`User` s: `Event` s，`Task` s，`Recipe` s。所有相同的原则都适用。希望这种模式可以帮助您使您的模式更加令人愉快。
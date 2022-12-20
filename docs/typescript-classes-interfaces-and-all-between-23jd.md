# 类型脚本类、接口以及所有介于

> 原文：<https://dev.to/krzysztofzuraw/typescript-classes-interfaces-and-all-between-23jd>

如果你想比 dev.to 上的帖子早一周得到这些帖子，你可以在[https://krzysztofzuraw.com/newsletter](https://krzysztofzuraw.com/newsletter)上加入我的时事通讯。

# 您好👋

解释类和接口的更好的方法是什么？我看到的类的一个聪明的用法是与验证来自 API 的响应相关联。

大多数当前的 TypeScript 或 JavaScript 应用程序需要以某种方式连接到 API。如何解析这些 JSON 并在所有应用程序中使用它们？我在项目中使用的一种模式是使用 dto 的模式——它代表数据传输对象。为什么我需要 dto 这样的东西？

让您的应用程序在整个应用程序中对相同的值和对象进行操作，并验证应用程序的传入数据。

## dto 在行动

如果你阅读👆段落-祝贺你🎉。让我们直接进入代码，这样你就不会感到无聊了

```
axios.get<{ user: IUserDTO }>('api/users/1').then(res => UserModel.create(res.data)); 
```

Enter fullscreen mode Exit fullscreen mode

我使用`IUserDTO`来告诉 axios 我可能从 API 得到什么响应——在我的例子中，将是:

```
interface IUserDTO {
  name: string;
  password_hash: string;
  email?: string;
  is_super_user: boolean;
} 
```

Enter fullscreen mode Exit fullscreen mode

大致翻译成 JSON:

```
{  "user":  {  "name":  "John",  "password_hash":  "42",  "email":  "john@example.com",  "is_super_user":  true  }  } 
```

Enter fullscreen mode Exit fullscreen mode

在`then`中，你可以看到`UserModel.create`——这是我们应用程序的用户模型的入口点。

## 型号用法

从现在开始，我将只在我的 redux 存储、选择器或组件中使用`UserModel`。像这里:

```
// actions
interface getUserSuccess {
  type: 'GET_USER_SUCCESS';
  user: IUserModel;
}

// state
type State = Readonly<{
  user: IUserModel | null;
  isFetching: boolean;
}>; 
```

Enter fullscreen mode Exit fullscreen mode

## 类型与接口

在代码片段的底部是另一种声明类型的方式- `type`。`type`和`interface`有什么区别？它们用于相同的目的——告诉 TypeScript 你期望什么样的形状和类型的值。我个人更经常使用`interface`，因为它们允许我扩展自身，比如:

```
interface Animal {
  name: string;
}

interface Dog extends Animal {
  isBarking: boolean;
}

const rex: Dog = { isBarking: true, name: 'Rex' }; 
```

Enter fullscreen mode Exit fullscreen mode

然而也有我使用`type`的情况——例如当我已经反应了`Props`声明:

```
type Props = ReturnType<typeof mapStateToProps> & typeof dispatchProps & OwnProps; 
```

Enter fullscreen mode Exit fullscreen mode

## 回到型号

我这里有`UserModel`节课:

```
class UserModel implements IUserModel {
  constructor(
    public name: string,
    passwordHash: string,
    public isSuperUser: boolean,
    email?: string
  ) {}

  static create(dto: IUserDTO): UserModel {
    return new UserModel(dto.name, dto.password_hash, dto.is_super_user, dto.email);
  }
}
interface IUserModel {
  name: string;
  passwordHash: string;
  email?: string;
  isSuperUser: boolean;
}

interface IUserDTO {
  name: string;
  password_hash: string;
  email?: string;
  is_super_user: boolean;
} 
```

Enter fullscreen mode Exit fullscreen mode

一开始，看起来这里发生了很多事情，但是让我们从头开始。首先我告诉`UserModel`类实现`IUserModel`。`IUserModel`是接口。这意味着所有具有相同类型的相同字段的对象都实现了接口。在上述
的情况下，当`UserModel`执行`IUserModel`时，必须具备:`name`、`password`、`isSuperUser`、& `email`。我确保它在`constructor`中。

如果我有一个对象:

```
const user: IUserModel = {
  name: 'Evil user',
  password: '',
  isSuperUser: true,
  evil: true,
}; 
```

Enter fullscreen mode Exit fullscreen mode

具有附加属性(如`evil`)的类型脚本会告诉我:

```
Type '{ name: string; passwordHash: string; isSuperUser: true; evil: boolean; }' is not assignable to type 'IUserModel'.
  Object literal may only specify known properties, and 'evil' does not exist in type 'IUserModel'.ts 
```

Enter fullscreen mode Exit fullscreen mode

接口的命名可以与实现这些接口的类的命名相同。但是我添加了`I`作为前缀，表示我在这里使用了一个接口。

回到构造函数。在每个`constructor`方法的参数之前，我有一个叫做`public`的东西。这是
的简写

```
class UserModel implements IUserModel {
  constructor(name, passwordHash: string, isSuperUser: boolean, email?: string) {
    this.name = name;
    this.passwordHash = passwordHash;
    this.isSuperUser = isSuperUser;
    this.email = email;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后我可以使用`new`关键字:`new UserModel('Krzysztof', '134', true, 'kz@example.com')`创建新的类。

最后缺失的一块是`static create()`法。关键字`static`意味着方法没有绑定到类的实例。所以不需要写:

```
const user = new UserModel('Krzysztof', '134', true, 'kz@example.com');
user.create(); 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用它，而无需首先实例化类- `UserModel.create()`，我在我的 API 调用代码中使用了它。在`create`的主体中，我创建了一个新的类。

### 总结&TL；速度三角形定位法(dead reckoning)

我使用具有 TypeScript 接口的类来完成:验证来自应用程序的数据。类型也很好，但是在我可以把不同类型粘在一起的情况下，比如`interfaces` & `ReturnTypes`。

如果你想比 dev.to 上的帖子早一周得到这些帖子，你可以在[https://krzysztofzuraw.com/newsletter](https://krzysztofzuraw.com/newsletter)上加入我的时事通讯。
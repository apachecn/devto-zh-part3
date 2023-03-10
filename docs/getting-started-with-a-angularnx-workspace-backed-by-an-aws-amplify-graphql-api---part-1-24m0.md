# AWS Amplify GraphQL API 支持的 Angular/NX 工作空间入门——第 1 部分

> 原文：<https://dev.to/beavearony/getting-started-with-a-angularnx-workspace-backed-by-an-aws-amplify-graphql-api---part-1-24m0>

我们将在的帮助下使用 [Angular](https://angular.io) 创建一个 Todo 应用程序

*   [Nx](https://nx.dev) 有助于多应用、多团队的项目风格
*   [AWS Amplify CLI](https://aws-amplify.github.io/docs) 创建全功能后端，完全用代码管理(基础设施即代码)
*   AWS AppSync JavaScript SDK 使用 Apollo 的 AppSync 客户端，具有离线和持久化功能
*   [AWS 放大角度](https://aws-amplify.github.io/docs/js/angular)角度组件和角度提供器，有助于与 AWS-Amplify 库集成

我们正在创建一个离线就绪的 Todo 应用程序，由 GraphQL API 和身份验证提供支持。我们可以通过添加更多的类别和功能来扩展这个应用程序。但是让我们从简单的开始。

可以看一下这个回购中的代码[。](https://github.com/BeaveArony/amplify-angular-nx-todo)

## 先决条件

我假设您已经安装了 [NodeJS](https://nodejs.org) 版本 8 或更高版本，并且您可以访问 AWS 帐户。免费层在第一年是非常慷慨的，可能不会花费你什么。

## 创建工作区

NX 是 Angular CLI 之上的一个示意图，它为我们提供了设置工作区的工具，在这里可以轻松地在应用程序之间共享代码，并创建边界以帮助在一个团队甚至多个团队中工作。

通过将逻辑分离成模块，只需像导入其他 npm 包一样导入它们，就可以轻松地重用它们。我们不需要将这些包中的任何一个发布到公共或私有的存储库中。这让我们免去了在每个库的不同版本上运行测试的麻烦，并为我们提供了所有部件协同工作的快照。

通过 AWS Amplify CLI，我们甚至可以将一个或多个云堆栈连接到我们的工作区，这样我们就可以将后端的“基础设施作为代码”与前端应用程序一起使用。这并不是说完全不同的产品都放在同一个回购协议中。更确切地说，考虑拥有一个客户网络和独立的移动应用程序，一个管理门户，一个支持团队的电子桌面应用程序，一个经理报告应用程序等等...它们都以某种方式处理相同的数据，并将从跨库的共享 SDK 和公共接口中获益匪浅。

即使您最终只使用一个应用程序，其中包括不同的领域或业务线(LOB ),它也有助于构建代码库，以便团队可以同时工作，而不必太担心以后集成到生产中的痛苦。

我们通过使用 npx 执行下面的命令来创建一个工作区文件夹，这样我们就不必全局安装它了

```
npx @nrwl/schematics amplify-graphql-angular-nx-todo 
```

当被问到时，我们会回答一些问题

***？您想使用哪种样式表格式？*** CSS

***？您希望您的 Nx 工作区使用的 npm 范围是什么？*** 我的

***？在新的工作区中创建什么(您可以在任何时候使用【ng g】)***空创建其他应用程序和库)

我们将我们的工作区命名为“我的”,它通常应该以您的组织或项目命名，例如。myorg。

我们创建一个空的工作区，这样我们就可以随心所欲地命名我们的第一个应用程序。否则，它将被命名为‘amplify-graph QL-angular-NX-todo’。

## 创建 app

我们决定将我们的第一个应用程序命名为“todo-app”(这将是这篇文章中唯一的一个应用程序)。

```
ng g app todo-app 
```

***？您希望应用程序使用什么框架？*** 棱角分明

***？应该在哪个目录中生成应用程序？*T3】**

***？您想使用哪种样式表格式？*** CSS

***？您希望为应用程序使用哪个单元测试运行程序？*** 玩笑话

***？您希望在申请中使用哪个 E2E 测试赛选手？*** 柏树

***？您希望将哪些标签添加到应用程序中？*** (用于林挺)app

这创造了两个应用程序

1.  待办事项应用程序
2.  托多应用程序 e2e

第二个 app 是端到端测试。该应用程序是我们实际的应用程序，我们将尽可能只使用它来编排其他模块。我们的大部分代码将放在 libs 中，以便在未来可能的应用程序中重用。

## 创建库

那么我们把代码放在哪里呢？NX 使创建新库变得容易！我们可能用许多库来编写我们的应用程序。

1.  我们需要一个库来存放后端的东西，让我们称这个库为`appsync`
2.  我们将 UI 组件放入库`todo-ui`

所以让我们运行下面的命令来生成我们想要的库:

```
ng g lib appsync --tags=shared --framework=angular --unitTestRunner=jest --style=css
ng g lib todo-ui --tags=ui --framework=angular --unitTestRunner=jest --style=css 
```

## 创建 AWS 后端

(AWS Amplify CLI)[[https://github.com/aws-amplify/amplify-cli](https://github.com/aws-amplify/amplify-cli)]使得在 AWS 云中创建无服务器后端变得非常容易。一切都在代码中定义，可以在我们的源代码控制中检查。它提高了编写冗长的云信息模板来描述我们的云资源的需求。几个简单的命令将使用团队资源或创建一个开发人员沙盒来测试新功能，从而启动整个云堆栈。

通过运行以下命令全局安装 Amplify CLI(如果之前没有这样做):

```
npm install -g @aws-amplify/cli
amplify configure 
```

如果你完全不熟悉 AWS Amplify，[你应该查看如何配置 Amplify CLI 的文档](https://aws-amplify.github.io/docs)

这里我们实际上是在全球范围内安装 AWS Amplify CLI，而不是使用 npx！我们将经常使用 amplify 命令。

安装放大 JavaScript 依赖项

```
npm i aws-amplify aws-amplify-angular 
```

## 调整 angular app 放大的 SDK

为了在角度环境中使用 AWS Amplify SDK，我们需要做一些更改:

1.  告诉 typescript 关于`window.global`变量的信息
2.  将“节点”添加到 tsconfig
3.  编写脚本将 aws-exports.js 重命名为 aws-exports.ts。

打开`apps/todo-app/src/polyfills.ts`并在文件
的顶部添加以下一行

```
(window as any).global = window; 
```

`node`包需要包含在`apps/todo-app/tsconfig.app.json`
中

```
{  "compilerOptions":  {  "types":  ["node"]  }  } 
```

AWS Amplify 附带了一个 UI 库。我们也可以重用这些组件的样式，并在它们的基础上进行构建。在`apps/todo-app/src/styles.css`中添加以下几行:

```
/* You can add global styles to this file, and also import other style files */
@import '~aws-amplify-angular/theme.css'; 
```

我们通过运行
将我们的项目或分支连接到云资源栈

```
amplify init 
```

使用 NX，我们需要改变相当多的默认值:

***？输入项目名称*** amplifynx
***？输入环境名称***
***？选择你的默认编辑器:*** Visual Studio 代码
***？选择你正在构建的应用类型*** javascript
请告诉我们你的项目
***？你在用什么 javascript 框架*** angular
***？源目录路径:***libs/app sync/src/l
***？分发目录路径:***dist/apps/todo-app
***？构建命令:*** npm 运行-脚本构建
***？开始命令:*** npm 运行-使用默认提供程序 awscloudformation 开始编写脚本

***？是否要使用 AWS 配置文件？*T3】是
T5】T6】？请选择您想要使用的概要文件** my.aws.profile
⠇在云端初始化项目...

我们将我们的云环境命名为 master，对应于我们当前的 git 分支 master。我们可以很容易地添加更多的分支，比如在云中开发和创建同名的环境(栈)。

我们将 ***源目录路径*** 设置为我们之前创建的名为 *appsync* 的 lib，这样自动生成的文件 aws_exports.js 将默认位于该文件夹中。这样，我们只需导入这个库，就可以与不同的应用程序共享我们的云配置。

***分发目录路径*** 指向单个应用程序的 dist 输出，这只有在我们使用 amplify CLI 命令来构建或服务应用程序或者我们将应用程序连接到 [Amplify 控制台](https://amplify.aws)时才重要。后者使得创建 CI/CD 管道变得非常容易，只需将 GIT 分支连接到部署即可。

您会注意到一个名为`amplify`的新顶层文件夹。在此文件夹中创建的大多数文件都被添加到了`.gitignore`中。其余的文件可以而且应该被添加到版本控制中，并且可以与您的团队共享。如果你计划公开你的回购，你也应该把`amplify/team-provider-info.json`加到`.gitignore`里。

## 让我们添加我们的第一个类别`auth`！

```
amplify add auth 
```

我喜欢更改默认配置来软化密码策略，并提供自定义的电子邮件主题文本，但是如果您愿意，您可以选择默认设置，然后通过运行
来更改这些设置

```
amplify auth update 
```

为了在云中创建这些`auth`资源，运行

```
amplify push 
```

你会看到

```
Current Environment: master

| Category | Resource name | Operation | Provider plugin   |
| -------- | ------------- | --------- | ----------------- |
| Auth     | cognitonxtodo | Create    | awscloudformation |
? Are you sure you want to continue? (Y/n) 
```

过一会儿，当您登录 AWS 控制台时，您会发现新创建的 Cognito 用户池。

这个命令会带你去:

```
amplify console auth 
```

## 重命名 aws-exports.js

`amplify push`命令应该已经生成了一个文件:`libs/appsync/src/lib/aws-exports.js`，我们应该将其重命名为`aws-exports.ts`，以便于将配置导入到其他类型脚本文件中。为了实现自动化，我们使用了`package.json`中的脚本部分。现在，每次我们提供或构建应用程序时，文件都会被重命名。

```
{  "start":  "npm run rename:aws:config || ng serve; ng serve",  "build":  "npm run rename:aws:config || ng build --prod; ng build --prod",  "rename:aws:config":  "cd libs/appsync/src/lib && [ -f aws-exports.js ] && mv aws-exports.js aws-exports.ts || true"  } 
```

我们还应该将这个重命名的文件添加到`.gitignore`，因此将`aws-export.js`改为`aws-exports.*s`。

## 添加 GraphQL API

如果您以前曾经编写过 GraphQL API，您将会体会到创建它的简单性，只需键入几行代码:

```
amplify add api 
```

***？请从以下提到的服务中选择一项***

***？提供 API 名称:***amplifynx

***？为 API 选择授权类型*** Amazon Cognito 用户池

使用配置为该项目的一部分的 Cognito 用户池

***？你有带注释的 GraphQL 模式吗？*** 没有

***？您想要引导模式创建吗？*** 是

***？什么最能描述你的项目:*** 带字段的单个对象(如带 ID、名称、描述的“Todo”)

***？您想现在编辑模式吗？*** 是

这将打开您之前配置的编辑器，其中包含一个示例 Todo 模式:

```
type Todo @model {
  id: ID!
  name: String!
  description: String
} 
```

我们不想要描述，但希望跟踪我们的待办事项的*已完成*状态。
首先，为了不太担心离线和后端排序，我们可以添加一个 *createdOnClientAt* 时间戳来对客户端上的待办事项进行排序。AppSync 在默认情况下添加了 *createdAt* 和 *updateAt* 时间戳，我们可以在模式中使用它们，但是当我们在离线和重新连接时排队几个待办事项时，它会扰乱我们的待办事项列表。

稍微解释一下:

假设我们只依赖于 *createdAt* 属性进行排序，并且我们在离线时添加 todo-1 和 todo-2。这些被添加到我们的发件箱。它们被正确排序，因为我们在创建这些待办事项时添加了时间戳。一旦我们上线，todo-1 将被发送到服务器，并将在服务器上接收一个新的 *createdAt* 时间戳，因为它是在服务器上创建的。该时间戳比 todo-2 的时间戳晚。因此 todo-1 将跳转到位置 2，直到 todo-2 被发送。todo-2 被发送到服务器后，获得一个新的时间戳，它将再次跳回到位置 2。我们将看到我们的客户发生短暂的重新订购。

在服务器上未触及 *createdOnClientAt* 属性。

将模式更改为这样并保存:

```
type Todo @model @auth(rules: [{ allow: owner }]) {
  id: ID!
  name: String!
  completed: Boolean!
  createdOnClientAt: AWSDateTime!
} 
```

我们将`@auth(rules: [{allow: owner}])`添加到 Todo 类型中，以便只显示每个登录用户的 Todo。这将自动向 DynamoDB 的底层 todo 表中的每个 todo 添加一个*所有者*字段。

## 幕后发生了什么？

当您查看`amplify/backend/api/{angularnx}/build`目录时，您会发现 AWS Amplify 团队提供了一个改进的 GraphQL 模式。它生成所有类型的 CRUD 操作和输入对象，并自动过滤和分页！只需编写 6 行带注释的 graphql，我们就可以获得 116 行现成的 graphql 最佳实践！太棒了！

## 上传 API

当我们第一次决定推出我们的 API 时，我们会被问及一些关于生成代码和将代码放在哪里的问题。选择 *angular* 作为语言目标，其余为默认值！

```
amplify push 
```

```
Current Environment: master

| Category | Resource name | Operation | Provider plugin   |
| -------- | ------------- | --------- | ----------------- |
| Api      | amplifynx     | Create    | awscloudformation |
| Auth     | cognitonxtodo | No Change | awscloudformation |
? Are you sure you want to continue? Yes 
```

***？是否要为新创建的 GraphQL API*** 生成代码【是】

***？选择代码生成语言目标***

***？输入 graphql 查询、突变和订阅的文件名模式*** (src/graphql/ **/_)。graphql)

_** ？输入 graphql 查询、突变和订阅的文件名模式**_ src/graph QL/API . service . ts

_**？你要生成/更新所有可能的 GraphQL 操作——查询、突变和订阅***是

⠋更新云中的资源。这可能需要几分钟时间...

> 当试图使用另一个目录时，比如我们的 lib 文件夹，会有一个 bug，所以只需保留默认路径`src/graphql/*`！否则，服务或类型将在我们的库中结束，并且几乎是空的。graphql 文件将位于默认位置。

## 放大码发生器

当我们决定生成代码时，我们有两种选择:

1.  有角的
2.  以打字打的文件

### 1。有角的

如果我们选择 angular 作为目标(在我们的例子中就是这样)，我们将得到一个包含所有类型脚本和 angular 服务的文件。该服务使用 amplify SDK 连接到 API。这对我们来说并不真正有用，因为我们决定使用 AppSync SDK 作为我们的客户端，而不是连接到我们的云资源来离线和持久化。我们不会直接使用类型或服务，但是服务中定义的操作可以为编写我们自己的查询和变化提供一个很好的起点。

选择*时，角度*也会生成`queries.graphql`、`mutations.graphql`、`subscriptions.graphql`和一个内省模式`schema.json`。这非常有用，我们将在后面看到。

### 2。以打字打的文件

这会生成*。ts 文件，所有查询、变更和订阅都作为字符串导出。

它还生成一个定义所有类型的文件`API.ts`。

我不喜欢这个选项，就像我们稍后将探讨的其他生成器一样。这里有一个由*类型脚本*选项生成的例子:

```
export type CreateTodoMutation = {
  createTodo: {
    __typename: 'Todo';
    id: string;
    name: string;
    completed: boolean;
    createdOnClientAt: string;
  } | null;
}; 
```

拥有嵌套的`createTodo`属性是正确的，但是要在代码中键入我们的 Todo 对象，我们需要这样做:

```
const newTodo: CreateTodoMutation['createTodo'] = {
  /*...*/
}; 
```

我的代码编辑器对字符串部分没有帮助，它可能会在复杂的模式中引入细微的错误。

## 阿波罗代码生成器

另一种可能是使用 apollo 客户机的代码生成器。我喜欢它生成的类型。它们干净、简短，上面的代码看起来像这样

```
const newTodo: CreateTodo_createTodo = {
  /*...*/
}; 
```

只需执行
即可使用

```
npx apollo-codegen generate src/graphql/*.graphql --schema src/graphql/schema.json --addTypename --target typescript --output libs/appsync/src/lib/api.types.ts 
```

> 在本地安装并运行它会给我一个错误，因为在 *aws-appsync* 中使用的依赖关系 *graphql* 的版本不匹配。尽管使用 npx 效果很好，但我并不担心为此寻找其他解决方案。使用纱线可能会更好...

## GraphQL 代码生成器

我在代码生成器中寻找的一个非常重要的特性是定制代码生成方式的可能性。 [{ GraphQL }代码生成器](https://graphql-code-generator.com)就是这样一个工具。它使用插件为许多不同的语言生成代码。

你可以通过运行
来安装它

```
npm i graphql-code-generator graphql-codegen-typescript-common graphql-codegen-typescript-client
graphql-codegen-fragment-matcher 
```

创建一个 codegen.yml 文件:

```
overwrite: true
schema: src/graphql/schema.json
documents: src/graphql/*.graphql
generates:
  ./libs/appsync/src/lib/graphql.ts:
    plugins:
      - 'typescript-common'
      - 'typescript-client'
      - 'fragment-matcher' 
```

然后简单地运行

```
npm run gql-gen 
```

这将在我们的 lib 中生成一个 graphql.ts 文件，提供在名称空间中组织的所有类型。如果我们添加另一个针对 *angular* 的插件，我们也将为每个操作获得一个服务，我们可以简单地将它注入到我们的组件中。

> 请注意，我们不会在我们的应用程序中使用该工具

## 选择代码生成器

我认为使用阿波罗代码生成器可能是中间的选择。当你把它添加到`package.json`的脚本部分，并把它挂接到 amplify push 命令:
时，它很容易使用

```
{  "generate":  "npx apollo-codegen generate src/graphql/*.graphql --schema src/graphql/schema.json --addTypename --target typescript --output libs/appsync/src/lib/api.types.ts",  "push":  "amplify push && npm run generate"  } 
```

> 在配置 amplify 的 codegen 时，确保使用`angular`(不是`typescript`)作为上一步的目标，以便生成`src/graphql/*.graphql`文件。
> 
> `src/graphql`文件夹仅用于代码生成，不应导入我们应用程序的任何地方！

既然`src/graphql`里的东西都是生成的，我们也可以把它和`.graphqlconfig.yml`一起放在`.gitignore`里。

```
src/graphql
.graphqlconfig.yml; 
```

## 编写我们的 GraphQL 操作

我们的应用需要

1.  获取我们的待办事项列表
2.  创建新的待办事项
3.  更新待办事项的完成状态
4.  删除待办事项

为了使我们的操作在不同的组件或服务中可重用，我们将它们放在一个文件`libs/appsync/src/lib/gql-operations.ts`中。

```
import gql from 'graphql-tag';

export const CREATE_TODO = gql`
  mutation CreateTodo($input: CreateTodoInput!) {
    createTodo(input: $input) {
      __typename
      id
      name
      completed
      createdOnClientAt
    }
  }
`;

export const LIST_TODOS = gql`
  query ListTodos($filter: ModelTodoFilterInput, $nextToken: String) {
    listTodos(filter: $filter, limit: 999, nextToken: $nextToken) {
      __typename
      items {
        __typename
        id
        name
        completed
        createdOnClientAt
      }
    }
  }
`;

export const UPDATE_TODO = gql`
  mutation UpdateTodo($input: UpdateTodoInput!) {
    updateTodo(input: $input) {
      __typename
      id
      name
      completed
      createdOnClientAt
    }
  }
`;

export const DELETE_TODO = gql`
  mutation DeleteTodo($input: DeleteTodoInput!) {
    deleteTodo(input: $input) {
      __typename
      id
    }
  }
`; 
```

> 为了不用担心获取更多的 todos 和简化 apollo 的缓存，我们将 listTodos 中的限制硬编码为 999

不要忘记，我们在`src/graphql/API.service.ts`中有自动生成代码，所以我们可以简单地复制其中一些语句，并将它们作为我们的起点。在具有深度嵌套模式的更高级的应用程序中，生成的操作将需要根据每个组件的需要进行调整。

从软件架构的角度来看，最好创建一个名为`todo-data`或`todo-model`的新库，并将文件放在那里，命名为`todo-operations.ts`。然后，我们还可以添加一个 facade，这将是我们向其他库公开的唯一内容。这个数据层 lib 也可以保存我们的本地状态，例如使用 redux。走这条路会使我们的组件更简单。我们可能会在未来对此进行扩展。

## 设置 AppSync 客户端

安装客户端、graphql-tag 和可选的 local feed

```
npm i aws-appsync graphql-tag localforage 
```

是时候修改`libs/appsync/src/lib/appsync.module.ts`来实例化和配置 AppSync 客户端了。

将文件内容更改为:

```
import { NgModule } from '@angular/core';
import Amplify, { Auth } from 'aws-amplify';
import { AmplifyAngularModule, AmplifyService } from 'aws-amplify-angular';
import AWSAppSyncClient, { AUTH_TYPE } from 'aws-appsync';
import * as localForage from 'localforage';
import config from './aws-exports';

Amplify.configure(config);

export const AppSyncClient = new AWSAppSyncClient({
  url: config.aws_appsync_graphqlEndpoint,
  region: config.aws_appsync_region,
  auth: {
    type: AUTH_TYPE.AMAZON_COGNITO_USER_POOLS,
    jwtToken: async () =>
      (await Auth.currentSession()).getIdToken().getJwtToken()
  },
  complexObjectsCredentials: () => Auth.currentCredentials(),
  cacheOptions: { addTypename: true },
  offlineConfig: { storage: localForage }
});

@NgModule({
  exports: [AmplifyAngularModule],
  providers: [AmplifyService]
})
export class AppsyncModule {} 
```

我们正在创建 AWSAppSyncClient 并导出实例，因此我们可以在其他地方使用它。
AmplifyService 需要添加到 providers 数组中，因为它不使用`@Injectable({providedIn: 'root'})`。

为了将 AppSync 存储持久化在 IndexedDB 而不是 LocalStorage 中，我们使用库 local feed。

为了连接它，不要忘记将`AppsyncModule`添加到`apps/todo-app/src/app/app.module.ts`的导入数组中。

> 注意:这里我们没有使用 [Apollo-Angular](https://github.com/apollographql/apollo-angular) 客户端。这将给我们一个可注入的服务，我们可以在我们的应用程序中使用它，它用 rxjs 包装了底层的 zen-observables。虽然这不是必须的，因为我们将看到在 angular 中构建这个应用程序。
> 
> AWSAppSyncClient 在幕后使用了相当多的库，这使得交换或添加其中的一些库变得很困难。我还没有找到一种方法，在保留所有 TypScript 类型的同时，将 [apollo-link-state](https://github.com/apollographql/apollo-link-state) 和 [apollo-angular-link-http](https://www.apollographql.com/docs/angular/basics/network-layer.html) 与 AWSAppSyncClient 一起使用。
> 这意味着在直接使用 AWSAppSyncClient 时，没有角 HttpClient 和拦截器之类的特性！

## 离线助手

AWS AppSync SDK 提供了[助手](https://github.com/awslabs/aws-mobile-appsync-sdk-js/blob/master/OFFLINE_HELPERS.md)来构建与 apollo-client 一起使用的变异对象。这使得在恶劣的网络条件下使用乐观的 UI 并在应用程序发生变化时更新本地 InMemoryCache 变得非常容易。

react 有更高级别的帮助器可用，但在使用其他库时，我们需要使用更低级别的函数`buildMutation`。例如，这个函数需要一个 apollo-client 实例，我们可以在自己的 typescript 函数中将其抽象出来。因为我们已经为我们的 API 生成了类型，所以当提供`__typename`字符串时，我们也可以使用这些类型来给我们额外的类型安全性。

我还发现使用配置对象比使用长参数列表更容易。这里是我的脱机变异构建器，你可以把它放在一个文件`libs/appsync/src/lib/offline.helpers.ts` :

```
import { OperationVariables } from 'apollo-client';
import {
  buildMutation,
  CacheOperationTypes,
  CacheUpdatesOptions,
  VariablesInfo
} from 'aws-appsync';
import { DocumentNode } from 'graphql';
import { AppSyncClient } from './appsync.module';

export interface TypeNameMutationType {
  __typename: string;
}

export interface GraphqlMutationInput<T, R extends TypeNameMutationType> {
  /** DocumentNode for the mutation */
  mutation: DocumentNode;
  /** An object with the mutation variables */
  variablesInfo: T | VariablesInfo<T>;
  /** The queries to update in the cache */
  cacheUpdateQuery: CacheUpdatesOptions;
  /** __typename from your schema */
  typename: R['__typename'];
  /** The name of the field with the ID (optional) */
  idField?: string;
  /** Override for the operation type (optional) */
  operationType?: CacheOperationTypes;
}

/**
 * Builds a MutationOptions object ready to be used by the ApolloClient to automatically update the cache according to the cacheUpdateQuery
 * parameter
 */
export function graphqlMutation<
  T = OperationVariables,
  R extends TypeNameMutationType = TypeNameMutationType
>({
  mutation,
  variablesInfo,
  cacheUpdateQuery,
  typename,
  idField,
  operationType
}: GraphqlMutationInput<T, R>) {
  return buildMutation<T>(
    AppSyncClient,
    mutation,
    variablesInfo,
    cacheUpdateQuery,
    typename,
    idField,
    operationType
  );
}

export function executeMutation<
  T = OperationVariables,
  R extends TypeNameMutationType = TypeNameMutationType
>(mutationInput: GraphqlMutationInput<T, R>) {
  return AppSyncClient.mutate(graphqlMutation<T, R>(mutationInput));
} 
```

> 请注意，这可能会与 appsync sdk 的未来版本相冲突

## 从库中导出所有内容

为了能够导入我们的类型、graphql 操作、助手、appsync-client 和 amplify，我们需要将它们导出到`libs/appsync/src/index.ts`文件中。

```
export * from './lib/appsync.module';
export * from './lib/api.types';
export * from './lib/gql-operations';
export * from './lib/offline.helpers'; 
```

要将它们导入到我们的组件中，我们可以简单地编写

```
import { /* ... */ } from '@my/appsync'; 
```

## 重述

到目前为止，我们已经使用 NX 设置了 Angular 多应用工作区，使用 AWS Cognito 初始化了 AWS Amplify，为我们提供了一个托管认证服务，并使用 AWS AppSync 生成了一个无服务器的 GraphQL API。只需几个 CLI 命令！

为了能够在多个应用中重用我们的后端，我们在一个单独的定制库中导入和配置了所有东西。

由于 GraphQL 使用模式，我们甚至可以自动生成所有类型，并复制查询和变异操作，作为我们自己操作需求的起点。

我们还编写了一些实用函数来帮助离线场景中的 Apollo 客户端缓存。

## 第二部分期待什么

在[第 2 部分](https://dev.to/beavearony/creating-angular-ui-components-and-connecting-them-to-the-graphql-appsync-api-part-2-33n0)中，我们将创建一个 UI，并使用我们在这里创建的东西连接到我们的后端。

[![beavearony image](img/d4a98c8b3973de5b2679b8ac142d8db9.png)](/beavearony) [## 创建角度 UI 组件并将它们连接到 graph QL app sync API——第 2 部分

### 迈克尔·古特曼

#angular #graphql #awsamplify #awsappsync](/beavearony/creating-angular-ui-components-and-connecting-them-to-the-graphql-appsync-api-part-2-33n0)
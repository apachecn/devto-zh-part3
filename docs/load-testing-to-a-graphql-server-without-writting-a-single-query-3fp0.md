# 在不编写任何查询的情况下对 GraphQL 服务器进行负载测试🔥

> 原文：<https://dev.to/estrada9166/load-testing-to-a-graphql-server-without-writting-a-single-query-3fp0>

对您的服务器进行负载测试是很重要的，它可以帮助您在有大量请求时识别可能的行为。

我决定创造 [`easygraphql-lt`](https://github.com/EasyGraphQL/easygraphql-lt) 来帮助我和你。 [`easygraphql-lt`](https://github.com/EasyGraphQL/easygraphql-lt) 背后的主要思想是用尽可能少的配置进行负载测试，这就是我接下来要解释的:

## 如何运行它:

```
$ npx easygraphql-lt <CONFIG_FILE>.json 
```

## 如何使用:

创建一个 JSON 文件，该文件应包含以下值:

#### 配置:

##### 网址(必填)

将用于进行负载测试的 URL，它将用于获取 GraphQL 模式的最新副本
,并进行所有查询/转换。

```
"url":  "http://localhost:7000/" 
```

##### Name (Optional):

这将是测试将要使用的名称；这是可选的，如果没有设置
，它将显示 URL 作为名称。

```
"name":  "Testing my new server" 
```

##### 选中查询(可选)

您可以选择一个您想要测试的查询/突变列表，为此，您必须用要测试的查询的名称创建一个
字符串数组；这是可选的，如果你没有创建它，所有的查询都将被测试。

```
"selectedQueries":  ["createUser",  "searchUser"] 
```

#### 查询文件(可选)

您可以选择是否要保存一个包含所有已测试查询的`json`文件。
如果你什么都不通过，那就没救了。默认值为`false`。

```
"queryFile":  true/false 
```

#### 突变(可选)

你也可以用 [`easygraphql-lt`](https://github.com/EasyGraphQL/easygraphql-lt) 来测试
你的突变；如果您没有传递任何东西，它只会测试查询。
默认值为`false`。
*如果你设置了`withMutations: true`，不要忘记在参数*上添加输入值

```
"withMutations":  true/false 
```

#### 持续时间(可选)

您可以选择测试的持续时间。

```
"duration":  5 
```

#### 到达率(可选)

您可以选择测试的到达率。

```
"arrivalRate":  10 
```

#### 火炮输出(可选)

你可以用[cannon . io](https://artillery.io/)，
创建一个`JSON`文件，其中包含负载测试的结果。在测试结束时，终端会显示一条消息，解释如何运行这个结果。如果没有设置，默认为
`false`。

```
"withOutput":  true/false 
```

#### args

在这里，您应该设置所有可能在负载测试中使用的参数，并且如果
`withMutations`是`true`，您应该将使用的值放在`mutation`上。

**注意:**如果你要使用一个字符串数组，它应该像这样创建`"[\"a\", \"b\"]"`

### JSON 文件示例

```
{  "config":  {  "url":  "http://localhost:7000/",  "name":  "Testing my new server",  "selectedQueries":  ["createUser",  "searchUser"],  "queryFile":  true,  "withMutations":  true,  "duration":  5,  "arrivalRate":  10,  "withOutput":  true  },  "args":  {  "getFamilyInfoByIsLocal":  {  "isLocal":  true,  "test":  "[\"a\", \"b\"]",  "age":  40,  "name":  "Demo Test"  },  "searchUser":  {  "name":  "Demo User"  },  "createUser":  {  "name":  "Demo User"  },  "createCity":  {  "input":  {  "name":  "Demo Name",  "country":  "Demo Country"  }  }  }  } 
```

* * *

*   如果你喜欢这个项目，别忘了在 GitHub 上给个⭐️奖。
*   完整文档[此处](https://github.com/EasyGraphQL/easygraphql-lt)。
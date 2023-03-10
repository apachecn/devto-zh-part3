# 测试图 Java 简介

> 原文：<https://dev.to/email2vimalraj/introducing-test-graphql-java-4i7h>

最近我的一个朋友问我，我们如何在 Java 中测试 GraphQL APIs。他目前正在探索`Karate's`的能力，然而，他不想仅仅为了这个而使用`Karate`，因为他们已经在使用基于`TestNG`的框架。还有一个 [graphql-java](https://github.com/graphql-java/graphql-java) 库，让你用 java 实现`GraphQL`并进行测试，但使用 Spring Boot。我的主要目标不是仅仅为了测试`GraphQL` API 而介绍 Spring Boot。我想保持简单！

目标是:

*   应该不会带来各种依赖(这个库目前只依赖`jackson`)。
*   应该只是把 graphql 文件变成请求有效载荷字符串。
*   应该能够使用任何 HTTP 客户端库。
*   应该能够使用任何 Java 测试框架。

这个图书馆就是这样诞生的。让我们直接跳到如何使用它。

## 入门

添加 maven 依赖项:

```
<dependency>
    <groupId>com.vimalselvam</groupId>
    <artifactId>test-graphql-java</artifactId>
    <version>1.0.0</version>
</dependency> 
```

我不使用`Gradle`，但是添加这个库作为`Gradle`依赖项应该是很简单的。

我们来测试一下 [Pokemon GraphQL API](https://graphql-pokemon.now.sh/) 。我们将测试以下查询:

```
query pokemon {
  pokemon(name: "Pikachu") {
    name
  }
} 
```

我们将触发这个查询，并断言成功的响应代码和响应体，其中`name`键包含`Pikachu`。

将上面的查询放到 maven 项目目录中的`src/test/resources/graphql/`下的`pokemon.graphql`中。

我们可以使用以下两种方法之一在测试中加载该文件:

*   使用`InputStream`:

```
InputStream iStream = getClass().getResourceAsStream("/graphql/pokemon.graphql"); 
```

*   使用`File`:

```
File file = new File("src/test/resources/graphql/pokemon.graphql"); 
```

对于这个例子，我使用第二种方法，`File`。

一旦您读取了文件，只需将它传递给`GraphqlTemplate`类进行如下解析:

```
String graphqlPayload = GraphqlTemplate.parseGraphql(file, null); 
```

第二个参数是`variables`，用于参数化 GraphQL 查询。一会儿我会告诉你如何使用它，直到让我们保持它`null`。

就是这样！现在您已经有了 graphql 查询字符串，可以将它作为请求负载直接传递到您首选的 HTTP 客户端库。

再说说`variables`。`GraphQL`具有设置一些`variables`的特性，并在运行时在查询执行期间传递这些变量。为此，我们将修改我们的`Pokemon`查询。

打开`pokemon.graphql`文件，并将其更改为:

```
query pokemon($name:String!) {
  pokemon(name: $name) {
    name
  }
} 
```

这里的`$name`是变量，它只接受`String`。`!`运算符表示这是强制变量。让我们看看如何在代码中处理这个`variables`场景。

在将 GraphQL 查询转换为普通字符串之前，让我们创建变量:

```
ObjectNode variables = new ObjectMapper().createObjectNode();
variables.put("name", "Pikachu"); 
```

这里我们使用`com.fasterxml.jackson.databind.node.ObjectNode`来创建变量。这个`ObjectNode`可以作为我们的`GraphqlTemplate`类中的第二个参数传递。

```
String graphqlPayload = GraphqlTemplate.parseGraphql(file, variables); 
```

就这么简单！

我已经开源了这个库，可以在这里找到:[https://github.com/vimalrajselvam/test-graphql-java](https://github.com/vimalrajselvam/test-graphql-java)。

欢迎投稿。如果您有任何想法或问题，请在上面的 github 链接中打开问题。

让我们看看完整的示例代码:

```
package com.vimalselvam.graphql;

import java.io.*;

import com.fasterxml.jackson.databind.JsonNode;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.node.ObjectNode;

import org.testng.Assert;
import org.testng.annotations.Test;

import okhttp3.*;

public class TestClass {
    private static final OkHttpClient client = new OkHttpClient();
    private final String graphqlUri = "https://graphql-pokemon.now.sh/graphql";

    private Response prepareResponse(String graphqlPayload) throws IOException {
        RequestBody body = RequestBody.create(MediaType.get("application/json; charset=utf-8"), graphqlPayload);
        Request request = new Request.Builder().url(graphqlUri).post(body).build();
        return client.newCall(request).execute();
    }

    @Test
    public void testGraphqlWithInputStream() throws IOException {
        // Read a graphql file as an input stream
        InputStream iStream = TestClass.class.getResourceAsStream("/graphql/pokemon.graphql");

        // Create a variables to pass to the graphql query
        ObjectNode variables = new ObjectMapper().createObjectNode();
        variables.put("name", "Pikachu");

        // Now parse the graphql file to a request payload string
        String graphqlPayload = GraphqlTemplate.parseGraphql(iStream, variables);

        // Build and trigger the request
        Response response = prepareResponse(graphqlPayload);

        Assert.assertEquals(response.code(), 200, "Response Code Assertion");

        String jsonData = response.body().string();
        JsonNode jsonNode = new ObjectMapper().readTree(jsonData);
        Assert.assertEquals(jsonNode.get("data").get("pokemon").get("name").asText(), "Pikachu");
    }

    @Test
    public void testGraphqlWithFile() throws IOException {
        // Read a graphql file
        File file = new File("src/test/resources/graphql/pokemon.graphql");

        // Create a variables to pass to the graphql query
        ObjectNode variables = new ObjectMapper().createObjectNode();
        variables.put("name", "Pikachu");

        // Now parse the graphql file to a request payload string
        String graphqlPayload = GraphqlTemplate.parseGraphql(file, variables);

        // Build and trigger the request
        Response response = prepareResponse(graphqlPayload);

        Assert.assertEquals(response.code(), 200, "Response Code Assertion");

        String jsonData = response.body().string();
        JsonNode jsonNode = new ObjectMapper().readTree(jsonData);
        Assert.assertEquals(jsonNode.get("data").get("pokemon").get("name").asText(), "Pikachu");
    }

    @Test
    public void testGraphqlWithNoVariables() throws IOException {
        // Read a graphql file
        File file = new File("src/test/resources/graphql/pokemon-with-no-variable.graphql");

        // Now parse the graphql file to a request payload string
        String graphqlPayload = GraphqlTemplate.parseGraphql(file, null);

        // Build and trigger the request
        Response response = prepareResponse(graphqlPayload);

        Assert.assertEquals(response.code(), 200, "Response Code Assertion");

        String jsonData = response.body().string();
        JsonNode jsonNode = new ObjectMapper().readTree(jsonData);
        Assert.assertEquals(jsonNode.get("data").get("pokemon").get("name").asText(), "Pikachu");
    }
} 
```
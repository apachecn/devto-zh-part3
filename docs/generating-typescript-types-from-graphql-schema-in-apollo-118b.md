# 在 Apollo 中从 GraphQL 模式生成 TypeScript 类型

> 原文：<https://dev.to/leighhalliday/generating-typescript-types-from-graphql-schema-in-apollo-118b>

这篇文章最初发表在 https://www.leighhalliday.com/generating-types-apollo 的我的博客上

[GraphQL](https://graphql.org/) 是一种类型化语言，那么当我们应该能够利用来自 GraphQL 的类型并让它们自动为我们生成时，为什么要在我们的 [TypeScript](https://www.typescriptlang.org/) 代码中重新定义所有的类型呢？这正是我们用[阿波罗工具](https://github.com/apollographql/apollo-tooling)命令`codegen:generate`所能做的。

本文面向熟悉 GraphQL 和 TypeScript 基础知识，但希望了解它们如何在 React 应用程序中无缝协作的人。我们将介绍这个命令、它的关键选项，以及在使用 [Shopify 的 Storefront GraphQL API](https://help.shopify.com/en/api/custom-storefronts/storefront-api) 添加产品列表时遇到的一些问题。

这个项目的源代码可以在这里找到:[https://github.com/leighhalliday/apollo-generating-types](https://github.com/leighhalliday/apollo-generating-types)

[https://www.youtube.com/embed/1PVrZNi3sb8](https://www.youtube.com/embed/1PVrZNi3sb8)

## 一个 GraphQL 查询

我们将要运行的查询如下，可以将其复制并粘贴到 [GraphQL Explorer](https://help.shopify.com/en/api/graphiql) 中，以查看其结果:

```
query  ProductsQuery($preferredContentType:  ImageContentType)  {  products(first:  10)  {  edges  {  node  {  id  title  description  updatedAt  ...ProductImages  }  }  }  }  fragment  ProductImages  on  Product  {  images(first:  3)  {  edges  {  node  {  id  transformedSrc(  maxWidth:  150  maxHeight:  100  preferredContentType:  $preferredContentType  )  }  }  }  } 
```

我们使用以下变量:

```
{  "preferredContentType":  "JPG"  } 
```

## 设置

在这个项目中，我们正在使用 create-react-app，如果你以前没有在 CRA 项目中使用过 TypeScript，你很幸运，因为设置非常容易。这篇文章的目的不是展示 GraphQL 如何与 React 一起工作，正如我之前在文章中和我的 [YouTube 频道](https://youtube.com/leighhalliday)中多次提到的那样，但是为了彻底起见，`App`组件的提供者是这样设置的，并带有一个额外的提供者层来支持 GraphQL 挂钩的使用。

```
import React, { Component } from "react";
import { ApolloProvider } from "react-apollo";
import { ApolloProvider as ApolloHooksProvider } from "react-apollo-hooks";
import createClient from "./apolloClient";
import Products from "./Products";
import "./App.css";

const client = createClient();

export default function App() {
  return (
    <ApolloProvider client={client}>
      <ApolloHooksProvider client={client}>
        <Products />
      </ApolloHooksProvider>
    </ApolloProvider>
  );
} 
```

## 命令

你可以传递给`apollo codegen:generate`命令[的选项的完整列表可以在这里](https://github.com/apollographql/apollo-tooling#apollo-clientcodegen-output)找到。我在这个例子中使用的基本命令如下:

```
--excludes=node_modules/*
--includes=**/*.tsx
--endpoint https://graphql.myshopify.com/api/graphql
--header \"X-Shopify-Storefront-Access-Token: 078bc5caa0ddebfa89cccb4a1baa1f5c\"
--target typescript
--tagName=gql
--outputFlat src/generated 
```

*   排除:生成类型时不要查看哪些文件
*   包括:生成类型时要查看哪些文件
*   端点:可以找到 GraphQL 模式的地方
*   header:从端点 URL 获取模式时传递这个头
*   目标:类型输出...可以是流程、打字稿等...我们这里需要打字稿
*   tagName:当你导入`graphql-tag`时，你给它取了什么名字？
*   outputFlat:您是希望所有生成的文件都放在一个地方，还是放在生成它们的每个文件旁边。我更喜欢一个单独的地方，这样在任何时候都更容易清除/替换。

我们可以在我们的`package.json`文件中添加一个带有这些选项的定制脚本(以及我们将在下面提到的几个其他选项),以便更容易地生成类型:

```
{  "scripts":  {  "apollo:generate":  "apollo codegen:generate --excludes=node_modules/* --includes=**/*.tsx --endpoint https://graphql.myshopify.com/api/graphql --header \"X-Shopify-Storefront-Access-Token: 078bc5caa0ddebfa89cccb4a1baa1f5c\" --target typescript --tagName gql --outputFlat src/generated --passthroughCustomScalars --customScalarsPrefix Shopify"  }  } 
```

### 阿波罗配置文件

我看到它说我们需要定义一个 Apollo 配置文件- [apollo.config.js](https://www.apollographql.com/docs/references/apollo-config.html) -即使它最终基本上是空的。如果你遇到这个错误，在你的文件夹的根目录下定义`apollo.config.js`，即使基本上清空它也应该能解决问题。

```
module.exports = {
  client: {}
}; 
```

## 使用类型

我们可以扩展`Query`(或`Mutation`)类，传入通过运行 codegen 命令产生的类型，允许作为响应接收的数据和发送给查询的变量被[静态类型化](https://www.apollographql.com/docs/react/recipes/static-typing.html)。

```
import { Query } from "react-apollo";

class ProductsQuery extends Query<ProductsData, ProductsDataVariables> {}

// Use ProductsQuery rather than Query in the component 
```

使用钩子，你可以将类型传递给`useQuery`和`useMutation`函数:

```
const { data, loading } = useQuery<ProductsData, ProductsDataVariables>(
  PRODUCTS_QUERY,
  {
    variables: { preferredContentType: ImageContentType.JPG },
    ssr: false
  }
); 
```

## 自定义标量

Shopify 的 GraphQL API 定义了许多自定义标量值，如`URL`(一个符合 RFC 3986 和 RFC 3987 的 URI 字符串)和`DateTime`(一个 ISO-8601 编码的 UTC 日期时间字符串)。通过传递下面提到的两个选项，我们可以将这些类型传递给我们的应用程序并定义自定义类型。为了避免与同名的预定义类型冲突，使用前缀是很重要的。

```
--passthroughCustomScalars
--customScalarsPrefix Shopify 
```

使用`global.d.ts`我们可以定义 Shopify 的自定义标量类型:

```
type ShopifyURL = string;
type ShopifyDateTime = string; 
```

## 我们的分量

最后看看我们为这个例子构建的组件，我们将从导入开始。我们已经导入了一些在运行上面显示的 codegen 命令时生成的类型。

```
import React from "react";
import { useQuery } from "react-apollo-hooks";
import gql from "graphql-tag";
import { ProductsData, ProductsDataVariables } from "./generated/ProductsData";
import { ImageContentType } from "./generated/globalTypes";

const PRODUCTS_QUERY = gql` --exact same query as example above-- `; 
```

既然已经定义了我们的导入和查询，我们可以使用`PRODUCTS_QUERY`和已经导入的类型来执行查询并显示它的响应。注意我们如何使用在`globalTypes`文件中生成的`ImageContentType`枚举将类型化值作为变量传递给我们的查询。

```
export default function Products() {
  // Pass in the types imported, the first one is the data response
  // the second one are the variables required by the query
  const { data, loading } = useQuery<ProductsData, ProductsDataVariables>(
    PRODUCTS_QUERY,
    {
      variables: { preferredContentType: ImageContentType.JPG },
      ssr: false
    }
  );

  // Handle loading state or when there is no data
  if (loading || !data) {
    return <div>Loading products...</div>;
  }

  // Render a response now that we have the data
  return (
    <div>
      {data.products.edges.map(({ node: product }) => (
        <div key={product.id}>
          <h2>{product.title}</h2>
          <ul className="images">
            {product.images.edges.map(({ node: image }, index) => (
              <li className="image-item" key={image.id || index}>
                <img src={image.transformedSrc} />
              </li>
            ))}
          </ul>
        </div>
      ))}
    </div>
  );
} 
```

如果你想看看使用`Query`组件是什么样子，可以在下面找到:

```
// same imports as above
// same query definition as above

class ProductsQuery extends Query<ProductsData, ProductsDataVariables> {}

export default function Products() {
  return (
    <ProductsQuery
      query={PRODUCTS_QUERY}
      variables={{ preferredContentType: ImageContentType.JPG }}
    >
      {({ data, loading }) => {
        if (loading || !data) {
          return <div>Loading products...</div>;
        }

        return (
          <div>
            {data.products.edges.map(({ node: product }) => (
              <div key={product.id}>
                <h2>{product.title}</h2>
                <ul className="images">
                  {product.images.edges.map(
                    ({ node: image }, index: number) => (
                      <li className="image-item" key={image.id || index}>
                        <img src={image.transformedSrc} />
                      </li>
                    )
                  )}
                </ul>
              </div>
            ))}
          </div>
        );
      }}
    </ProductsQuery>
  );
} 
```

## 明白了

以下是您在使用 GraphQL / TypeScript 代码生成时可能遇到的一些问题:

*   缺少`customScalarsPrefix`选项...可能有与默认类型冲突的类型，比如`URL`。
*   如果全局文件中没有类型，TS 可能会抱怨`isolatedModules`设置(这是 create-react-app 所需要的)。
*   使用`outputFlat`选项要求每个类型在整个应用程序中是唯一的。
*   您需要给每个查询起一个名字`query ProductsData { ... }`

## 结论

类型系统允许静态分析和代码生成，使用 Apollo 工具库使我们能够通过生成 GraphQL 查询使用的类型来利用这一点，甚至在查询试图错误地使用引用字段或引用字段不存在时警告我们。我希望你喜欢看到如何使用它来提高质量，但主要是为了避免你的头发，我们已经定义了重写类型！
# 在前端和后端测试 GraphQL！

> 原文：<https://dev.to/estrada9166/testing-graphql-on-the-front-end-and-backend-4c42>

对您的代码进行一些测试总是一个您应该实现的好习惯。你所做的测试将帮助你防止一些错误，也将确保你的应用程序如你所想的那样工作。

有时进行测试可能很困难，需要大量的代码，但大多数时候这取决于您用来测试代码的实现；有一些包可以帮助你用几行代码进行测试。

今天，我将使用`easygraphql-tester`展示一个选项，在前端和后端测试您的 GraphQL 查询、变异和订阅。

`easygraphql-tester`可用于模拟查询、突变、订阅；此外，它还可以用作断言和测试解析器。

## 前端测试。

如果我们想在前端测试我们的 GraphQL 查询和变异，我们应该遵循下面的步骤:

*   安装软件包:`npm i -D easygraphql-tester`-或- `yarn add -D easygraphql-tester`
*   在`package.json`上，您可以创建一个新的脚本，它将从服务器下载 GraphQL 模式，如下所示:
    *   `"prepare-schema": "npx get-graphql-schema <YOUR_SERVER_URL> > schema.json"`。
    *   在运行测试之前，运行脚本`npm run prepare-schema`以获得 GraphQL 模式的最新版本。
*   安装您最喜欢的测试运行程序。
*   初始化该类，并传递模式。

### 常用查询和突变:

```
const CREATE_ITEM_MUTATION = gql`
  mutation CREATE_ITEM_MUTATION(
    $title: String!
    $description: String!
    $price: Int!
    $image: String
    $largeImage: String
  ) {
    createItem(
      title: $title
      description: $description
      price: $price
      image: $image
      largeImage: $largeImage
    ) {
      id
    }
  }
`;

const SINGLE_ORDER_QUERY = gql`
  query SINGLE_ORDER_QUERY($id: ID!) {
    order(id: $id) {
      id
      charge
      total
      createdAt
      user {
        id
      }
      items {
        id
        title
        description
        price
        image
        quantity
      }
    }
  }
`; 
```

### 使用 Jest 和 easygraphql-tester - assertion 的例子。

```
'use strict'

import EasyGraphQLTester from 'easygraphql-tester';
import fs from 'fs';
import path from 'path';

import { CREATE_ITEM_MUTATION } from '../components/CreateItem';
import { SINGLE_ORDER_QUERY } from '../components/Order';

const schema = fs.readFileSync(path.join(__dirname, '..', 'schema.graphql'), 'utf8');

const tester = new EasyGraphQLTester(schema);

it('should pass CREATE_ITEM_MUTATION', () => {
  tester.test(true, CREATE_ITEM_MUTATION, {
    title: 'New item',
    description: 'This is going to be my new item',
    price: 10
  })
});

it('should pass SINGLE_ORDER_QUERY', () => {
  tester.test(true, SINGLE_ORDER_QUERY, { 
    id: '1' 
  })
}); 
```

### 使用 Jest 和 easygraphql-tester - mock 的例子:

您也可以使用 mock 方法来测试您的视图是否正确地呈现了预期的数据，您可以在那里设置一些`fixtures`

```
'use strict'

import EasyGraphQLTester from 'easygraphql-tester';
import fs from 'fs';
import path from 'path';

import { CREATE_ITEM_MUTATION } from '../components/CreateItem';
import { SINGLE_ORDER_QUERY } from '../components/Order';

const schema = fs.readFileSync(path.join(__dirname, '..', 'schema.graphql'), 'utf8');

const tester = new EasyGraphQLTester(schema);

it('should pass CREATE_ITEM_MUTATION', () => {
  const variables = {
    title: 'New item',
    description: 'This is going to be my new item',
    price: 10
  };

  const result = tester.mock({
    query: CREATE_ITEM_MUTATION,
    variables
  });

  expect(typeof result.data.createItem.id).toBe('string');
});

it('should pass SINGLE_ORDER_QUERY', () => {
  const result = tester.mock({
    query: SINGLE_ORDER_QUERY,
    variables: {
      id: '1'
    },
    fixture: {
      data: {
        order: {
          id: '1',
          createdAt: '03-11-2019',
          items: [{
            id: '1234'
          }]
        }
      }
    }
  });

  expect(result.data.order.id).toBe('1');
  expect(result.data.order.createdAt).toBe('03-11-2019');
  expect(result.data.order.items[0].id).toBe('1234');
  expect(typeof result.data.order.total).toBe('number');
  expect(typeof result.data.order.items[0].description).toBe('string');
}); 
```

## 后端测试:

如果我们想在后端测试我们的 GraphQL 查询和变异，我们应该遵循下面的步骤:

*   安装软件包:`npm i -D easygraphql-tester`-或- `yarn add -D easygraphql-tester`
*   安装您最喜欢的测试运行程序并准备测试。
*   初始化该类，并传递模式。

**注:**

*   如果没有使用 graphql-js，可以将解析器作为第二个参数传递给构造函数，以便测试解析器。

初始化该类后，可以使用 graphql 方法，它将接收 4 个参数，唯一需要的是第一个参数，这些参数是:

*   查询:要测试的查询/变异。
*   rootValue:它将是传递给解析器的 rootValue。
*   contextValue:它将是传递给解析器的上下文。
*   variableValues:这将是查询/变异将要使用的变量。

### 解析器

```
"use strict";

const license = (__, args, ctx) => {
  const { key } = args;

  return {
    id: "1234",
    body: "This is a test license",
    description: `This is a description with key ${key}`
  };
};

module.exports = {
  Query: {
    license
  }
}; 
```

### 用 Jest 测试。

```
"use strict";

const fs = require("fs");
const path = require("path");
const { expect } = require("chai");
const EasyGraphQLTester = require("easygraphql-tester");

const resolvers = require("../resolvers");
const schemaCode = fs.readFileSync(
  path.join(__dirname, "..", "schema.gql"),
  "utf8"
);

describe("Test resolvers", () => {
  let tester;
  beforeAll(() => {
    tester = new EasyGraphQLTester(schemaCode, resolvers);
  });

  it("should return expected values", async () => {
    const query = `
      query GET_LICENSE($key: String!) {
        license(key: $key) {
          id
          body
          description
        }
      }
    `;

    const args = {
      key: "1234"
    };

    const result = await tester.graphql(query, {}, {}, args);
    expect(result.data.license.id).to.be.eq("1234");
    expect(result.data.license.body).to.be.eq("This is a test license");
    expect(result.data.license.description).to.be.eq(
      `This is a description with key ${args.key}`
    );
  });
}); 
```

* * *

如果你喜欢这个包，别忘了在 [GitHub](https://github.com/EasyGraphQL/easygraphql-tester) 上给⭐️

[演示](https://codesandbox.io/s/42m2rx71j4)

[回购](https://github.com/EasyGraphQL/easygraphql-tester)

[网站](https://easygraphql.com/)
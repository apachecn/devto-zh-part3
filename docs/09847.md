# 使用 GraphQL 模式指令进行基于角色的授权

> 原文：<https://dev.to/tushark1/-------using-graphql-schema-directives-for-role-based-authorization---------3jgo>

当使用 node.js 在基于 graphql 的 api 后端工作时，如果您偶然发现基于角色的授权，有很多方法可以授权登录的用户。

您可以将授权逻辑与 graphql 完全分开(在一个控制器中)，您可以将逻辑编写在解析器本身中(增加解析器中的代码量)，或者为了保持代码清晰易懂，编写 GraphQL 自定义模式指令。

下面是如何在 graphql 中编写自定义模式指令，以授权特定的角色。

```
//HasRoleDirective.js

import { SchemaDirectiveVisitor } from "apollo-server-express";
import {
  GraphQLDirective,
  DirectiveLocation,
  GraphQLList,
  defaultFieldResolver
} from "graphql";
import { ensureAuthenticated } from "../controllers/authController";
import { AuthorizationError } from "../errors";

class HasRoleDirective extends SchemaDirectiveVisitor {
  static getDirectiveDeclaration(directiveName, schema) {
    return new GraphQLDirective({
      name: "hasRole",
      locations: [DirectiveLocation.FIELD_DEFINITION],
      args: {
        roles: {
          type: new GraphQLList(schema.getType("Role"))
        }
      }
    });
  }
  visitFieldDefinition(field) {
    const { resolve = defaultFieldResolver } = field;
    const roles = this.args.roles;
    field.resolve = async function(...args) {
      const [, , context] = args;
      await ensureAuthenticated(context);
      const userRoles = context.me.role;

      if (roles.some(role => userRoles.indexOf(role) !== -1)) {
        const result = await resolve.apply(this, args);
        return result;
      }
      throw new AuthorizationError({
        message: "You are not authorized for this resource"
      });
    };
  }
}
export default HasRoleDirective; 
```

Enter fullscreen mode Exit fullscreen mode

首先，当接受参数时，我们声明指令名和它接受的有效参数。
稍后在字段定义`visitFieldDefinition(field)`中，将描述逻辑，我们接受参数，从参数中提取 context rt，函数调用`ensureAuthenticated(context)`从上下文中检查 jwtToken，我已经在 jwt token 中包含了用户的角色。
于是`HasRole`指令宣告并准备使用。要使用它，您需要将指令传递给 graphql config，并在 typeDefinition 中声明它，如下所示

```
// GraphQL Config
const schemaDirectives = { hasRole: HasRoleDirective };
const server = new ApolloServer({
  typeDefs,
  resolvers,
  schemaDirectives,
  ...context
});

//typedefinitions
import { gql } from "apollo-server-express";
export default gql`
  directive @hasRole(roles: [String!]) on FIELD_DEFINITION | FIELD
  scalar Date

  type Query {
    _: String
  }
  type Mutation {
    _: String
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

通过这种方式，您将能够在 typeDefs
示例中使用自定义模式指令，该示例演示了如何使用自定义模式指令:

```
import { gql } from "apollo-server-express";

export default gql`
  extend type Query {
    businesses: [Business!] @hasRole(roles: [THIS_SUPER_ADMIN])
    business(id: ID!): Business @hasRole(roles: [THIS_ADMIN, THIS_SUPER_ADMIN])
  }
  extend type Mutation {
    businessUpdate(name: String!): Business!
      @hasRole(roles: [THIS_ADMIN, THIS_SUPER_ADMIN])
  }
  type Business {
    id: ID!
    name: String!
  }
`; 
```

Enter fullscreen mode Exit fullscreen mode

另一个例子，验证用户是否被认证

```
//AuthDirective.js
import { SchemaDirectiveVisitor } from "apollo-server-express";
import { defaultFieldResolver } from "graphql";
import { ensureAuthenticated } from "../controllers/authController";

class AuthDirective extends SchemaDirectiveVisitor {
  visitFieldDefinition(field) {
    const { resolve = defaultFieldResolver } = field;

    field.resolve = async function(...args) {
      const [, , context] = args;
      await ensureAuthenticated(context);
      return resolve.apply(this, args);
    };
  }
}
export default AuthDirective;
//passing to graphql config
const schemaDirectives = { auth: AuthDirective };
const server = new ApolloServer({
  typeDefs,
  resolvers,
  schemaDirectives,
  ...context
});

//declaration in typeDefinitions
import { gql } from "apollo-server-express";
export default gql`
  directive @auth on FIELD_DEFINITION
  scalar Date

  type Query {
    _: String
  }
  type Mutation {
    _: String
  }
`;
//typedefinitions usage
`extend type Query {
    payments(
      customer: ID
      status: String
      cursor: String
      limit: Int
    ): PaymentCollection! @auth
  }
  ` 
```

Enter fullscreen mode Exit fullscreen mode

如果您需要对一个查询/变异应用多个指令，注意:您在 graphql 查询中编写指令的方向是从右到左，首先解析最右边的指令，然后再解析它左边的指令。
所以说如果你有这个

```
`extend type Query {
    payments(
      customer: ID
      status: String
      cursor: String
      limit: Int
    ): PaymentCollection! @hasRole(roles: [THIS_ADMIN, THIS_SUPER_ADMIN]) @auth
  }` 
```

Enter fullscreen mode Exit fullscreen mode

首先解析 auth 指令，在 auth 指令之后解析 hasRoles。假设 auth 指令失败，则 hasRoles 指令永远不会到达。

* * *

我仍然在 GraphQL 中学习很多，如果上面的代码有任何改进或错误，请留下评论，
从我们犯的错误中学习总是更好的:)

* * *
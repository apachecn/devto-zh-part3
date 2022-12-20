# 建立了一个非常规的数据库

> 原文：<https://dev.to/andywer/built-an-unconventional-database-thing---feedback-5e52>

嘿伙计们！

我刚刚用 JavaScript 和 TypeScript 代码构建了一个静态评估 SQL 查询的工具: [Postguard](https://github.com/andywer/postguard) 。

它诞生于在没有查询构建器的情况下编写显式数据库查询的愿望，但是是可静态分析的，并且构建在 Babel 和 TypeScript 编译器 API 之上。

## 它有什么作用？

你这样查询数据库:

```
import { sql } from "squid/pg"
import database from "./database"
import { UserRecord } from "./schema"

export async function queryUserById(id: string): Promise<UserRecord | null> {
  const { rows } = await database.query<UserRecord>(sql`
    SELECT * FROM users WHERE if = ${id} `)
  return rows[0] || null
} 
```

为您的表定义一个模式。当使用 TypeScript:
时，记录类型可以从模式中派生

```
// schema.ts
import { defineTable, Schema, TableRow } from "squid"

export type UserRecord = TableRow<typeof usersTable>

const usersTable = defineTable("users", {
  id: Schema.Number,
  name: Schema.String,
  created_at: Schema.Date
}) 
```

现在让我们运行`postguard`来验证我们刚刚写的:

```
$ npx postguard ./sample/*.ts

✖ Query validation failed in sample/test.ts:7:32:

  No table in the query's scope has a column "if".
  Tables in scope: "users"

   5 | export async function queryUserByID(id: number) {
   6 |   const { rows } = await database.query<UserRecord>(sql`
>  7 |     SELECT * FROM users WHERE if = ${id}
     |                               ^
   8 |   `)
   9 |   return rows[0] || null
  10 | } 
```

啊，我们的查询中有一个打字错误！来修一下:

```
const { rows } = await database.query<UserRecord>(sql`
  SELECT * FROM users WHERE id = ${id} `) 
```

让我们再运行一次:

```
$ npx postguard ./sample/*.ts

✔ Validated 1 queries against 1 table schemas. All fine! 
```

## 更高级的东西

让我们使用前面的示例代码，更改选择查询:

```
import { sql } from "squid/pg"
import database from "./database"
import { UserRecord } from "./schema"

export async function queryUserById(id: string): Promise<UserRecord | null> {
  const { rows } = await database.query<UserRecord>(sql`
    SELECT id, name FROM users WHERE id = ${id} `)
  return rows[0] || null
} 
```

```
$ npx postguard ./sample/*.ts

✖ Query validation failed in sample/test.ts:6:40:

  Query's result does not match the expected result type.
    Missing columns in result rows: "created_at"
    Actual columns in result rows: "id", "name"

  4 |
  5 | export async function queryUserByID(id: number) {
> 6 |   const { rows } = await database.query<UserRecord>(sql`
    |                                        ^^^^^^^^^^^^
  7 |     SELECT id, name FROM users WHERE id = ${id}
  8 |   `)
  9 |   return rows[0] || null 
```

发生了什么事？我们定义我们期望查询返回类型为`UserRecord` :
`await database.query<UserRecord>(/*...*/)`的`rows`

Postguard 评估了该查询，并注意到该 SELECT 查询的结果行与 TypeScript 类型`UserRecord`不匹配，因为该类型有一个`created_at`属性。

修复很简单:

```
import { sql } from "squid/pg"
import database from "./database"
import { UserRecord } from "./schema"

export async function queryUserById(id: string): Promise<UserRecord | null> {
  const { rows } = await database.query<UserRecord>(sql`
    SELECT * FROM users WHERE id = ${id} `)
  return rows[0] || null
} 
```

```
$ npx postguard ./sample/*.ts

✔ Validated 1 queries against 1 table schemas. All fine! 
```

## 为什么？

那我为什么还要花时间在这上面呢？与 ORM 共事多年后，我慢慢地越来越厌倦它们。查询生成器似乎是一个更好的解决方案。

现在，查询构建器的问题是，您可以有效地编写 SQL 查询，但不是作为 SQL 查询；您可以使用查询构建器的专有 API 来制定它们。现在我需要阅读两个复杂的接口:我的 Postgres 的奇特的 SQL 特性和查询构建器的全面的 API...

编写优秀的老 SQL 感觉像是自然的方式，所以回到那个。但是现在我对我的代码失去了所有的信心，需要保持一个非常高的测试覆盖率，因为我不能静态地推理我的查询。我实际上需要运行它们来看看它们是否工作。

进入阶段:后卫。回归自信，缩短反馈周期。

## 反馈

所以这个工具还非常年轻，还不应该用于生产代码。

尽管如此，我还是希望听到一些关于整体方法的反馈！

这个概念可能看起来有点深奥，但是代码感觉很自然。

分享你的任何想法:)

黑客快乐！
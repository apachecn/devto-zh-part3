# 使用 Knex 和迁移进行端到端 API 测试

> 原文：<https://dev.to/dinos_vl/end-to-end-api-testing-using-knex-migrations-4bje>

当我们构建一个 API 的时候，让一些测试覆盖一般的流程，尽可能少的嘲笑，通常是一个好主意。

大多数情况下，您的 API 将使用/依赖于一个或多个数据库。一方面，如果我们可以测试我们的 API 而不嘲笑任何与数据库相关的东西，那将是非常好的，但另一方面，我们应该避免使用我们实际的数据库进行测试，**即使我们实际上事后清理了我们的混乱**。

## 数据库迁移拯救世界

事实证明，在这种情况下，数据库迁移非常方便。如果我们能够在测试之前创建一个新的数据库，迁移它，播种它(如果需要的话)，然后在测试之后回滚并删除该数据库，就像什么都没发生过一样，那将是非常理想的。

## Knex

在节点应用程序中，我们可以使用 Knex 实现类似的功能。我们需要做的就是使用 Knex 创建一个新的测试数据库，然后应用我们已经为现有的非测试数据库实现的相同迁移设置。

## 这会是什么样子

**您已经存在的迁移文件。**这是您已经用于数据库的迁移。这也是我们将用来为我们的测试准备临时测试数据库的迁移。
注意，您也可以有一个种子文件，这样如果需要的话，您就可以直接将数据植入您的表中。

```
import * as Knex from 'knex';

export async function up(knex: Knex): Promise<any> {
  return () =>
    knex.schema.createTable('table1', (t) => {
      t.increments('id')
        .primary()
        .notNullable()
        .unique('table1');
      // more columns
    });
    // more tables
}

export async function down(knex: Knex): Promise<any> {
  return () => knex.schema.dropTable('table1');
} 
```

Enter fullscreen mode Exit fullscreen mode

你的测试。注意，在每个测试套件的开始和结束，我们分别进行迁移和回滚，所以每个测试套件都可以有一个新的测试数据库。

```
const config = {
  client: 'postgres',
  debug: true,
  connection: {
    host: 'localhost',
    database: 'test_database',
    port: '5432',
    password: '',
    user: ''            
  },
  migrations: {
    directory: './migrations'
  },
  seeds: {
    directory: './seeds'
  }
};
const knex = require('knex')(config);

describe('test suite description', () => {
  beforeAll(() => {
    return knex.migrate.latest();
    // you can here also seed your tables, if you have any seeding files
  });
  afterAll(() => {
    return knex.migrate
      .rollback()
      .then(() => knex.destroy());
  });

  test('test 1', () => {
    // testing stuff
  });
  test('test 2', () => {
    // testing other stuff
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将使用 Jest 的选项 globalSetup 和 globalTeardown，这样我们就可以创建我们的数据库，并分别在每次 Jest 调用前后删除它。

**为全局设置:**

```
import * as knex from 'knex';

function getDbConnection() {
  return knex({
    client: 'postgres',
    debug: true,
    connection: {
      host: "localhost"
      database: "postgres",
      port: "5432",
      password: "",
      user: "",            
    }
  });
}

async function createDatabase() {
  const dbConnection = getDbConnection();

  try {
    await dbConnection.raw('CREATE DATABASE test_database');
  } catch (err) {
    console.log(err);
  } finally {
    await dbConnection.destroy();
  }
}

module.exports = async () => {
  await createDatabase();
}; 
```

Enter fullscreen mode Exit fullscreen mode

**为全球拆机:**

```
import * as knex from 'knex';

async function deleteDatabase() {
  const dbConnection = knex({
    client: 'postgres',
    debug: true,
    connection: {
      host: "localhost"
      database: "postgres",
      port: "5432",
      password: "",
      user: "",            
    }
  });

  try {
    await dbConnection.raw('DROP DATABASE IF EXISTS test_database');
  } catch (err) {
    console.log(err);
  } finally {
    await dbConnection.destroy();
  }
}

module.exports = async () => {
  await deleteDatabase();
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以运行 Jest，并且知道我们将拥有一个临时的测试数据库，其模式与我们的原始数据库相同。

## 总结

我们可以使用 Knex 来获得一个临时数据库，用它我们可以测试 API 的流程和端点，而不必模仿任何与数据库相关的东西。我们可以确定数据库表的模式将与原始模式相同，因为我们将使用构建普通数据库时使用的相同迁移文件。

PS:这是我第一次写东西，所以我真的很感谢反馈和建设性的批评。
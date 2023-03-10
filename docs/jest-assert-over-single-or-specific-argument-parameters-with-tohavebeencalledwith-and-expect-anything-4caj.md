# Jest 断言单个或特定的参数。toHaveBeenCalledWith 和 expect.anything()

> 原文：<https://dev.to/hugo__df/jest-assert-over-single-or-specific-argument-parameters-with-tohavebeencalledwith-and-expect-anything-4caj>

> 使用 Jest，可以使用`.toHaveBeenCalled` / `.toBeCalled`和`expect.anything()`断言模拟函数调用的单个或特定参数。

完整的示例存储库在 github.com/HugoDF/jest-specific-argument-assert 的第 17-66 行，更具体地说是在 src/pinger.test.js 文件的第 17-66 行。

您可以使用`expect.anything()`来忽略调用模拟 Jest 函数时使用的某些参数，如下所示:

```
test('calls getPingConfigs with right accountId, searchRegex', async () => {
  await pinger(1);
  expect(mockPingConfig).toHaveBeenCalledWith(
    1,
    expect.anything(),
    expect.anything(),
    new RegExp('.*')
  );
}); 
```

请继续阅读，了解被测代码的更多细节，以及为什么要使用这种方法。

测试中的代码遵循模块边界，类似于[用 Docker Compose、Express 和 Postgres](https://dev.to/hugo__df/an-enterprise-style-node-js-rest-api-setup-with-docker-compose-express-and-postgres-2hm0-temp-slug-2013945) 设置企业风格 Node.js REST API 中描述的内容。特别是一个 [3 层(表示层、域层、数据层)](https://codewithhugo.com/node-postgres-express-docker-compose/#architecture-example-user-session-management)，我们只实现了域层和(伪)数据层。

## 保证特定参数/自变量断言的被测代码

测试中的代码如下([参见 GitHub](https://github.com/HugoDF/jest-specific-argument-assert/blob/master/src/pinger.js) 上完整的 src/pinger.js 文件)，只有相关的代码被包含进来，以使我们用 Jest mocks、`.toHaveBeenCalled`和`expect.anything()`解决的问题变得显而易见。

```
// Half-baked implementation of an uptime monitor
const { getPingConfigs } = require('./pingConfig');

async function getUrlsForAccount(accountId, offset, limit, searchRegex) {
  const configs = await getPingConfigs(accountId, offset, limit, searchRegex);
  // return configs.map(conf => conf.url);
}

async function pinger(accountId, { offset = 0, limit = 50 } = {}, search) {
  const searchRegex = search
    ? new RegExp(search.split('  ').join('|'))
    : new RegExp('.*');
  const urls = await getUrlsForAccount(accountId, offset, limit, searchRegex);
}

module.exports = pinger; 
```

唯一一个超出模块私有上下文的调用是`getPingConfigs(accountId, offset, limit, searchRegex)`。这就是为什么断言将出现在我们用`jest.mock('./pingConfig', () => {})`设置的`getPingConfigs` mock 上([参见 GitHub](https://github.com/HugoDF/jest-specific-argument-assert/blob/master/src/pinger.test.js) 上完整的 src/pinger.test.js 代码)。

### 发现被测代码中的正交性

我们还可以看到有正交功能在进行。即:

*   `accountId`的通过
*   计算/默认/传递搜索正则表达式
*   抵销/限额的默认/通过

### 正交功能详尽测试用例的问题

我们所有的测试都将围绕调用`getPingConfigs`的值(使用`.toHaveBeenCalledWith`断言)。

让我们创建一些不利用`expect.anything()`的测试，在每个调用中，我们将指定每个参数的值给`getPingConfigs` : `accountId`，`offset`，`limit`和`searchRegex`。

置换，(`Y`表示传递给`pinger`的变量被设置，`N`表示未被设置)。

| 帐户 Id | 抵消 | 限制 | 搜索 | 单字搜索 |
| --- | --- | --- | --- | --- |
| Y | 普通 | 普通 | Y | Y |
| Y | 普通 | 普通 | Y | 普通 |
| Y | 普通 | Y | 普通 | 不适用的 |
| Y | Y | Y | 普通 | 不适用的 |
| Y | 普通 | 普通 | Y | Y |
| Y | 普通 | 普通 | Y | 普通 |
| Y | Y | 普通 | Y | Y |
| Y | Y | 普通 | Y | 普通 |
| Y | Y | Y | Y | Y |
| Y | Y | Y | Y | 普通 |

如果我们必须在`getPingConfigs`调用的断言中指定每个参数/自变量，那么上面的每种排列都会导致不同的测试用例。

我们上面所做的列举将会产生 **10 个测试用例**。

### 为正交功能创建测试用例

事实证明，以下案例涵盖了与我们关心的相同的逻辑:

1.  在搜索中
    1.  如果 search 是**没有**设置，`pinger`应该用默认的 searchRegex 调用
    2.  如果设置了搜索并且是单个单词(无空格)，`pinger`应该用正确的 searchRegex 调用
    3.  如果设置了搜索并且是多工作(空格)，`pinger`应该用正确的 searchRegex 调用
2.  在极限/偏移时
    1.  如果未设置**限制/偏移**，则`pinger`应调用默认值
    2.  如果设置了限制/偏移，`pinger`应该调用传递的值

注意断言只涉及到调用的*部分*，这正是`expect.anything()`派上用场的地方，因为它不需要同时断言模拟调用的所有参数/自变量。

## 模拟函数调用时特定参数断言

下面实现了我们在[“为正交功能创建测试用例”](https://codewithhugo.com/jest-specific-argument-parameter-assert/#creating-test-cases-for-orthogonal-functionality) :
中定义的测试用例

```
describe('without search', () => {
  test('calls getPingConfigs with right accountId, searchRegex', async () => {
    await pinger(1);
    expect(mockPingConfig).toHaveBeenCalledWith(
      1,
      expect.anything(),
      expect.anything(),
      new RegExp('.*')
    );
  });
});
describe('offset, limit', () => {
  test('calls getPingConfigs with passed offset and limit', async () => {
    await pinger(1, { offset: 20, limit: 100 });
    expect(mockPingConfig).toHaveBeenCalledWith(
      1,
      20,
      100,
      expect.anything()
    );
  });
  test('calls getPingConfigs with default offset and limit if undefined', async () => {
    await pinger(1);
    expect(mockPingConfig).toHaveBeenCalledWith(1, 0, 50, expect.anything());
  });
});
describe('search', () => {
  describe('single-word search', () => {
    test('calls getPingConfigs with right accountId, searchRegex', async () => {
      await pinger(1, {}, 'search');
      expect(mockPingConfig).toHaveBeenCalledWith(
        1,
        expect.anything(),
        expect.anything(),
        new RegExp('search')
      );
    });
  });
  describe('multi-word search', () => {
    test('calls getPingConfigs with right accountId, searchRegex', async () => {
      await pinger(1, {}, 'multi word search');
      expect(mockPingConfig).toHaveBeenCalledWith(
        1,
        expect.anything(),
        expect.anything(),
        new RegExp('multi|word|search')
      );
    });
  });
}); 
```

## 进一步阅读

前往[github.com/HugoDF/jest-specific-argument-assert](https://github.com/HugoDF/jest-specific-argument-assert)查看完整的代码和测试套件。这包括与用 Jest `.toHaveBeenCalledWith` / `.toBeCalled`和`expect.anything()`说明特定自变量/参数断言的概念无关的代码和测试。

代码的编写方式松散地遵循了[用 Docker Compose、Express 和 Postgres](https://dev.to/hugo__df/an-enterprise-style-node-js-rest-api-setup-with-docker-compose-express-and-postgres-2hm0-temp-slug-2013945) 设置企业风格 Node.js REST API 中描述的内容。特别是一个 [3 层(表示层、域层、数据层)](https://codewithhugo.com/node-postgres-express-docker-compose/#architecture-example-user-session-management)，我们只实现了域层和(伪)数据层。
# 用 Jest 或 sinon 表达请求和回应的指南

> 原文：<https://dev.to/hugo__df/a-guide-to-express-request-and-response-mockingstubbing-with-jest-or-sinon-fnh>

为了测试一个快速处理程序，了解如何成功模仿/存根`request`和`response`对象是很有用的。下面的例子将使用 Jest 和 sinon(在 AVA 运行)编写。

这样做的理由如下。Jest 是一个非常流行的“一体化”测试框架。Sinon 是最受欢迎的“JavaScript 独立测试间谍、存根和模拟”之一，它“适用于任何单元测试框架”。

本文中详细介绍的方法是关于如何独立于 Express app 实例测试处理程序，方法是用模拟的请求(`req`)和响应(`res`)对象直接调用它们。这只是测试 Express handlers 和中间件的一种方法。另一种方法是启动 Express 服务器(最好是在内存中使用 SuperTest)。在[“使用 SuperTest、moxios 和 Jest 测试 Express 应用程序”](https://dev.to/hugo__df/testing-an-express-app-with-supertest-moxios-and-jest-4f44)中，我将详细介绍如何实现这一点。

用模拟的请求/响应测试 Express 应用程序的一个大的概念飞跃是理解如何模拟一个链式 API。

这是通过从它的每个方法:
返回`res`实例来实现的

```
const mockResponse = {
  const res = {};
  // replace the following () => res
  // with your function stub/mock of choice
  // making sure they still return `res`
  res.status = () => res;
  res.json = () => res;
  return res;
}; 
```

请参见位于[github.com/HugoDF/mock-express-request-response](https://github.com/HugoDF/mock-express-request-response)的示例库和工作应用。

目录:

## 存根和模拟:Jest.fn vs sinon

`jest.fn`和`sinon.stub`作用相同。它们都返回一个函数的模拟/存根。这只是意味着一个函数，它能回忆起关于它的调用的信息，例如，调用了多少次，使用了什么参数。

Jest mock 与框架的其余部分紧密集成。这意味着我们可以有如下的断言:

```
test('jest.fn recalls what it has been called with', () => {
  const mock = jest.fn();
  mock('a', 'b', 'c');
  expect(mock).toHaveBeenCalledTimes(1);
  expect(mock).toHaveBeenCalledWith('a', 'b', 'c');
}); 
```

Sinon“仅仅”是一个 spies/stubs/mocks 库，这意味着我们需要一个单独的测试运行程序，下面的例子相当于之前的 Jest，但是使用了 AVA:

```
const test = require('ava');
const sinon = require('sinon');
test('sinon.stub recalls what it has been called with', t => {
  const mock = sinon.stub();
  mock('a', 'b', 'c');
  t.true(mock.called);
  t.true(mock.calledWith('a', 'b', 'c'));
}); 
```

## 嘲讽/存根链接的 API:快速响应

快速用户域 API 是基于中间件的。一个中间件，以请求(通常称为`req`)、响应(通常称为`res`)和下一个(调用下一个中间件)作为参数。

“路由处理器”是一个不倾向于调用`next`的中间件，它通常会导致一个响应被发送。

下面是一些路由处理程序的示例(在 express-handlers.js 中)。

在这个例子中，`req.session`是由`client-sessions`生成的，T1 是 Mozilla 的一个中间件，它设置一个加密的 cookie，这个 cookie 在客户端上设置(使用一个`Set-Cookie`)。这超出了本文的范围。对于所有意图和目的，我们可以访问/写入任何其他组的请求/响应属性。

```
async function logout(req, res) {
  req.session.data = null;
  return res.status(200).json();
}
async function checkAuth(req, res) {
  if (!req.session.data) {
    return res.status(401).json();
  }
  const { username } = req.session.data;
  return res.status(200).json({ username });
}

module.exports = {
  logout,
  checkAuth
}; 
```

它们通过被“装载”在一个快速应用程序(`app`)实例(在 app.js 中):
上而被消耗

```
const express = require('express');
const app = express();

const { logout, checkAuth } = require('./express-handlers.js');

app.get('/session', checkAuth);
app.delete('/session', logout); 
```

为了让上面的代码以集成的方式工作，我们还需要像这样对`client-sessions`进行`app.use`打包。注意，`cookieName`很重要，因为它是在`req`对象上设置会话的属性。

我们还添加了`express.json`中间件(Express 4.16+)，它的工作方式类似于 body-parser 的`.json()`选项 ie。它解析 JSON 主体并将输出存储到`req.body`中。

```
const express = require('express');
const app = express();
const session = require('client-sessions');

app.use(express.json());
app.use(session({
  secret: process.env.SESSION_SECRET || 'my-super-secret',
  cookieName: 'session',
  duration: 60 * 60 * 1000 // 1 hour
}));

const { logout, checkAuth } = require('./express-handlers.js');

app.get('/session', checkAuth);
app.delete('/session', logout); 
```

### 用 Jest 或 sinon 嘲讽/打断 req(一个简单的明示请求)

一个 mockRequest 函数需要返回一个请求兼容的对象，这是一个普通的 JavaScript 对象，它可能看起来像下面这样，这取决于被测试的代码正在使用什么属性。我们的代码只访问`req.session.data`，这意味着它期望`req`拥有一个作为对象的`session`属性，这样它就可以尝试访问`req.session.data`属性。

```
const mockRequest = (sessionData) => {
  return {
    session: { data: sessionData },
  };
}; 
```

由于上面只是处理数据，所以开玩笑地嘲笑它或使用 sinon 和您选择的测试运行程序(摩卡、AVA、磁带、茉莉……)没有区别。

### 用笑话嘲弄/打断(一种简单的表达反应)

mockResponse 函数如下所示，我们的测试代码只调用了`status`和`json`函数。我们遇到的问题是调用是连锁的。这意味着`status`、`json`和其他`res` (Express response)方法返回`res`对象本身。

这意味着理想情况下，我们的模拟将以同样的方式运行:

```
const mockResponse = () => {
  const res = {};
  res.status = jest.fn().mockReturnValue(res);
  res.json = jest.fn().mockReturnValue(res);
  return res;
}; 
```

我们利用`jest.fn`的`mockReturnValue`方法将`status`和`json`的返回值设置到模拟响应实例(`res`)上。

### 用 sinon 嘲讽/揶揄 res(一种简单的明示反应)

与上面等价的 sinon(有类似的解释)如下。对于 sinon，我们必须显式地`require`它，因为它是一个独立的库(即不是由测试框架注入的)。

Sinon 存根有一个`returns`方法，其行为类似于`mockReturnValue` Jest mock 方法。它设置存根的返回值。

我们的模拟响应实例(`res`)上的`status`和`json`方法返回响应实例(`res`)本身。

```
const sinon = require('sinon');

const mockResponse = () => {
  const res = {};
  res.status = sinon.stub().returns(res);
  res.json = sinon.stub().returns(res);
  return res;
}; 
```

### 使用 status 和 json()测试从`req`读取并发送一个`res`的处理程序

`checkAuth`处理器从`req`读取数据，并使用`status()`和`json()`发送一个`res`。

它包含以下逻辑，如果未设置`session.data`，则未设置会话，因此用户未通过身份验证，因此它发送一个带有空 JSON 主体的`401 Unauthorized`状态。否则，它用 200 状态码反映 JSON 响应中的部分会话内容(只有`username`)。

下面是测试中的代码(在 express-handlers.js 中):

```
async function checkAuth(req, res) {
  if (!req.session.data) {
    return res.status(401).json();
  }
  const { username } = req.session.data;
  return res.status(200).json({ username });
} 
```

我们需要测试两条路径:一条通向 401，另一条通向 200。

在 GitHub[GitHub . com/Hugo df/mock-express-request-response/releases/tag/check-auth-tests](https://github.com/HugoDF/mock-express-request-response/releases/tag/check-auth-tests)上查看这段代码的快照(点击 commit sha 查看版本变更的差异)。

使用我们之前定义的`mockRequest`和`mockResponse`，我们将设置一个没有会话数据(对于 401)但有包含用户名的会话数据(对于 200)的请求。然后我们将检查`req.status`是否分别用 401 和 200 调用。在 200 的情况下，我们还将检查用正确的有效载荷(`{ username }`)调用了`res.json`。

在 Jest 中(参见 express-handlers . Jest-test . js):

```
describe('checkAuth', () => {
  test('should 401 if session data is not set', async () => {
    const req = mockRequest();
    const res = mockResponse();
    await checkAuth(req, res);
    expect(res.status).toHaveBeenCalledWith(401);
  });
  test('should 200 with username from session if session data is set', async () => {
    const req = mockRequest({ username: 'hugo' });
    const res = mockResponse();
    await checkAuth(req, res);
    expect(res.status).toHaveBeenCalledWith(200);
    expect(res.json).toHaveBeenCalledWith({ username: 'hugo' });
  });
}); 
```

使用 sinon + AVA 的相同测试(在 express-handlers.sinon-test.js 中):

```
test('checkAuth > should 401 if session data is not set', async (t) => {
  const req = mockRequest();
  const res = mockResponse();
  await checkAuth(req, res);
  t.true(res.status.calledWith(401));
});

test('checkAuth > should 200 with username from session if data is set', async (t) => {
  const req = mockRequest({ username: 'hugo' });
  const res = mockResponse();
  await checkAuth(req, res);
  t.true(res.status.calledWith(200));
  t.true(res.json.calledWith({ username: 'hugo' }));
}); 
```

> 在 GitHub[GitHub . com/Hugo df/mock-express-request-response/releases/tag/check-auth-tests](https://github.com/HugoDF/mock-express-request-response/releases/tag/check-auth-tests)上查看这段代码的快照(点击 commit sha 查看版本变更的差异)。

### 使用 status 和 json()测试写入`req`并发送`res`的处理程序

`logout`处理器写入 req(它将`req.session.data`设置为`null`，并使用`res.status`和`res.json`发送响应。下面是测试中的代码。

```
async function logout(req, res) {
  req.session.data = null;
  return res.status(200).json();
} 
```

它没有任何分支逻辑，但是我们应该在两个单独的测试中测试`session.data`是否被重置以及是否发送了响应。在 GitHub[GitHub . com/Hugo df/mock-express-request-response/releases/tag/logout-tests](https://github.com/HugoDF/mock-express-request-response/releases/tag/logout-tests)上查看这段代码的快照(点击 commit sha 查看版本变更的差异)。

在 Jest 中，使用`mockRequest`和`mockResponse`函数(在 express-handlers.jest-test.js 中):

```
describe('logout', () => {
  test('should set session.data to null', async () => {
    const req = mockRequest({ username: 'hugo' });
    const res = mockResponse();
    await logout(req, res);
    expect(req.session.data).toBeNull();
  });
  test('should 200', async () => {
    const req = mockRequest({ username: 'hugo' });
    const res = mockResponse();
    await logout(req, res);
    expect(res.status).toHaveBeenCalledWith(200);
  });
}); 
```

在 AVA + sinon 中使用 mockRequest 和 mockResponse 函数(在 express-handlers.sinon-test.js 中):

```
test('logout > should set session.data to null', async (t) => {
  const req = mockRequest({ username: 'hugo' });
  const res = mockResponse();
  await logout(req, res);
  t.is(req.session.data, null);
});
test('logout > should 200', async (t) => {
  const req = mockRequest({ username: 'hugo' });
  const res = mockResponse();
  await logout(req, res);
  t.true(res.status.calledWith(200));
}); 
```

> 在 GitHub[GitHub . com/Hugo df/mock-express-request-response/releases/tag/logout-tests](https://github.com/HugoDF/mock-express-request-response/releases/tag/logout-tests)上查看这段代码的快照(点击 commit sha 查看版本变更的差异)。

## 一个复杂的处理程序请求/响应模拟场景:一个用 body 登录的请求

我们的登录处理程序完成了应用程序中最繁重的工作。它在`express-handlers.js`中，包含以下逻辑。

登录处理程序首先验证`req.body`和 400s 的内容是否丢失(这将是我们的前两个测试)。

然后登录处理程序尝试给定用户名的`getUser`，如果没有这样的用户，它 401s(这将是我们的第三个测试)。

接下来，登录处理程序将来自请求的密码与来自`getUser`输出的散列/加盐版本进行比较，如果比较失败，则返回 401s(这将是我们的第四次测试)。

最后，如果用户名/密码对用户有效，登录处理程序将 session.data 设置为`{ username }`并发送 201 响应(这将是我们的第 5 个测试)。

有意义的最后一个测试(我还没有实现)是检查处理程序是否在执行过程中发生错误时发送 500(例如`getUser` throws)。

> 查看 GitHub 上的代码快照[GitHub . com/Hugo df/mock-express-request-response/releases/tag/log in-tests](https://github.com/HugoDF/mock-express-request-response/releases/tag/login-tests)(点击 commit sha 查看版本变更的差异)。

登录函数如下，为了可读性，我省略了`getUser`。`getUser`在任何情况下都是作为硬编码的数组查找来实现的，而在您的应用程序中，它将是某种数据库或 API 调用(除非您使用 oAuth)。

```
const bcrypt = require('bcrypt');

async function login(req, res) {
  try {
    const { username, password } = req.body;
    if (!username || !password) {
      return res.status(400).json({ message: 'username and password are required' });
    }
    const user = getUser(username);
    if (!user) {
      return res.status(401).json({ message: 'No user with matching username' });
    }
    if (!(await bcrypt.compare(password, user.password))) {
      return res.status(401).json({ message: 'Wrong password' });
    }
    req.session.data = { username };
    return res.status(201).json();
  } catch (e) {
    console.error(`Error during login of "${req.body.username}": ${e.stack}`);
    res.status(500).json({ message: e.message });
  }
} 
```

在`app.js` :
中，通过“安装”在快速应用程序上来消耗

```
app.post('/session', login); 
```

为了能够测试登录函数，我们需要扩展`mockRequest`函数，它仍然返回一个普通的 JavaScript 对象，所以我们的 Jest 和 AVA + sinon 版本没有区别:

```
const mockRequest = (sessionData, body) => ({
  session: { data: sessionData },
  body,
}); 
```

> 查看 GitHub 上的代码快照[GitHub . com/Hugo df/mock-express-request-response/releases/tag/log in-tests](https://github.com/HugoDF/mock-express-request-response/releases/tag/login-tests)(点击 commit sha 查看版本变更的差异)。

### 使用 Jest 测试登录处理程序

彻底测试这个 Express 处理程序还需要一些测试，但基本上与`checkAuth`和`logout`处理程序的原理相同。

测试如下所示(在 express-handlers.jest-test.js 中):

```
describe('login', () => {
  test('should 400 if username is missing from body', async () => {
    const req = mockRequest(
      {},
      { password: 'boss' }
    );
    const res = mockResponse();
    await login(req, res);
    expect(res.status).toHaveBeenCalledWith(400);
    expect(res.json).toHaveBeenCalledWith({
      message: 'username and password are required'
    });
  });
  test('should 400 if password is missing from body', async () => {
    const req = mockRequest(
      {},
      { username: 'hugo' }
    );
    const res = mockResponse();
    await login(req, res);
    expect(res.status).toHaveBeenCalledWith(400);
    expect(res.json).toHaveBeenCalledWith({
      message: 'username and password are required'
    });
  });
  test('should 401 with message if user with passed username does not exist', async () => {
    const req = mockRequest(
      {},
      {
        username: 'hugo-boss',
        password: 'boss'
      }
    );
    const res = mockResponse();
    await login(req, res);
    expect(res.status).toHaveBeenCalledWith(401);
    expect(res.json).toHaveBeenCalledWith({
      message: 'No user with matching username'
    });
  });
  test('should 401 with message if passed password does not match stored password', async () => {
    const req = mockRequest(
      {},
      {
        username: 'guest',
        password: 'not-good-password'
      }
    );
    const res = mockResponse();
    await login(req, res);
    expect(res.status).toHaveBeenCalledWith(401);
    expect(res.json).toHaveBeenCalledWith({
      message: 'Wrong password'
    });
  });
  test('should 201 and set session.data with username if user exists and right password provided', async () => {
    const req = mockRequest(
      {},
      {
        username: 'guest',
        password: 'guest-boss'
      }
    );
    const res = mockResponse();
    await login(req, res);
    expect(res.status).toHaveBeenCalledWith(201);
    expect(res.json).toHaveBeenCalled();
    expect(req.session.data).toEqual({
      username: 'guest',
    });
  });
}); 
```

> 查看 GitHub 上的代码快照[GitHub . com/Hugo df/mock-express-request-response/releases/tag/log in-tests](https://github.com/HugoDF/mock-express-request-response/releases/tag/login-tests)(点击 commit sha 查看版本变更的差异)。

### 使用 AVA + sinon 测试登录处理程序

同样，这些测试没有什么本质上的新内容，它们只是更密集，更接近于您在现实世界应用程序中所做的事情，如下所示(在 express-handlers.sinon-test.js 中):

> 查看 GitHub 上的代码快照[GitHub . com/Hugo df/mock-express-request-response/releases/tag/log in-tests](https://github.com/HugoDF/mock-express-request-response/releases/tag/login-tests)(点击 commit sha 查看版本变更的差异)。

```
test('login > should 400 if username is missing from body', async (t) => {
  const req = mockRequest(
    {},
    { password: 'boss' }
  );
  const res = mockResponse();
  await login(req, res);
  t.true(res.status.calledWith(400));
  t.true(res.json.calledWith({
    message: 'username and password are required'
  }));
});
test('should 400 if password is missing from body', async (t) => {
  const req = mockRequest(
    {},
    { username: 'hugo' }
  );
  const res = mockResponse();
  await login(req, res);
  t.true(res.status.calledWith(400));
  t.true(res.json.calledWith({
    message: 'username and password are required'
  }));
});
test('should 401 with message if user with passed username does not exist', async (t) => {
  const req = mockRequest(
    {},
    {
      username: 'hugo-boss',
      password: 'boss'
    }
  );
  const res = mockResponse();
  await login(req, res);
  t.true(res.status.calledWith(401));
  t.true(res.json.calledWith({
    message: 'No user with matching username'
  }));
});
test('should 401 with message if passed password does not match stored password', async (t) => {
  const req = mockRequest(
    {},
    {
      username: 'guest',
      password: 'not-good-password'
    }
  );
  const res = mockResponse();
  await login(req, res);
  t.true(res.status.calledWith(401));
  t.true(res.json.calledWith({
    message: 'Wrong password'
  }));
});
test('should 201 and set session.data with username if user exists and right password provided', async (t) => {
  const req = mockRequest(
    {},
    {
      username: 'guest',
      password: 'guest-boss'
    }
  );
  const res = mockResponse();
  await login(req, res);
  t.true(res.status.calledWith(201));
  t.true(res.json.called);
  t.deepEqual(
    req.session.data,
    { username: 'guest' }
  );
}); 
```

> 查看 GitHub 上的代码快照[GitHub . com/Hugo df/mock-express-request-response/releases/tag/log in-tests](https://github.com/HugoDF/mock-express-request-response/releases/tag/login-tests)(点击 commit sha 查看版本变更的差异)。

## 测试中间件并模仿 Express request.get 头

另一个您可能想要模仿/存根 Express 请求和响应对象的场景是在测试中间件功能时。

测试中间件略有不同。很多中间件都有什么都不做的条件(只是调用`next()`)。一个 Express 中间件应该总是调用`next()`(它的第三个参数)或者发送一个响应。

这里有一个示例中间件，它允许使用格式为`Bearer {API_KEY}`的`Authorization`报头中的 API 密钥进行认证。

除了中间件与处理程序的区别，`headerAuth`还使用了`req.get()`，它用于从 Express 请求中获取消息头。

我省略了`apiKeyToUser`和`isApiKey`。`apiKeyToUser`只是从 apiKeys 到用户名的查找。在现实世界的应用程序中，这将是一个数据库查找，就像在`login`代码中替换`getUser`一样。

```
function headerAuth(req, res, next) {
  if (req.session.data) {
    return next()
  }
  const authenticationHeader = req.get('authorization')
  if(!authenticationHeader) {
    return next()
  }
  const apiKey = authenticationHeader
    .replace('Bearer', '')
    .trim();
  if (!isApiKey(apiKey)) {
    return next()
  }
  req.session.data = { username: apiKeyToUser[apiKey] };
  next();
} 
```

> 查看 GitHub 上的代码快照[GitHub . com/Hugo df/mock-express-request-response/releases/tag/middleware-header-tests](https://github.com/HugoDF/mock-express-request-response/releases/tag/middleware-header-tests)(点击 commit sha 查看版本变更的差异)。

### 更新 mockRequest 支持访问头

这是一个不同版本的 mockRequest，它仍然是一个普通的 JavaScript 对象，它模仿`req.get`只是为了让测试通过:

```
const mockRequest = (authHeader, sessionData) => ({
  get(name) {
    if (name === 'authorization') return authHeader
    return null
  },
  session: { data: sessionData }
}); 
```

### 测试用 Jest 访问头的中间件

大多数测试检查在中间件执行时会话上没有任何变化，因为它有许多短路条件。

注意我们如何传递一个无操作函数`() => {}`作为第三个参数(也就是`next`)。

```
describe('headerAuthMiddleware', () => {
  test('should set req.session.data if API key is in authorization and is valid', async () => {
    const req = mockRequest('76b1e728-1c14-43f9-aa06-6de5cbc064c2');
    const res = mockResponse();
    await headerAuthMiddleware(req, res, () => {});
    expect(req.session.data).toEqual({ username: 'hugo' });
  });
  test('should not do anything if req.session.data is already set', async () => {
    const req = mockRequest('76b1e728-1c14-43f9-aa06-6de5cbc064c2', { username: 'guest' });
    const res = mockResponse();
    await headerAuthMiddleware(req, res, () => {});
    expect(req.session.data).toEqual({ username: 'guest' });
  });
  test('should not do anything if authorization header is not present', async () => {
    const req = mockRequest(undefined);
    const res = mockResponse();
    await headerAuthMiddleware(req, res, () => {});
    expect(req.session.data).toBeUndefined();
  });
  test('should not do anything if api key is invalid', async () => {
    const req = mockRequest('invalid-api-key');
    const res = mockResponse();
    await headerAuthMiddleware(req, res, () => {});
    expect(req.session.data).toBeUndefined();
  });
}); 
```

> 查看 GitHub 上的代码快照[GitHub . com/Hugo df/mock-express-request-response/releases/tag/middleware-header-tests](https://github.com/HugoDF/mock-express-request-response/releases/tag/middleware-header-tests)(点击 commit sha 查看版本变更的差异)。

### 测试使用 AVA + sinon 访问报头的中间件

大多数测试检查在中间件执行时会话上没有任何变化，因为它有许多短路条件。

注意我们如何传递一个无操作函数`() => {}`作为第三个参数(也就是`next`)。

```
test('should set req.session.data if API key is in authorization and is valid', async (t) => {
  const req = mockRequest('76b1e728-1c14-43f9-aa06-6de5cbc064c2');
  const res = mockResponse();
  await headerAuthMiddleware(req, res, () => {});
  t.deepEqual(
    req.session.data,
    { username: 'hugo' }
  );
});
test('should not do anything if req.session.data is already set', async (t) => {
  const req = mockRequest('76b1e728-1c14-43f9-aa06-6de5cbc064c2', { username: 'guest' });
  const res = mockResponse();
  await headerAuthMiddleware(req, res, () => {});
  t.deepEqual(
    req.session.data,
    { username: 'guest' }
  );
});
test('should not do anything if authorization header is not present', async (t) => {
  const req = mockRequest(undefined);
  const res = mockResponse();
  await headerAuthMiddleware(req, res, () => {});
  t.is(req.session.data, undefined);
});
test('should not do anything if api key is invalid', async (t) => {
  const req = mockRequest('invalid-api-key');
  const res = mockResponse();
  await headerAuthMiddleware(req, res, () => {});
  t.is(req.session.data, undefined);
}); 
```

> 查看 GitHub 上的代码快照[GitHub . com/Hugo df/mock-express-request-response/releases/tag/middleware-header-tests](https://github.com/HugoDF/mock-express-request-response/releases/tag/middleware-header-tests)(点击 commit sha 查看版本变更的差异)。

## 测试快递处理程序和中间件的关键

按照本帖中描述的方式有效地测试 Express 有几个关键。

首先是理解代码做什么。这比看起来要难。JavaScript 测试主要是关于理解 JavaScript，一点是关于测试工具，一点是理解被测应用程序中使用的工具。以便用正确的数据类型模拟工具的返回值。

帖子中的所有测试都归结为理解什么是`req`、`res`和`next`(一个对象、一个对象和一个函数)。它们具有/可以具有哪些属性，如何使用这些属性，以及它们是函数还是对象。

这只是测试 Express handlers 和中间件的一种方法。另一种方法是启动 Express 服务器(最好是在内存中使用 SuperTest)。我将在[“使用 SuperTest、moxios 和 Jest 测试 Express 应用程序”](https://dev.to/hugo__df/testing-an-express-app-with-supertest-moxios-and-jest-4f44)中详细介绍如何实现这一点

[无刷标志
克里斯·巴比利斯](https://unsplash.com/@cbarbalis?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)
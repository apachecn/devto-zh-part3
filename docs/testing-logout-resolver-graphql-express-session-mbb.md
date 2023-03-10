# 测试注销解析器(GraphQL & express-session)

> 原文：<https://dev.to/tmns/testing-logout-resolver-graphql-express-session-mbb>

大家好，上周我正在组装一个 GraphQL API，在实现身份验证时，我发现自己在尝试测试我的注销解析器时遇到了困难。问题是注销解析器调用了`session.destroy()`(由`express-session`实现)，但是由于它直接调用解析器，我不确定如何将一个真正的`Session`对象(带有所需的属性/方法，比如`destroy`)传递给解析器，因为这通常是由中间件处理的。

最终，通过查看`express-session`的源代码，我能够弄清楚如何创建一个有效的`Session`对象。最后，我的测试看起来像:

```
import { MemoryStore, Cookie } from "express-session";
[...]
    expect.assertions(2);
    // create user to first authenticate and obtain valid session with
    await User.create({ username: "name", password: "password" });
    var args = {
      input: {
        username: "name",
        password: "password"
      }
    };

    // mock session object building
    var ctx = {
      sessionStore: new MemoryStore()
    };

    var cookie = new Cookie();

    // adds valid session object to ctx
    ctx.sessionStore.createSession(ctx, { cookie });

    // log our test user in...
    await resolvers.Mutation.login(null, args, ctx);
    expect(ctx.session.user).toBeTruthy();

    // ...and log em out
    await resolvers.Mutation.logout(null, null, ctx);
    expect(ctx.session).toBeFalsy();
[...] 
```

Enter fullscreen mode Exit fullscreen mode

然而，这是我第一次自己构建和测试这样的东西——所以我想知道是否有一个已知的/更好的方法来处理这个问题。我搜索了很多，但我找到的例子与其他会话包有关，如`cookie-session`，在 REST API 上使用 GraphQL，以及其他 *close，但不完全是*问题/解决方案。

任何见解将不胜感激！
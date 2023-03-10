# 实际上，复试是好的

> 原文：<https://dev.to/masaeedu/actually-callbacks-are-fine-l4g>

# ...Wat?

[![wat](img/2abe6b7acf44e71b0900e3d4c7cb6ce4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--t0J_Jkgw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/jugRfTk.gif)

这是一篇关于 JS 宇宙中最著名的恶棍并不是真的邪恶，只是被误解了的文章。

# 在呼叫器里下地狱

[![going_to_hell](img/cebf37189124e1e3e6fe3d72fb12657b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K1RkNbYm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/uZPriGg.gif)

“回调地狱”这个名词的背景我就不深究了；相反，我会给你指出[这篇](https://blog.hellojs.org/asynchronous-javascript-from-callback-hell-to-async-and-await-9b9ceb63c8e8)很好的文章，它解释了问题和一些典型的解决方案。如果你不熟悉这个术语，请去读那篇文章；我会等的。

好的。所以我们将从文章中复制并粘贴有问题的代码，然后我们将看看如何在没有使用承诺和异步/等待
的情况下解决问题

```
const verifyUser = function(username, password, callback) {
  dataBase.verifyUser(username, password, (error, userInfo) => {
    if (error) {
      callback(error);
    } else {
      dataBase.getRoles(username, (error, roles) => {
        if (error) {
          callback(error);
        } else {
          dataBase.logAccess(username, error => {
            if (error) {
              callback(error);
            } else {
              callback(null, userInfo, roles);
            }
          });
        }
      });
    }
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

# 展平金字塔

[![flattening_the_pyramid](img/7c811f8f6932a26ac178731b32aa406a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uUs2Y7hQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/LdtOB.gif)

如果我们查看代码，我们会注意到每次执行异步操作时，我们都必须传递一个回调来接收结果。因为我们将所有接收结果的回调内联定义为匿名函数，所以我们最终得到了这个巨大的厄运金字塔。

作为第一步，让我们执行一个简单的重构，将每个匿名回调函数复制并粘贴到一个单独的变量中，引入 curried 参数来显式地传递从周围范围捕获的变量:

```
const verifyUser = (username, password, callback) =>
  dataBase.verifyUser(username, password, f(username, callback));

const f = (username, callback) => (error, userInfo) => {
  if (error) {
    callback(error);
  } else {
    dataBase.getRoles(username, g(username, userInfo, callback));
  }
};

const g = (username, userInfo, callback) => (error, roles) => {
  if (error) {
    callback(error);
  } else {
    dataBase.logAccess(username, h(userInfo, roles, callback));
  }
};

const h = (userInfo, roles, callback) => (error, _) => {
  if (error) {
    callback(error);
  } else {
    callback(null, userInfo, roles);
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果没有其他东西，这肯定是有点恭维，但我们现在有一些新的问题与此代码:

*   这种生意到处都在重复
*   我们中间表达式的变量名是没有意义的
*   `verifyUser`、`f`、`g`和`h`彼此紧密耦合，因为它们直接引用彼此

# 看到图案

[![seeing_the_pattern](img/c8b3964636cb76c3108ecef2c2b07bfe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FdRKJqAA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/qD0UhYv.gif)

在我们处理这些问题之前，让我们注意一下这些表达之间的一些相似之处。

所有这些函数都接受一些数据和一个`callback`参数。`f`、`g`和`h`另外接受一对参数`(error, something)`，其中只有一个是非`null` / `undefined`值。如果`error`不为空，函数会立即将`error`馈送到`callback`并终止。否则，它们使用`something`做更多的工作，导致`callback`最终被输入不同的错误，或者`null`和一些结果值。

记住这些共性，我们将开始一个重构中间表达式的程序，使它们看起来越来越相似。

# 整容变化

我发现`if`语句非常冗长，所以我们现在花点时间用三元表达式替换所有这些`if`语句。因为返回值无论如何都会被丢弃，所以这不会导致代码行为的任何改变。

我还打算通过将重复的`error`和`callback`变量分别缩短为`e`和`cb`:
来减少视觉噪音

```
const verifyUser = (username, password, cb) =>
  dataBase.verifyUser(username, password, f(username, cb));

const f = (username, cb) => (e, userInfo) =>
  e ? cb(e) : dataBase.getRoles(username, g(username, userInfo, cb));

const g = (username, userInfo, cb) => (e, roles) =>
  e ? cb(e) : dataBase.logAccess(username, h(userInfo, roles, cb));

const h = (userInfo, roles, cb) => (e, _) =>
  e ? cb(e) : cb(null, userInfo, roles); 
```

Enter fullscreen mode Exit fullscreen mode

# 大肆逢迎

[![currying_aggressively](img/0afc52ce35d005d3a2081aaf54a7f329.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xM0xVCbg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/aChoznE.gif)

因为我们即将开始用函数参数来做一些严肃的体操，所以我将借此机会来梳理所有的函数参数，这些函数参数*可以被梳理。这引入了一致性，并促进了进一步的重构。*

我们不能轻易地处理接受一对参数`(e, xyz)`的函数，因为底层的`dataBase` API(对我们来说是不透明的)要求回调同时接受一个可能的错误和一个可能的结果。但是所有其他出现的多参数函数都可以(也将会)通过 currying 消除。

我们将从`dataBase`方法开始:

```
// Curried wrapper around the `dataBase` API
const DB = {
  verifyUser: username => password => cb =>
    dataBase.verifyUser(username, password, cb),

  getRoles: username => cb =>
    dataBase.getRoles(username, cb),

  logAccess: username => cb =>
    dataBase.logAccess(username, cb)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们将用来自`DB`的包装操作替换`dataBase`的所有用法，并处理任何剩余的多参数函数。此外，我们将把`h`中的`cb(null, userInfo, roles)`替换为`cb(null, { userInfo, roles })`，这样回调总是接收两个参数:一个可能的错误和一个可能的结果。

```
const verifyUser = username => password => cb =>
  DB.verifyUser(username)(password)(f(username)(cb));

const f = username => cb => (e, userInfo) =>
  e ? cb(e) : DB.getRoles(username)(g(username)(userInfo)(cb));

const g = username => userInfo => cb => (e, roles) =>
  e ? cb(e) : DB.logAccess(username)(h(userInfo)(roles)(cb));

const h = userInfo => roles => cb => (e, _) =>
  e ? cb(e) : cb(null, { userInfo, roles }); 
```

Enter fullscreen mode Exit fullscreen mode

# 把里面翻出来

让我们再做一些重构。出于稍后将变得清楚的原因，我们将把所有的错误检查代码“向外”拉一级。我们将使用一个匿名函数来接收当前步骤的错误`e`或结果`v`，如果没有问题，则将结果和回调转发到下一步:
，而不是每一步都进行自己的错误检查

```
const verifyUser = username => password => cb =>
  DB.verifyUser(username)(password)((e, v) =>
    e ? cb(e) : f(username)(cb)(v)
  );

const f = username => cb => userInfo =>
  DB.getRoles(username)((e, v) =>
    e ? cb(e) : g(username)(userInfo)(cb)(v)
  );

const g = username => userInfo => cb => roles =>
  DB.logAccess(username)((e, _) =>
    e ? cb(e) : h(userInfo)(roles)(cb)
  );

const h = userInfo => roles => cb => cb(null, { userInfo, roles }); 
```

Enter fullscreen mode Exit fullscreen mode

请注意错误处理是如何从我们的最终函数:`h`中完全消失的。它只是接受几个参数，从这些参数中构建一些复合结果，然后立即返回并将结果输入给定的回调。让我们重写`h`来更清楚地展示这一点:

```
const h = userInfo => roles => {
  const result = { userInfo, roles };

  return cb => cb(null, result);
} 
```

Enter fullscreen mode Exit fullscreen mode

`cb`参数现在在不同的位置被传递，所以为了一致性，我们将围绕参数移动，这样所有的数据首先传递，回调最后传递:

```
const verifyUser = username => password => cb =>
  DB.verifyUser(username)(password)((e, v) =>
    e ? cb(e) : f(username)(v)(cb)
  );

const f = username => userInfo => cb =>
  DB.getRoles(username)((e, v) =>
    e ? cb(e) : g(username)(userInfo)(v)(cb)
  );

const g = username => userInfo => roles => cb =>
  DB.logAccess(username)((e, _) =>
    e ? cb(e) : h(userInfo)(roles)(cb)
  );

const h = userInfo => roles => {
  const result = { userInfo, roles };

  return cb => cb(null, result);
} 
```

Enter fullscreen mode Exit fullscreen mode

`verifyUser`和`f`现在看起来几乎一模一样。他们都:

*   接收一些数据和一个回调
*   执行一些异步操作
*   接收错误或值
*   如果结果是错误，立即将其传递给回调
*   否则，传递成功的结果并回调到下一步(`<next step>(v)(cb)`)

`g`很相似，但有一个转折。如果没有问题，它不是接收一个`v`参数并将其传递给下一步，而是无条件地丢弃任何成功的结果，只将回调传递给下一步。

为了消除这个问题，我们将重写`g`,以便它模仿其他两个函数并传递它的(未定义的)结果。为了处理不想要的结果，我们将在“下一步”中引入一个伪参数，这样它将丢弃已经传递的内容:

```
const g = username => userInfo => roles => cb =>
  DB.logAccess(username)((e, v) =>
    e ? cb(e) : (_ => h(userInfo)(roles))(v)(cb) // the "next step" discards the result
  ); 
```

Enter fullscreen mode Exit fullscreen mode

现在它遵循与`verifyUser`和`f`相同的公式。为了清楚起见，我们把异步操作和每个函数的“下一步”显式复制到局部变量:

```
const verifyUser = username => password => {
  const task = DB.verifyUser(username)(password);
  const next = f(username);

  return cb => task((e, v) => e ? cb(e) : next(v)(cb));
}

const f = username => userInfo => {
  const task = DB.getRoles(username);
  const next = g(username)(userInfo);

  return cb => task((e, v) => e ? cb(e) : next(v)(cb));
}

const g = username => userInfo => roles => {
  const task = DB.logAccess(username);
  const next = _ => h(userInfo)(roles);

  return cb => task((e, v) => e ? cb(e) : next(v)(cb));
}

const h = userInfo => roles => {
  const result = { userInfo, roles };

  return cb => cb(null, result);
} 
```

Enter fullscreen mode Exit fullscreen mode

你看到模式了吗？

# 分解出模式

在这一点上，很明显，有一些非常重复的事情正在发生。看起来有人复制并粘贴了处理错误的代码，并将回调线程化到每个函数中。当然，这是故意的；我们已经重构到一个统一的模式，这样我们可以复制并粘贴重复的*到*。

现在，我们可以一举将所有的错误处理和回调线程业务移到一对助手函数中:

```
const after = task => next =>
  cb => task((e, v) => e ? cb(e) : next(v)(cb));

const succeed = v =>
  cb => cb(null, v); 
```

Enter fullscreen mode Exit fullscreen mode

我们的脚步变成:

```
const verifyUser = username => password =>
  after
    (DB.verifyUser(username)(password))
    (f(username));

const f = username => userInfo =>
  after
    (DB.getRoles(username))
    (g(username)(userInfo));

const g = username => userInfo => roles =>
  after
    (DB.logAccess(username))
    (_ => h(userInfo)(roles));

const h = userInfo => roles =>
  succeed({ userInfo, roles }); 
```

Enter fullscreen mode Exit fullscreen mode

错误处理和回调线程已经消失了！

在这里停一下是个好主意。尝试将`after`和`succeed`的定义嵌入到这些新表达式中，让自己相信它们与我们重构掉的表达式是等价的。

好了，我们越来越热了！然而，`f`、`g`和`h`似乎不再做什么了...

# 修剪自重

[![pruning_dead_weight](img/e406535bcbb49c73f20e82506aa6e199.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L_iaKZC3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/egYE4gy.gif)

...所以让我们摆脱他们！我们所要做的就是从`h`开始向后工作，将每个函数内联到引用它的定义中:

```
// Inline h into g
const g = username => userInfo => roles =>
  after(DB.logAccess(username))(_ =>
    succeed({ userInfo, roles })
  ); 
```

Enter fullscreen mode Exit fullscreen mode

```
// Inline g into f
const f = username => userInfo =>
  after(DB.getRoles(username))(roles =>
    after(DB.logAccess(username))(_ =>
      succeed({ userInfo, roles })
    )
  ); 
```

Enter fullscreen mode Exit fullscreen mode

```
// Inline f into verifyUser
const verifyUser = username => password =>
  after(DB.verifyUser(username)(password))(userInfo =>
    after(DB.getRoles(username))(roles =>
      after(DB.logAccess(username))(_ =>
        succeed({ userInfo, roles })
      )
    )
  ); 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用[引用透明性](https://en.wikipedia.org/wiki/Referential_transparency)来引入一些临时变量，使其更具可读性:

```
const verifyUser = username => password => {
  const auth  = DB.verifyUser(username)(password);
  const roles = DB.getRoles(username);
  const log   = DB.logAccess(username);

  return after(auth)(u =>
    after(roles)(r =>
      after(log)(_ =>
        succeed({ userInfo: u, roles: r })
      )
    )
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

现在你知道了！这非常简洁，没有重复任何错误检查，并且大致类似于我们之前链接的文章中的`Promise`版本。你这样调用`verifyUser`:

```
const main = verifyUser("someusername")("somepassword");
main((e, o) => (e ? console.error(e) : console.log(o))); 
```

Enter fullscreen mode Exit fullscreen mode

# 最终代码

```
// Tools for sequencing callback APIs
const after = task => next =>
  cb => task((e, v) => e ? cb(e) : next(v)(cb));

const succeed = v =>
  cb => cb(null, v);

// Curried wrapper around the `dataBase` API
const DB = {
  verifyUser: username => password => cb =>
    dataBase.verifyUser(username, password, cb),

  getRoles: username => cb =>
    dataBase.getRoles(username, cb),

  logAccess: username => cb =>
    dataBase.logAccess(username, cb)
}

// Our implementation
const verifyUser = username => password => {
  const auth  = DB.verifyUser(username)(password);
  const roles = DB.getRoles(username);
  const log   = DB.logAccess(username);

  return after(auth)(u =>
    after(roles)(r =>
      after(log)(_ =>
        succeed({ userInfo: u, roles: r })
      )
    )
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

# M 字

[![the_m_word](img/5ec3b6593ee38645a84b25db43482796.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gxIUrZXR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/2KZm9I3.gif)

完事了吗？嗯，我们中的一些人可能仍然觉得`verifyUser`中的代码有点太过*三角形*。有很多方法可以解决这个问题，但是为了解释我首先必须承认一些事情。

我在重构这段代码的过程中没有独立发现`after`和`succeed`的定义。我实际上已经有了定义，因为我是从 Haskell 库中复制的，在那里它们被命名为`>>=`和`pure`。这两个函数共同构成了[【延续单子】](http://blog.sigfpe.com/2008/12/mother-of-all-monads.html)的定义。

这有什么关系？事实证明，有许多简便的方法可以将一元计算排序在一起，而不会受到末日金字塔效应的影响。

为了举例说明，让我们从稍微不同地格式化`verifyUser`的定义开始:

```
const verifyUser = username => password => {
  const auth  = DB.verifyUser(username)(password);
  const roles = DB.getRoles(username);
  const log   = DB.logAccess(username);

  return
    after(auth) (u =>
    after(roles)(r =>
    after(log)  (_ =>
    succeed({ userInfo: u, roles: r }))));
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果您眯着眼睛，忽略括号，您可能会注意到这个定义和下面的 Haskell 函数之间的相似之处:

```
-- In Haskell, function application does not require parentheses,
-- and binary functions may be applied infix
verifyUser :: Username -> Password -> IO (UserInfo, Roles)
verifyUser username password =
  let
    auth  = DB.verifyUser username password
    roles = DB.getRoles username
    log   = DB.logAccess username
  in
    auth  >>= \u ->
    roles >>= \r ->
    log   >>= \_ ->
    pure (u, r) 
```

Enter fullscreen mode Exit fullscreen mode

这种使用`>>=`和函数引入从一元计算的步骤中捕获的新变量的模式非常常见，因此有一种特殊的语法糖，称为“do-notation”。下面是用 do 符号编写的 Haskell 中的相同计算:

```
verifyUser' :: Username -> Password -> IO (UserInfo, Roles)
verifyUser' username password =
  let
    auth  = DB.verifyUser username password
    roles = DB.getRoles username
    log   = DB.logAccess username
  in
    do
      u <- auth
      r <- roles
      _ <- log
      pure (u, r) 
```

Enter fullscreen mode Exit fullscreen mode

尽管我们在 JS 中没有通用的 do 符号(也许我们应该有！)，有各种方法模拟。单子和 do-notation 的详细解释超出了本文的范围，但是出于说明的目的，这里有一种用模拟 do-notation 库用 JS 编写`verifyUser`的方法:

```
const { mdo } = require("@masaeedu/do");

// `Cont` is our implementation of the continuation monad
const Cont = monad({ pure: succeed, bind: after });

const verifyUser = username => password => {
  const auth  = DB.verifyUser(username)(password);
  const roles = DB.getRoles(username);
  const log   = DB.logAccess(username);

  return mdo(Cont)(({ u, r }) => [
    [u, () => auth ],
    [r, () => roles],
        () => log   ,
        () => Cont.pure({ userInfo: u, roles: r })
  ]);
}; 
```

Enter fullscreen mode Exit fullscreen mode

这很好，但也值得注意的是，一些一元计算有一个“固定”的结构，即它们可能不会利用前面步骤的结果来决定下一步做什么。由于此类计算并不真正需要显式绑定和命名中间步骤的结果，因此可以通过“遍历”步骤的固定容器来更方便地构建它们，这将最终产生相应的结果容器。

幸运的是，我们的例子就是这样一个“固定结构”计算，因为每一步都独立于前一步的结果。这意味着它也可以用更简洁的方式写成:

```
const verifyUser = username => password => {
  const auth  = DB.verifyUser(username)(password);
  const roles = DB.getRoles(username);
  const log   = DB.logAccess(username);

  // Applicative lifting
  const f = u => r => _ => ({ userInfo: u, roles: r });
  return Cont.lift(f)([auth, roles, log]);
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
const verifyUser = username => password => {
  const auth  = DB.verifyUser(username)(password);
  const roles = DB.getRoles(username);
  const log   = DB.logAccess(username);

  // Traverse a dictionary of continuations into a continuation of a dictionary
  return Obj.sequence(Cont)({
    userInfo: auth,
    roles: roles,
    _: log
  })
}; 
```

Enter fullscreen mode Exit fullscreen mode

对构建一元计算和应用计算的所有方法的详细分析超出了本文的范围，但可以说，有许多强大而优雅的工具可以在任意一元中合成计算。通过认识到我们的基于回调的异步模型是一元的(具体来说，它对应于延续一元)并见证相关的一元操作，我们可以将这些通用工具应用于异步编程。

# 结论

好的，我们成功了！有哪些外卖？我希望我已经说服了你以下几点:

*   引用透明重构是消除重复和发现有用模式的强大技术
*   “回调地狱”不是回调固有的问题，而是基于回调的 API 的特定调用规则的问题。使用正确的方法，基于回调的 API 可以简洁优雅地使用
*   编程环境中的“单子”概念并不(仅仅)是学术上的晦涩难懂，而是识别和利用日常编程中自然出现的模式的有用工具

# 进一步工作

我故意避免引入类型签名或单子之类的概念，直到文章的最后，以保持事物的可接近性。也许在未来的文章中，我们可以重新推导出这种抽象，首先考虑单子和单子转换者的概念，并特别注意类型和法则。

# 鸣谢

非常感谢 [@jlavelle](https://github.com/jlavelle/) 、 [@mvaldesdeleon](https://github.com/mvaldesdeleon) 和 [@gabejohnson](https://github.com/gabejohnson) 对本文提供反馈和建议。*
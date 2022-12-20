# 揭开依赖注入、控制反转、服务容器和服务提供者的神秘面纱

> 原文：<https://dev.to/michi/demystifying-dependency-injection-inversion-of-control-service-containers-and-service-providers-5e72>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/demystifying-dependency-injection)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

本文旨在揭开 DI 和 IoC 这两个可怕术语的神秘面纱。我们将在节点环境中对此进行编码。
想象有下面的代码

```
// index.js

class Database {
    insert(table, attributes) {
        // inserts record in database
        // ...

        const isSuccessful = true
        return isSuccessful
    }
}

class UserService {
    create(user) {
        // do a lot of validation etc.
        // ...

        const db = new Database
        return db.insert('users', user)
    }
}

const userService = new UserService
const result = userService.create({ id: 1})
console.log(result) 
```

Enter fullscreen mode Exit fullscreen mode

运行`node index.js`现在应该记录值“true”。

代码中发生了什么？有一个数据库类用于将内容保存到数据库中，还有一个 UserService 类用于创建用户。用户将被保存在数据库中，所以当我们创建一个新用户时，我们创建了一个数据库实例。换句话说，用户服务依赖于数据库。或者说，数据库是 UserService 的依赖项。

问题来了。如果我们编写测试来检查零件`// do a lot of validation etc.`会怎么样。我们需要为各种场景编写总共 10 个测试。在所有这些测试中，我们真的想要将用户插入数据库吗？我不这么认为。我们甚至不关心这部分代码。因此，如果在运行测试时可以用一个假的数据库替换掉这个数据库，那就太好了。

# 依赖注入

进入依赖注入。听起来很花里胡哨，但实际上超级简单。我们没有在“create”方法中更新数据库实例，而是像这样将它注入到用户服务中。

```
class Database {
    insert(table, attributes) {
        // inserts record in database
        const isSuccessful = true
        return isSuccessful
    }
}

class UserService {
    constructor(db) {
        this.db = db
    }

    create(user) {
        return this.db.insert('users', user)
    }
}

const db = new Database
const userService = new UserService(db)

const result = userService.create({ id: 1})
console.log(result) 
```

Enter fullscreen mode Exit fullscreen mode

测试可能看起来像这样

```
 class TestableDatabase {
    insert() {
        return true
    }
}

const db = new TestableDatabase
const userService = new UserService(db) 
```

Enter fullscreen mode Exit fullscreen mode

但是当然，我听到你说的了。虽然我们使代码可测试，但 API 却深受其害。总是传入数据库的一个实例很烦人。

# 反转控制

进入控制反转。它的工作是为你解决依赖性。

它看起来是这样的:在应用程序的开始，你将实例化绑定到一个键，并在以后的任何时候使用它。

在我们检查我们的 IoC 容器(也称为**服务容器**)的代码之前，让我们先看一下用法。

```
ioc.bind('userService', () => new UserService(new Database)) 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以在应用程序中的任何位置使用 ioc.use 来访问用户服务。

```
ioc.use('userService').create({ id: 1}) 
```

Enter fullscreen mode Exit fullscreen mode

每当你调用`ioc.use('userService')`，它就会创建一个新的 UserService 实例，基本上就是执行第二个函数的回调。如果您喜欢总是访问同一个实例，请使用 app.singleton 而不是 app.bind.

```
ioc.singleton('userService', () => new UserService(new Database))

ioc.use('userService').create({ id: 1}) 
```

Enter fullscreen mode Exit fullscreen mode

## 实现 ioc

```
global.ioc = {
    container: new Map,
    bind(key, callback) {
        this.container.set(key, {callback, singleton: false})
    },
    singleton(key, callback) {
        this.container.set(key, {callback, singleton: true})
    },
    use(key) {
        const item = this.container.get(key)

        if (!item) {
            throw new Error('item not in ioc container')
        }

        if (item.singleton && !item.instance) {
            item.instance = item.callback()
        }

        return item.singleton ? item.instance : item.callback()
    },
} 
```

Enter fullscreen mode Exit fullscreen mode

这根本不是很多代码！
所以方法`bind`和`singleton`只是将键和回调存储在一个 map 中，而使用`use`方法，我们再次从容器中获取我们想要的东西。
我们还把`ioc`变成了一个全局变量，所以它可以从任何地方访问。

但是，我们把所有这些国际奥委会的绑定放在哪里呢？

## 服务商

输入服务提供商。另一个有趣的术语，意思仅仅是“这是我们在服务容器中绑定我们的东西的地方”。这可以简单到拥有

```
// providers/AppProvider.js

function register() {
    ioc.singleton('userService', () => new UserService(new Database))
}

module.exports = { register } 
```

Enter fullscreen mode Exit fullscreen mode

然后在应用程序启动时简单地执行提供者的注册方法。

## 测试

我们现在如何测试它？

在我们的测试中，我们可以简单地覆盖服务容器中的 userService。

```
 class TestableDatabase {
    create() {
        return true
    }
}

ioc.singleton('userService', () => new UserService(new TestableDatabase))

ioc.use('userService').create({id: 1}) 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的，但是有一个问题，如果您的测试需要用户服务中的实际数据库，这些测试现在也可能接收 TeastableDatabase。让我们在 ioc 对象上创建一个`fake`和`restore`方法。我们也必须稍微改变一下我们的`use`方法

```
global.ioc = {
    container: new Map,
    fakes: new Map,
    bind(key, callback) {
        this.container.set(key, {callback, singleton: false})
    },
    singleton(key, callback) {
        this.container.set(key, {callback, singleton: true})
    },
    fake(key, callback) {
        const item = this.container.get(key)

        if (!item) {
            throw new Error('item not in ioc container')
        }

        this.fakes.set(key, {callback, singleton: item.singleton})
    },
    restore(key) {
        this.fakes.delete(key)
    },
    use(key) {
        let item = this.container.get(key)

        if (!item) {
            throw new Error('item not in ioc container')
        }

        if (this.fakes.has(key)) {
            item = this.fakes.get(key)
        }

        if (item.singleton && !item.instance) {
            item.instance = item.callback()
        }

        return item.singleton ? item.instance : item.callback()
    },
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们更新我们的测试

```
 class TestableDatabase {
    insert() {
        return true
    }
}

ioc.fake('userService', () => new UserService(new TestableDatabase))

ioc.use('userService').create({id: 1})

ioc.restore('userService') 
```

Enter fullscreen mode Exit fullscreen mode

## 其他用例

### 避免无用的抽象

这个例子摘自阿多尼斯文档。

有些对象你想实例化一次，然后重复使用。你通常用一个单独的文件来处理单例。

```
const knex = require('knex')

const connection = knex({
  client: 'mysql',
  connection: {}
})

module.exports = connection 
```

Enter fullscreen mode Exit fullscreen mode

有了 IoC 容器，这种抽象就没有必要了，从而使代码库更加整洁。

### 避免相对要求

假设你在文件`app/controllers/auth/UserController.js`的深处，想要得到文件`app/apis/GitHub.js`。你平时是怎么做到的？

```
const GitHub = require('../../apis/GitHub') 
```

Enter fullscreen mode Exit fullscreen mode

不如我们将它添加到服务容器中？

```
// providers/AppProvider.js

ioc.bind('API/GitHub', () => require('../app/apis/GitHub') 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以在任何地方像这样使用它

```
ioc.use('API/GitHub') 
```

Enter fullscreen mode Exit fullscreen mode

因为对每个文件都这样做很烦人，所以让我们简单地添加一个方法来要求来自根目录的文件。

将下面的代码添加到`ioc.use`方法的末尾，并删除找不到键时的异常抛出。

```
global.ioc = {
// ...
    use(key) {
        // ...
        return require(path.join(rootPath, namespace))
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以使用
访问 GitHub 服务

```
ioc.use('apis/GitHub') 
```

Enter fullscreen mode Exit fullscreen mode

但是，ioc 容器必须位于目录的根目录中。让我们把 IoC 容器提取出来，用它做一个工厂。最终结果是

```
//lib/ioc.js

module.exports = function createIoC(rootPath) {
    return {
        container: new Map,
        fakes: new Map,
        bind(key, callback) {
            this.container.set(key, {callback, singleton: false})
        },
        singleton(key, callback) {
            this.container.set(key, {callback, singleton: true})
        },
        fake(key, callback) {
            const item = this.container.get(key)

            if (!item) {
                throw new Error('item not in ioc container')
            }

            this.fakes.set(key, {callback, singleton: item.singleton})
        },
        restore(key) {
            this.fakes.delete(key)
        },
        use(namespace) {
            let item = this.container.get(namespace)

            if (item) {
                if (this.fakes.has(namespace)) {
                    item = this.fakes.get(namespace)
                }

                if (item.singleton && !item.instance) {
                    item.instance = item.callback()
                }

                return item.singleton ? item.instance : item.callback()
            }

            return require(path.join(rootPath, namespace))
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们将对象包装在函数`createIoC`中，该函数期望传入根路径。“require”方法现在返回下面的`return require(rootPath + '/' + path)`。

在`index.js`中，我们现在必须创建这样的容器

```
global.ioc = require('./lib/ioc')(__dirname) 
```

Enter fullscreen mode Exit fullscreen mode

* * *

这就是国际奥委会的基本知识！我把代码放在了 GitHub 上，你可以在那里再次查看。我还添加了一些测试，并使得伪造根需求成为可能。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)/[IOC-node](https://github.com/MZanggl/ioc-node)

### 节点的控制容器反转

<article class="markdown-body entry-content container-lg" itemprop="text">

> 节点的零依赖性 IOC 容器

# 装置

> `npm install ioc-node`

# 例示

```
// index.js
global.ioc = require('ioc-node')(__dirname)
```

Enter fullscreen mode Exit fullscreen mode

## 使用

想象一下下面的类

```
class UserService {
    constructor(database) {
        this.database = database
    }

    create(data) {
       this.database.create('user', data)
    }
}
```

Enter fullscreen mode Exit fullscreen mode

您可以使用以下方法注入依赖项

```
ioc.bind('userService', () => new UserService(new Database))
```

Enter fullscreen mode Exit fullscreen mode

并在以后利用与

```
ioc.use('userService').create({ id: 1})
```

Enter fullscreen mode Exit fullscreen mode

如果你不想每次使用`ioc.use`时都创建一个新的实例，用`ioc.singleton`而不是`ioc.bind`创建绑定。

```
ioc.singleton('userService', () => new UserService(new Database))
ioc.use('userService')
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/MZanggl/ioc-node)

* * *

如果这篇文章对你有所帮助，我有更多关于简化编写软件的技巧[在这里](https://michaelzanggl.gumroad.com/l/intent-driven-development)。
# JavaScript 中的自动依赖注入

> 原文：<https://dev.to/michi/automatic-dependency-injection-in-javascript-gii>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/automatic-dependency-injection-in-javascript)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

在本系列的上一篇文章中，我们通过创建与`ioc.bind`和`ioc.singleton`的绑定来实现我们自己的 ioc 容器。
但是这种设置可能有点麻烦。这就是为什么许多框架也带有自动依赖注入。

Laravel 能够做到这一点要感谢 PHP 的类型提示机制

```
public function __construct(UserRepository $users)
{
    $this->users = $users;
} 
```

Enter fullscreen mode Exit fullscreen mode

Angular 使用了 [TypeScript 的 emitedecoraemetadata](http://nicholasjohnson.com/blog/how-angular2-di-works-with-typescript/)。

```
class Pterodactyls {}

@Component({...})
class Park {
    constructor(x: Pterodactyls, y: string) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

但是这些奢侈品不是普通的 JavaScript。因此，在本文中，我们将以类似于在 MVC 框架 Adonis.js 上完成[的方式实现自动注入。](https://adonisjs.com/)

你可以在与上一篇文章相同的 [GitHub](https://github.com/MZanggl/ioc-node) 上找到完整的代码。

我们从上次的代码(稍微改进的版本)开始:

```
module.exports = function createIoC(rootPath) {
    return {
        _container: new Map,
        _fakes: new Map,
        bind(key, callback) {
            this._container.set(key, {callback, singleton: false})
        },
        singleton(key, callback) {
            this._container.set(key, {callback, singleton: true})
        },
        fake(key, callback) {
            const item = this._container.get(key)
            this._fakes.set(key, {callback, singleton: item ? item.singleton : false})
        },
        restore(key) {
            this._fakes.delete(key)
        },
        _findInContainer(namespace) {
            if (this._fakes.has(namespace)) {
                return this._fakes.get(namespace)
            }

            return this._container.get(namespace)
        },
        use(namespace) {
            const item = this._findInContainer(namespace)

            if (item) {
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

这个想法是为了避免手工创建类，而是使用一个新的方法`ioc.make`。让我们写一个我们能想到的最简单的测试。

```
describe('auto injection', function() {
    it('can new up classes', function() {
        const SimpleClass = ioc.use('test/modules/SimpleClass')
        const test = ioc.make(SimpleClass)
        expect(test).to.be.instanceOf(SimpleClass)
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

而`SimpleClass`看起来是这样的

```
// test/modules/SimpleClass.js

class SimpleClass {}

module.exports = SimpleClass 
```

Enter fullscreen mode Exit fullscreen mode

运行测试应该会失败，因为我们还没有实现`ioc.make`。让我们在`index.js`
实施吧

```
const ioc = {
    // ...
    make(object) {
        return new object
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

测试通过！
但是总要先做`ioc.use`再做`ioc.make`才能上新的课，有点烦人。因此，让我们将一个字符串传递到`ioc.make`中，这将解决内部的依赖性。

新的测试！

```
it('can make classes using the filepath instead of the class declaration', function() {
    const test = ioc.make('test/modules/SimpleClass')
    expect(test).to.be.instanceOf(ioc.use('test/modules/SimpleClass'))
}) 
```

Enter fullscreen mode Exit fullscreen mode

并且`ioc.make`变成了

```
if (typeof object === 'string') {
    object = this.use(object)
}

return new object 
```

Enter fullscreen mode Exit fullscreen mode

不错！有了这个，我们已经可以上新课了。最棒的是，它们是可伪造的，因为`ioc.use`首先查看我们可以用`ioc.fake`填充的假容器。

解决了这个问题，让我们来构建自动注入机制。测试:

```
it('should auto inject classes found in static inject', function() {
        const injectsSimpleClass = ioc.make('test/modules/InjectsSimpleClass')

        expect( injectsSimpleClass.simpleClass ).to.be.instanceOf( ioc.use('test/modules/SimpleClass') )
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们必须创建类`InjectsSimpleClass.js`

```
// test/modules/InjectsSimpleClass.js

class InjectsSimpleClass {
    static get inject() {
        return ['test/modules/SimpleClass']
    }

    constructor(simpleClass) {
        this.simpleClass = simpleClass
    }
}

module.exports = InjectsSimpleClass 
```

Enter fullscreen mode Exit fullscreen mode

这个想法是我们静态地定义所有需要注入的类。这些问题将由 ioc 容器解决，并进行更新。

`ioc.make`会变成:

```
if (typeof object === 'string') {
    object = this.use(object)
}

// if the object does not have a static inject property, let's just new up the class
if (!Array.isArray(object.inject)) {
    return new object
}

// resolve everything that needs to be injected
const dependencies = object.inject.map(path => {
    const classDeclaration = this.use(path)
    return new classDeclaration
})

return new object(...dependencies) 
```

Enter fullscreen mode Exit fullscreen mode

还不错。但是`return new classDeclaration`似乎有些不对劲...如果这个注入的类也有依赖项需要解决呢？这听起来像是递归的经典案例！让我们用一个新的测试来尝试一下。

```
it('should auto inject recursively', function() {
    const recursiveInjection = ioc.make('test/modules/RecursiveInjection')
    expect(recursiveInjection.injectsSimpleClass.simpleClass).to.be.instanceOf(
            ioc.use('test/modules/SimpleClass')
        )
    }) 
```

Enter fullscreen mode Exit fullscreen mode

我们必须创建一个新文件来帮助我们进行测试。

```
// test/modules/RecursiveInjection.js

class RecursiveInjection {

    static get inject() {
        return ['test/modules/InjectsSimpleClass']
    }

    constructor(injectsSimpleClass) {
        this.injectsSimpleClass = injectsSimpleClass
    }
}

module.exports = RecursiveInjection 
```

Enter fullscreen mode Exit fullscreen mode

测试将会失败，显示`AssertionError: expected undefined to be an instance of SimpleClass`。我们所要做的就是关掉

```
const dependencies = object.inject.map(path => {
    const classDeclaration = this.use(path)
    return new classDeclaration
}) 
```

Enter fullscreen mode Exit fullscreen mode

用

```
const dependencies = object.inject.map(path => this.make(path)) 
```

Enter fullscreen mode Exit fullscreen mode

总之，`make`方法看起来像这样

```
if (typeof object === 'string') {
    object = this.use(object)
}

// if the object does not have a static inject property, let's just new up the class
if (!Array.isArray(object.inject)) {
    return new object
}

// resolve everything that needs to be injected
const dependencies = object.inject.map(path => this.make(path))

return new object(...dependencies) 
```

Enter fullscreen mode Exit fullscreen mode

差不多就是这样了！repo 中的版本处理更多的事情，比如不创建非类，能够传递额外的参数，别名等等。但这应该涵盖了自动注射的基础知识。令人惊讶的是，实现这一点只需要很少的代码。
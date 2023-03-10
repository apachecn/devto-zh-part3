# 为节点和浏览器编写自己的可链接事件发射器类

> 原文：<https://dev.to/jochemstoel/write-your-own-chainable-event-emitter-class-for-node-and-browser-5gp>

这是星期六的早上，我还在等咖啡，所以让我们做一些琐碎的事情来温暖这一天。

## 为节点和浏览器编写自己的可链接事件发射器类

JavaScript 中的事件发射器通常有三种方法。

### addEventListener

添加/注册将在调度事件时调用的侦听器。

### removeEventListener

删除/注销事件侦听器。

### dispatchEvent

该方法用于*触发*某种类型的事件。

## 上课

首先，我们确保发射器是实例化的，而不是作为函数调用的。

```
function Emitter() {
    if (!(this instanceof Emitter)) throw new TypeError('Emitter is not a function.')
    // ...
} 
```

声明一个私有变量来存储侦听器。这个数组将被更多的数组填充，其中 array[0]是事件类型，array[1]是回调函数。

```
/**
 * Store event handlers here.
 * @type {Array}
 * @private
 */
let handlers = [] 
```

### addEventListener

该方法将通过向*处理程序*添加一个数组类型的项目来为所提供类型的事件添加/注册一个新的事件侦听器，其中 array[0]是类型，array[1]是回调。

```
/**
 * Add event listener.
 * @param {string} event type 
 * @param {function} callback function
 */
this.addEventListener = (type, fn) => {
    handlers.push([type, fn])
} 
```

实例化后，您可以如下调用 addevent listener:

```
emitter.addEventListener('message', message => console.log('received a message!', message)) 
```

### removeEventListener

我们还需要能够删除我们不再需要的事件侦听器。为此，我们需要删除*处理程序*中的所有条目，其中条目[0]是事件类型。

```
/**
 * Remove event listener.
 * @param {string} event type 
 * @param {function} callback function
 */
this.removeEventListener = (type, fn = true) => {
    handlers = handlers.filter(handler => !(handler[0] == type && (fn == true ? true : handler[1] == fn)))
} 
```

```
emitter.addEventListener('ready', console.log) // console.log will be called when a ready event happens
emitter.removeEventListener('ready', console.log)  // console.log will no longer be called on ready events 
```

### dispatchEvent

触发事件的方法在浏览器中称为 *dispatchEvent* 。在节点环境中，它通常被称为*发射*。我们将稍微修改这个函数，使其支持通配符事件类型(如[https://www.npmjs.com/package/eventemitter2](https://dev.toEventEmitter2)所示)。此外，除了事件数据之外，第二个参数 *type* 被赋予事件处理程序。当您实现通配符事件类型支持时，该参数有助于确定什么是什么。

```
// without the type argument, this event could be anything
emitter.addEventListener('*', (event, type) => console.log(`an event of type = ${type} was emitted.`))
emitter.addEventListener('user:*', (event, type) => console.log(`something usery happened.`)) 
```

```
/**
 * Dispatch event.
 * @param {string} event type 
 * @param {any} event data
 */
 this.dispatchEvent = (type, data) => {
    handlers.filter(handler => new RegExp("^" + handler[0].split("*").join(".*") + "$").test(type)).forEach(handler => handler[1](data, type))
 } 
```

### getEventListeners

也许您希望能够获得/列出所有事件侦听器(某种类型)。

```
/**
 * Get list of event handlers (of a type) or all if type is not specified
 * @param {string} [event type] (optional) 
 */
 this.getEventListeners = type => {
    if (!type)
        return handlers
    let fns = []
    handlers.filter(handler => handler[0] == type).forEach(handler => fns.push(handler[1]))

    return fns
} 
```

### clearEventListeners

让我们添加这个额外的方法，通过重新初始化*处理程序*来清除所有事件监听器。

```
/**
 * Clear event listeners
 * @param {string} [event type] (optional)
 */
    this.clearEventListeners = () => { handlers = [] } 
```

## 至此

我们的发射器类现在看起来像这样。

```
function Emitter() {
    if (!(this instanceof Emitter)) throw new TypeError('Emitter is not a function.')

    let handlers = []

    this.addEventListener = (type, fn) => {
        handlers.push([type, fn])
    }

    this.removeEventListener = (type, fn = true) => {
        handlers = handlers.filter(handler => !(handler[0] == type && (fn == true ? true : handler[1] == fn)))
    }

    this.dispatchEvent = (type, data) => {
        handlers.filter(handler => new RegExp("^" + handler[0].split("*").join(".*") + "$").test(type)).forEach(handler => handler[1](data, type))
    }

    this.clearEventListeners = () => { handlers = [] }

    this.getEventListeners = type => {
        if (!type)
            return handlers

        let fns = []
        handlers.filter(handler => handler[0] == type).forEach(handler => fns.push(handler[1]))

        return fns
    }
} 
```

恭喜你！您有一个工作事件发射器类。自己试试:

```
var emitter = new Emitter()
emitter.addEventListener('ready', console.log)
emitter.addEventListener('foo.*', (event, type) => console.log({type,event}))
emitter.dispatchEvent('ready', Date.now())
emitter.dispatchEvent('foo.bar', 'blabalbla')
emitter.removeEventListener('ready', console.log)
emitter.clearEventListeners() 
```

但我们还没有完成，我承诺了一个可链接的事件发射器。可链接意味着发射器是一个总是返回自身的单例，允许您继续调用它的方法。

## 快捷键

因为我们不喜欢一直写 *addEventListener* 和 *dispatchEvent* ，所以就加上这些快捷键吧。这些快捷方式都返回*这个*的末尾来制作外链。

```
 /**
     * Shortcut for addEventListener.
     * @param {string} event type 
     * @param {function} callback function 
     */
    this.on = (type, fn) => {
        this.addEventListener(type, fn)
        return this /* chain */
    }

    /**
     * Shortcut for removeEventListener
     * @param {string} event type 
     * @param {function} callback function 
     */
    this.off = (type, fn) => {
        this.removeEventListener(type, fn)
        return this /* chain */
    }

    /**
     * Shortcut for dispatchEvent
     * @param {string} event type 
     * @param {any} event data
     */
    this.emit = (type, data) => {
        this.dispatchEvent(type, data)
        return this /* chain */
    }

     /**
     * Shortcut for clearEventListeners
     * @param {string} event type 
     */
    this.clear = type => {
        this.clearEventListeners(type)
        return this
    }

    /**
     * 
     * @param {string} [type]
     */
    this.list = type => this.getEventListeners(type) 
```

现在我们的事件发射器类可以像这样访问:

```
emitter.on('message', message => console.log(message).on('open', onOpen).on('error', console.error).emit('ready', { status: 200, details: 'this is a ready event'}) 
```

## 最终结果:类发射器

你最终的发射器类应该是这样的:

```
/**
 * Simpler EventTarget class without the need to dispatch Event instances.
 * @constructor
 * @returns {Emitter} new instance of Emitter
 */
function Emitter() {
    if (!(this instanceof Emitter)) throw new TypeError('Emitter is not a function.')

    /**
     * Store event handlers here.
     * @type {Array}
     * @private
     */
    let handlers = []

    /**
     * Add event listener.
     * @param {string} event type 
     * @param {function} callback function
     */
    this.addEventListener = (type, fn) => {
        handlers.push([type, fn])
    }

    /**
     * Remove event listener.
     * @param {string} event type 
     * @param {function} callback function
     */
    this.removeEventListener = (type, fn = true) => {
        handlers = handlers.filter(handler => !(handler[0] == type && (fn == true ? true : handler[1] == fn)))
    }

    /**
     * Dispatch event.
     * @param {string} event type 
     * @param {any} event data
     */
    this.dispatchEvent = (type, data) => {
        handlers.filter(handler => new RegExp("^" + handler[0].split("*").join(".*") + "$").test(type)).forEach(handler => handler[1](data, type))
    }

    /**
     * Clear event listeners
     * @param {string} [event type] (optional)
     */
    this.clearEventListeners = () => { handlers = [] }

    /**
     * Get list of event handlers (of a type) or all if type is not specified
     * @param {string} [event type] (optional) 
     */
    this.getEventListeners = type => {
        if (!type)
            return handlers

        let fns = []
        handlers.filter(handler => handler[0] == type).forEach(handler => fns.push(handler[1]))

        return fns
    }

    /**
     * Shortcut for addEventListener.
     * @param {string} event type 
     * @param {function} callback function 
     */
    this.on = (type, fn) => {
        this.addEventListener(type, fn)
        return this /* chain */
    }

    /**
     * Shortcut for removeEventListener
     * @param {string} event type 
     * @param {function} callback function 
     */
    this.off = (type, fn) => {
        this.removeEventListener(type, fn)
        return this /* chain */
    }

    /**
     * Shortcut for dispatchEvent
     * @param {string} event type 
     * @param {any} event data
     */
    this.emit = (type, data) => {
        this.dispatchEvent(type, data)
        return this /* chain */
    }

     /**
     * Shortcut for clearEventListeners
     * @param {string} event type 
     */
    this.clear = type => {
        this.clearEventListeners(type)
        return this
    }

    /**
     * 
     * @param {string} [type]
     */
    this.list = type => this.getEventListeners(type)
}

module.exports = Emitter 
```

## 搞定！

干得好，你成功地复制粘贴了我的代码！
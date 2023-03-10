# 调用 Vue 组件中的超级方法

> 原文：<https://dev.to/ashraful/call-super-method-in-vue-component-3pl2>

### 打气

到目前为止(VueJS v2 ),我们还没有像 react 这样的基于类的组件。有自己的语法，而不是传统的 javascript。在这种情况下，你有没有想过从 mixin 或其他组件中调用一个方法？好吧！！我再解释一下。

### 什么已经有了？

在 ES6 或更高版本中，我们有能力编写类，即使我们有超级方法来调用父类方法。例子如下...

```
// Defining Parent class in Javascript (ES6)
class Parent {
  getAge() {
    return 52
  }
}

// Defining Child class in Javascript (ES6)
class Child extends Parent {
  getAge() {
    const parentAge = super.getAge()
    // We will get parent ager here. And do further process
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**您是否感觉到 Vue 组件不允许这样做？**

### 解决 Vue 组件

有一个图书馆使这成为可能。[来源 **Vue 超叫**T3】](https://github.com/iashraful/vue-super-call)

#### 如何在你的项目中使用？

*   在`npm install vue-super-call`之前安装
*   添加你的 main.js/index.js(你的项目的入口文件)如下，

```
import VueSuperMethod from 'vue-super-call'

Vue.prototype.$super = VueSuperMethod 
```

Enter fullscreen mode Exit fullscreen mode

#### 如何调用超距法？

*   基本语法是`this.$super(Mixin).yourMethod()`
*   正确的例子如下...

```
// This is mixin
export default {
    name: 'MyMixin',
    methods: {
        sayHello() {
            return "Hello"
        }
    }
}

// Your Component
export default {
    name: 'MyComponent',
    mixins: [MyMixin],
    methods: {
        sayHello() {
            let parentMethodData = this.$super(MyMixin).sayHello()
            console.log(parentMethodData)
            console.log('I am from component after super call')
        }
    }
}

// Console Output
$ Hello
$ I am from component after super call 
```

Enter fullscreen mode Exit fullscreen mode

*与 VueJS 一起玩得开心:)*
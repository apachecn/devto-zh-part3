# 在组件方法中访问 vue 全局过滤器

> 原文：<https://dev.to/flowck/accessing-vue-global-filters-inside-component-methods-1pai>

通常情况下，您需要从 vue 应用程序的方法和组件的生命周期中访问全局过滤器。没有黑客和疯狂的导入也可以做到，取而代之的是你将使用 [$options 对象](https://vuejs.org/v2/api/#vm-options) :

```
this.$options.filters.yourFilterName(paramValue); 
```

## 演示

让我们假设您的`main.js`中有一个全局过滤器，它将常规的`Numbers`格式转换成美元货币格式，您将会得到如下结果:

```
// main.js
import Vue from "vue";

/**
 * numberToUSD: It format numbers into the USD currency format.
 * @param {number} value
 * @returns {string} value
 */
Vue.filter("numberToUSD", value => { 
    return value ? `$${value.toLocaleString("en-US")}` : "$0.0";
}); 
```

然后你想在一个组件方法或者生命周期方法中访问同样的过滤器，使用 [$options 对象](https://vuejs.org/v2/api/#vm-options)类似于:

```
// App.vue

export default {
    name: "App.vue",
    data() {
        return {
            value: 5000
        };
    },
    methods: {
        /*
         * Inside a regular method
         */
        logValueInUSD() {
            console.log(this.$options.filters.numberToUSD(this.value));
        }
    },
    /**
     * Inside lifecycle method: created()
     */
    created() {
        console.log(this.$options.filters.numberToUSD(this.value));
    }
} 
```

这种技术对我来说非常方便，特别是当我需要将值作为道具传递给第三方组件时，我不能使用插值和管道来应用它们。

我在通过$options 访问全局过滤器时注意到的唯一缺点是，访问过滤器的指令可能会变得有点长，但这可以通过将`this.$options.filters`打包成一个变量，然后像这样访问特定的过滤器来轻松克服:

```
const filters = this.$options.filters;

filters.numberToUSD(this.value); 
```

谢谢你的邀请，再见👋👋。
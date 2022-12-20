# Vue 2.6.6 版本第 1 部分异步错误处理

> 原文：<https://dev.to/jsbaguette/vue-266-release-part1-23b4>

是的，Vue 的新版本发布了！🎉

今天我们将讨论第一部分:异步错误处理

如需了解 vue 异步错误处理的历史，请查看此处的

首先，如何配置异步错误处理！

```
Vue.config.errorHandler = (err, vm, info) => {
  // err => the message error
  // vm the exact component where the error occur
  // info is error type v-on / lifecycle etc
  console.log("[ERROR CATCH]: ", err);
  console.log("[ERROR COMPONENT]: ", vm);
  console.log("[ERROR INFO]: ", info);
}; 
```

Enter fullscreen mode Exit fullscreen mode

作为老版本，你可以很容易地想象到一些定制的错误跟踪服务，如
或 [Bugsnag](https://docs.bugsnag.com/platforms/javascript/vue/) 所做的。

我们现在就要跑进天堂了！

# 1)组件生命周期挂钩😍

下面是钩子生命周期中错误处理的三种方式:

```
export default {
  name: "myAwesomeComponent",
  mounted() {
     // i do some work here
     throw new Error("i am a bad error");
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
export default {
  name: "myAwesomeComponent",
  mounted() {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        reject("i am a bad error");
      }, 2000);
    });
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
 const err = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      reject("i am a bad error");
    }, 2000);
  });
};

export default {
  name: "myAwesomeComponent",
  async mounted() {
      let ret = err();
      return ret;
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 重要:

⚠️ ⚠️:如果你使用 Promise 或 async，你应该像第二个和第三个例子那样返回它。如果不这样做，vue 将不会捕捉到 config.error 中的错误。

## 不要这样！👿👿👿

```
export default {
  name: "myAwesomeComponent",
  mounted() {
    // it will not being catch
    new Promise((resolve, reject) => {
      setTimeout(() => {
        reject("i am a bad error");
      }, 2000);
    });
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

```
const err = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      reject();
    }, 2000);
  });
};
export default {
  name: "myAwesomeComponent",
  async mounted() {
    // it will not being catch
    let ret = err();
    ret;
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

在这里你应该返回 ret 并返回新的承诺

# 2) Vue 事件处理程序(自定义和 DOM 监听器)😍

## Sync:

一如既往......代码！😎 😎

```
<template>
  <div>I am a simple component</div> </template> 
<script>
export default {
  name: "comp",
  mounted() {
    this.$emit("custom")
  }
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

```
<template>
    <div>
       <comp v-on:custom="customE"/>
    </div> </template> 
<script>
import Cmp from "@/Component";
export default {
  components: {
    Cmp
  },
  name: "app",
  methods: {
    customE() {
      throw new Error("i am a bad error");
    }
  }
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

简单对吗？😄

## 异步:

```
<template>
  <div>I am a simple component</div> </template> 
<script>
export default {
  name: "comp",
  mounted() {
    setTimeout(() => {
        this.$emit("custom")
    }, 1500)
  }
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

```
<template>
    <div>
       <comp v-on:custom="customE"/>
    </div> </template> 
<script>
const err = () => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      reject("i am a bad error");
    }, 2000);
  });
};
import Cmp from "@/Component";
export default {
  components: {
    Cmp
  },
  name: "app",
  methods: {
    customE() {
      return err(); // the return is IMPORTANT here
    }
  }
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

这样，来自那些承诺链的错误也将被处理。所以只要你想回来，你就可以把它们锁起来。

感谢阅读:)。

如果你想关注我或者知道我实际上在做什么！

*   [我的推特](https://twitter.com/giraud_florent)
*   [我的实际工作](https://f3ltron.github.io/vuepress-component-docgen/)
*   [我的 github](https://github.com/f3ltron)
*   [我的企业](https://www.atecna.fr)

**PS** :如果你想了解更多关于发布[的信息，来这里](https://medium.com/the-vue-point/vue-2-6-released-66aa6c8e785e)
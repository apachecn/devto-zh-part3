# 断点对顺风和顺风的反应

> 原文：<https://dev.to/jsbaguette/tailwind-vuejs-breakpoint-reactivity-2al7>

# 你好 vuejs 情人！❤️

## TL:TR

在[顺风](http://tailwindcss.com)之前，我使用了[尾化](http://vuetifyjs.com)。Vuetify 是一个不可思议的 css 框架！

但是🤔

我在 Vuetify 上遇到了一些问题:

*   “被迫”使用虚拟化组件
*   在某些情况下做出改变会变得非常困难....

从那以后，我发现了顺风！

Tailwind 是我们所说的“Css 实用程序”，它允许我们有更多的模块化。

话虽如此。

我喜欢而没有发现的是与 VueJS 相关的断点反应。它可以让你动态地改变 css 类或者样式等等...

```
<template>
  <div :class="{classCustom: breakpoints.xs}" ></div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

所以我决定自己来。

## 我们去连接顺风断点与 VueJS 的反应😇

首先，我受到 Vuetify 所做的事情的启发，通过[插件](https://vuejs.org/v2/guide/plugins.html)集成断点逻辑。这很方便，因为它添加了一些“全局属性”= >断点。

是的，但它是“全球性的”。我不喜欢全球化的这一方面，这意味着你必须接受它...如果我不想让它们出现在我的组件中，并在编译后节省大量代码。最重要的是，酒店名称等的定制几乎是不可能的。

所以我决定利用 Vue 可观测性。

```
import Vue from 'vue'

const screens = {
  sm: 640,
  md: 768,
  lg: 1024,
  xl: 1280
}

const sm = val => val >= screens.sm && val <= screens.md
const md = val => val >= screens.md && val <= screens.lg
const lg = val => val >= screens.lg && val <= screens.xl
const xl = val => val >= screens.xl

const getBreakpoint = w => {
  if (sm(w)) return 'sm'
  else if (md(w)) return 'md'
  else if (lg(w)) return 'lg'
  else if (xl(w)) return 'xl'
  else return 'all'
}
const debounce = function(func, wait) {
  var timeout
  return () => {
    const later = function() {
      timeout = null
    }
    const callNow = !timeout
    clearTimeout(timeout)
    timeout = setTimeout(later, wait)
    if (callNow) func()
  }
}

const breakpoints = Vue.observable({
  w: window.innerWidth,
  h: window.innerHeight,
  is: getBreakpoint(window.innerWidth)
})

window.addEventListener(
  'resize',
  debounce(() => {
    breakpoints.w = window.innerWidth
    breakpoints.h = window.innerHeight
    breakpoints.is = getBreakpoint(window.innerWidth)
  }, 200),
  false
)

export default breakpoints 
```

Enter fullscreen mode Exit fullscreen mode

你现在可以像这样简单地使用它们...

```
<template>
  <div>{{ breakpoints.is }} {{ breakpoints.w }} {{ breakpoints.h }} </div>
</template>
<script>
import breakpoints from '@/plugins/breakpoints'

export default {
  name: 'Component',
  data: () => {
    return {
      breakpoints
    }
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！由于我们使用的是 Vue.observable，Vue 会自动将这些数据转化为活性数据。

ps:它与功能组件配合得很好！

```
<script>
import breakpoints from '@/plugins/breakpoints.js'

export default {
  name: 'TitleXL',
  functional: true,
  props: {
    text: {
      type: String,
      default: ''
    }
  },
  render: (h, { data, props }) => {
    const textW = breakpoints.mdAndUp ? 'text-5xl' : 'text-3xl'

    return (
      <div class={textW} {...data}>
        {props.text}
      </div>
    )
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

现在你不会在你不再需要的地方得到数据，❤️

如你所知，任何代码都是可以改进的。如果您有任何建议，请随时联系我或发表评论或喜欢这篇文章:)

我的推特:[https://twitter.com/giraud_florent](https://twitter.com/giraud_florent)
我的 LinkedIn:[https://www.linkedin.com/in/fgiraud42/](https://www.linkedin.com/in/fgiraud42/)
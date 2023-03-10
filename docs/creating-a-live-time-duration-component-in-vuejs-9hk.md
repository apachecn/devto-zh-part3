# 在 Vue.js 中创建实时持续时间组件

> 原文：<https://dev.to/raymondcamden/creating-a-live-time-duration-component-in-vuejs-9hk>

原谅这篇文章有点尴尬的标题。今天的演示是基于我最近在 cnn.com 网站上看到的一些东西——政府关闭已经持续了多长时间的实时更新。他们现在没有在网上，但它基本上显示了这一点…

部分政府关闭已经持续了 11 天 15 小时 49 分 7 秒

这些值会实时更新。你可能会说(我也同意)这对于一个新闻网站来说有点过于戏剧化了(也许这就是它现在不存在的原因)，但当我看到它时，我认为在 Vue 中构建它会很有趣。我最终有了不止一个，而是两个想法的迭代，我想在下面分享它们。一如既往，我欢迎您对可以改进的地方提出意见。让我们开始吧。

## 版本一

最初的版本从一组简单的特性开始。该组件应该接受一个`date`值(无论是过去还是未来),然后简单地显示持续时间，同时自动更新它。这里有一个如何使用它的例子:

```
<div id="app" v-cloak>

  My birthday was <time-since :date="birthday"></time-since> ago. 

</div> 
```

Enter fullscreen mode Exit fullscreen mode

这是它背后的 JavaScript 代码。首先，就 Vue app 本身:

```
const app = new Vue({
  el:'#app', 
  data:{
    birthday:new Date(2018, 3, 8)
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我只是添加了一个日期值。真正的肉在组件里:

```
Vue.component('time-since',{
  template:`
<span> days hours minutes seconds</span>
`,
  data() {
    return {
      interval:null,
      days:0,
      hours:0,
      minutes:0,
      seconds:0,
      intervals:{
        second: 1000,
        minute: 1000 * 60,
        hour: 1000 * 60 * 60,
        day: 1000 * 60 * 60 * 24
      }
    }
  },
  props:{
    date:{
      required:true
    }
  },
  mounted() {
    this.interval = setInterval(() => {
      this.updateDiffs();
    },1000);

    this.updateDiffs();
  },
  destroyed() {
    clearInterval(this.interval);    
  },
  methods:{
    updateDiffs() {
      //lets figure out our diffs
      let diff = Math.abs(Date.now() - this.date.getTime());
      this.days = Math.floor(diff / this.intervals.day);
      diff -= this.days * this.intervals.day;
      this.hours = Math.floor(diff / this.intervals.hour);
      diff -= this.hours * this.intervals.hour;
      this.minutes = Math.floor(diff / this.intervals.minute);
      diff -= this.minutes * this.intervals.minute;
      this.seconds = Math.floor(diff / this.intervals.second);
    }
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

好吧，让我们从上到下解决这个问题。该模板相当简单，并且是硬编码的，将持续时间显示为:

```
<span>X days Y hours Z minutes A seconds</span> 
```

Enter fullscreen mode Exit fullscreen mode

这是无法改变的。下一个块处理组件的数据，唯一有趣的部分(在我看来)是建立数学来记住各种基于毫秒的间隔。

接下来看`mounted`和`destroyed`。`mounted`负责设置第二个基本间隔来更新显示(并立即运行它)。`destroyed`如果组件从 DOM 中完全删除，则处理删除间隔。

最后，`updateDiffs`只是处理数学运算。直觉告诉我，这一部分可能由比我聪明的人用更少的代码编写，但它确实有效，所以我不去管它。

您可以在此代码栏中查看完整的代码(并使用它):

[https://codepen.io/cfjedimaster/embed/BvYPPM?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/BvYPPM?height=600&default-tab=result&embed-version=2)

很酷，对吧？但是，让我们看看我们如何能踢它一个档次。

## 版本第二

所以第一个版本的一个问题是它强制一种特殊的输出。如果您想对显示进行一些定制，该怎么办？这就是老虎机的用武之地。看看这个版本:

```
<div id="app" v-cloak>

  My birthday was <time-since :date="birthday">
  <template slot-scope="int">
  D H M S
  </template>
  </time-since> ago. 

</div> 
```

Enter fullscreen mode Exit fullscreen mode

在这个版本中，我使用了一个槽，并定制了用于间隔的标签，使其更接近 CNN 版本。如果我愿意，我甚至可以去掉秒的值，让它不那么令人分心。让我们看看更新的组件。

```
Vue.component('time-since',{
  template:`
<span>
<slot :days="days" :hours="hours" :minutes="minutes" :seconds="seconds"> days hours minutes seconds</slot>
</span>
`,
 //stuff deleted...
}); 
```

Enter fullscreen mode Exit fullscreen mode

变化相当小。现在，该模板支持默认输出(与以前的版本相同)，而且还绑定了可在标记中使用的所有四个区间的值。只有当你没有传入一个槽的时候，这个槽里面的文本才会被使用。现在，该组件支持与以前相同的输出，而且还支持完全定制。

您可以在这里看到输出:

[https://codepen.io/cfjedimaster/embed/xmjROa?height=600&default-tab=result&embed-version=2](https://codepen.io/cfjedimaster/embed/xmjROa?height=600&default-tab=result&embed-version=2)

好吧，你觉得怎么样？
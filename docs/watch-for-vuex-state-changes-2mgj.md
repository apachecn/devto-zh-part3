# 注意 Vuex 状态变化！

> 原文：<https://dev.to/viniciuskneves/watch-for-vuex-state-changes-2mgj>

这是我在 Dev.to 上的第一个帖子，所以我会感谢任何可以帮助我提高整体写作水平的反馈，以及我可能忘记写和解释的事情！第一段写完了，我们走吧！

* * *

今天有人在 Slack 上问了一个关于如何处理 Vue 组件中不同的*状态*的问题。他想要的是这样的:你发出一个请求，它有 3 个基本的*状态*(等待/加载、成功、失败/错误)。在一个 Vue 组件中如何处理？他问了用 Vuex 做这件事的方法(他用的是 Vuex)，但我会后退一步，因为没必要用 Vuex 做这件事(但我也会探索 Vuex 的世界)。

首先，我们有 3 种状态，我们必须对每一种状态做出不同的反应。下面的片段展示了一种方法:

```
<template>
  <h1 v-if="status === 'success'">Success</h1>
  <h1 v-else-if="status === 'error'">Error</h1>
  <h1 v-else>Loading</h1>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

它基本上根据期望的行为*状态*显示不同的消息。

让我们首先假设它是一个单独的组件，并且所请求的数据在其他任何地方都不需要(父组件或兄弟组件)，这使得该方法很简单(我将在后面研究其他组件)。

我假设你对 Vue.js 有点熟悉，这意味着你知道`created`、`methods`和`data`。现在让我们为这个特定的组件实现期望的行为(`api.get`正在模仿一个有 1s 延迟的 API 请求，这样我们就可以看到状态的转换)。

```
import api from '@/api';

export default {
  name: 'simple',
  data() {
    return {
      status: 'pending',
    };
  },
  created() {
    console.log(`CREATED called, status: ${this.status}`);

    this.handleCreated();
  },
  methods: {
    async handleCreated() {
      try {
        await api.get();

        this.status = 'success';
      } catch (e) {
        console.error(e);

        this.status = 'error';
      }
    },
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

这里没有什么大不了的，因为所有的事情都是在组件内部处理的，而提出这个问题的人却不是这样。我想他的背景有点不同。在他的例子中，*状态*需要在其他组件之间共享，而不仅仅是这个组件的子组件。在这种情况下，我们可能有一个共享的**状态**，这就是 Vuex 的用武之地(你可以用`Event Bus`达到同样的效果，甚至比仅仅为这个唯一的状态添加 Vuex 还要好)。

现在让我们更新我们的组件，使用来自 Vuex 商店的*状态*,而不是本地值。为此，我们首先创建*状态*状态。

```
export default new Vuex.Store({
  state: {
    status: 'pending',
  },
  mutations: {

  },
  actions: {

  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们更新我们的组件来使用`state.status` :

```
<template>
  <h1 v-if="status === 'success'">Success</h1>
  <h1 v-else-if="status === 'error'">Error</h1>
  <h1 v-else>Loading</h1>
</template>

<script>
import { mapState } from 'vuex';

export default {
  name: 'vuex1',
  computed: mapState(['status']),
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

下一步是在调用 API 后更新*状态*。我们可以像以前一样实现它，只是引用 Vuex 商店内的*状态*，但这是一种非常糟糕的方式。现在正确的做法是分派一个 Vuex 动作来为我们处理它，所以首先我们创建动作来处理它:

```
export default new Vuex.Store({
  state: {
    status: 'pending',
  },
  getters: {
    status: state => state.status,
  },
  mutations: {
    updateStatus(state, status) {
      Vue.set(state, 'status', status);
    },
  },
  actions: {
    async fetchApi({ commit }) {
      try {
        await api.get();

        commit('updateStatus', 'success');
      } catch (e) {
        console.error(e);

        commit('updateStatus', 'error');
      }
    },
  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们假设状态在其他组件之间共享，并且我们不希望每个组件一遍又一遍地调度相同的动作，那么从组件调度我们的动作就没有意义了。因此，我们在我们的`App.vue`文件或任何其他对您的应用程序有意义的组件(可能在一个视图的主要组件中)中调度我们的动作。下面是调度创建的动作的`App.vue`文件的片段:

```
<template>
  <div>
    <simple />
    <vuex1 />
  </div>
</template>

<script>
import Simple from '@/components/Simple.vue';
import Vuex1 from '@/components/Vuex1.vue';

export default {
  name: 'app',
  components: {
    Simple,
    Vuex1,
  },
  created() {
    this.$store.dispatch('fetchApi');
  },
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

酷，现在它的工作正如预期的那样！但我什么都没告诉你。他试图解决的问题比这个问题更复杂。他希望当*状态*改变时，由这个*状态*更新的一些组件表现不同。想象一下，一旦这个 API 调用成功，您可能想要为每个组件分派不同的动作，但是当您只从页面中呈现的组件分派动作时，您如何实现这一点呢？

我在这里的目的是向您展示处理这种情况的几种可能性。我事先同意的一件事是，对于我们大多数人来说，这听起来可能是一个尴尬的局面，但请尝试抽象我向您展示的场景，并关注您可以从我在这里展示的功能中实现什么(您可能会有一个完全不同的场景，其中该解决方案比这里更适合)。

## 手表

实现我们想要的解决方案的最简单方法。您可以[观察](https://vuejs.org/v2/api/#watch)的属性更新，并以您想要的方式处理它。在下面的例子中，我们需要更新一个“复杂”的对象，否则我们的组件会崩溃:

```
<template>
  <h1 v-if="status === 'success'">Success {{ complex.deep }}</h1>
  <h1 v-else-if="status === 'error'">Error</h1>
  <h1 v-else>Loading</h1>
</template>

<script>
import { mapState } from 'vuex';

export default {
  name: 'vuex2',
  data() {
    return {
      complex: null,
    };
  },
  computed: mapState(['status']),
  watch: {
    status(newValue, oldValue) {
      console.log(`Updating from ${oldValue} to ${newValue}`);

      // Do whatever makes sense now
      if (newValue === 'success') {
        this.complex = {
          deep: 'some deep object',
        };
      }
    },
  },
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## Vuex 观察

您知道吗，您还可以使用 Vuex 来观察变化？以下是[份文件](https://vuex.vuejs.org/api/#watch)。唯一的要求是，它监视一个函数，该函数接收 State 作为第一个参数，Getters 作为第二个参数，并返回另一个函数，该函数的结果将被监视。

使用 Vuex watch 时有一个警告:它返回一个`unwatch`函数，如果你想停止观察器，应该在你的`beforeDestroy`钩子中调用这个函数。如果不调用这个函数，观察器仍然会被调用，这不是我们想要的行为。

这里要记住的一点是，反应发生在调用观察回调之前，这意味着我们的组件将在我们设置复杂对象之前更新，所以我们需要在这里注意:

```
<template>
  <h1 v-if="status === 'success'">Success {{ complex && complex.deep }}</h1>
  <h1 v-else-if="status === 'error'">Error</h1>
  <h1 v-else>Loading</h1>
</template>

<script>
import { mapState } from 'vuex';

export default {
  name: 'vuex3',
  data() {
    return {
      complex: null,
    };
  },
  computed: mapState(['status']),
  created() {
    this.unwatch = this.$store.watch(
      (state, getters) => getters.status,
      (newValue, oldValue) => {
        console.log(`Updating from ${oldValue} to ${newValue}`);

        // Do whatever makes sense now
        if (newValue === 'success') {
          this.complex = {
            deep: 'some deep object',
          };
        }
      },
    );
  },
  beforeDestroy() {
    this.unwatch();
  },
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

## Vuex 订阅

您可以[订阅](https://vuex.vuejs.org/api/#subscribe)突变，这意味着无论何时提交突变，您的处理程序都会被调用(您可以用[订阅动作](https://vuex.vuejs.org/api/#subscribeaction)对动作进行同样的操作)。这有点棘手，因为我们不会只订阅特定的突变，所以我们必须在这里小心。

使用 Vuex subscribe 时有一个注意事项:它返回一个`unsubscribe`函数，如果您想停止订阅者，应该在您的`beforeDestroy`钩子中调用这个函数。如果不调用这个函数，订阅者仍然会被调用，这不是我们想要的行为。

这里的缺点是，我们已经丢失了旧的值，但是作为第一种情况，它是在反应发生之前被调用的，所以我们避免了双重检查，如果这是一个问题的话。结果如下面的代码片段所示:

```
<template>
  <h1 v-if="status === 'success'">Success {{ complex.deep }}</h1>
  <h1 v-else-if="status === 'error'">Error</h1>
  <h1 v-else>Loading</h1>
</template>

<script>
import { mapState } from 'vuex';

export default {
  name: 'vuex4',
  data() {
    return {
      complex: null,
    };
  },
  computed: mapState(['status']),
  created() {
    this.unsubscribe = this.$store.subscribe((mutation, state) => {
      if (mutation.type === 'updateStatus') {
        console.log(`Updating to ${state.status}`);

        // Do whatever makes sense now
        if (state.status === 'success') {
          this.complex = {
            deep: 'some deep object',
          };
        }
      }
    });
  },
  beforeDestroy() {
    this.unsubscribe();
  },
};
</script> 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

正如我前面提到的，我的想法不仅仅是解决 Slack 的那个人提出的问题。我想从更广泛的角度分享可用的解决方案以及如何使用它们。

你可能有一个不同的问题，这些解决方案可能很适合，但正如我在这篇文章中所做的:保持简单！我从一个针对特定问题的非常简单的解决方案开始，你也应该这样做。等到性能问题或重构出现后，再解决复杂的解决方案。

你也可以在 Github 上查看:[vue-listen-to-change-example](https://github.com/viniciuskneves/vue-listen-to-change-example)

#### 更新

*   23/03/2020:追加`unwatch` / `unsubscribe`呼叫`beforeDestroy`。特别感谢 [@opolancoh](https://dev.to/opolancoh) 在[评论](https://dev.to/opolancoh/comment/mofi)中提及。
    *   拉请求:[https://github . com/viniciuskneves/vue-listen-to-change-example/pull/7](https://github.com/viniciuskneves/vue-listen-to-change-example/pull/7)
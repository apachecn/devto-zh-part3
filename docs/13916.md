# 第 014 集-用 Vuex-ASP.NET 核心集中前端状态:从 0 到过度杀伤

> 原文：<https://dev.to/joaofbantunes/episode-014---centralizing-frontend-state-with-vuex---aspnet-core-from-0-to-overkill-1dhe>

在这一集里，我们继续在前端玩，通过使用 Vuex 集中所有状态，利用最近可能更流行的模式，因为它们与 React 和 Redux 一起使用。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/OtQVABhGPgQ](https://www.youtube.com/embed/OtQVABhGPgQ)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

在上一集中，我们开始使用 Vue.js 构建前端。到目前为止，应用程序没有与后端通信，所有数据都保存在组件实例中。在这一集里，我们还没有连接前端和后端，但是你将改变我们处理数据的方式，通过使用一种状态管理模式将所有东西集中在一个存储中，正如在 [Vuex](https://vuex.vuejs.org/) 文档中所描述的。

Vuex 和 Vue.js 一起用，就像 [Redux](https://redux.js.org/) 和 [React](https://reactjs.org/) 一起用， [NgRx](https://ngrx.io/) 和 [Angular](https://angular.io/) 一起用一样(还有更多备选方案，但我认为这些是最知名的)。

正如我提到的，在前端使用存储的目标不仅是拥有一个处理数据输入和输出的单点，而且还要避免相同的数据分散在组件中，从而导致各种问题，以确保一切保持同步。通过保持数据的集中，所有感兴趣的组件都可以看到每一个变化，然后能够相应地更新它们的 UI。

对于应用程序的当前状态，它真的很小很简单，使用商店是多余的，但是考虑到我们正在构建的项目，我们已经可以预期应用程序将会增长，所以我们可以从一开始就为此做好准备。

## Vuex 核心概念

让我们先快速浏览一下 Vuex 的核心概念，这些概念在[项目的主页](https://vuex.vuejs.org/)中有很好的记录。

### 状态

状态是包含所有应用程序级数据的单个对象。这是我们获取数据以供给组件的地方，也是所有更改必须到达的地方，尽管组件不会直接更改状态，而是通过使用突变。

### 突变

突变是改变状态的方式。突变像事件一样工作，有一个类型/名称和一个关联的处理程序。当我们想要做出改变时，我们做一个调用处理程序的`store.commit('mutation-type')`(或者如果我们想要将数据传递给突变处理程序，就做`store.commit('mutation-type', somePayload)`)。

### 吸杂

Getters 类似于状态的数据库视图。如果我们只是使用来自存储的数据，我们就不需要使用它们(因为在本文中我们不会使用它们)，但是如果我们想要一些派生的信息，并在多个地方使用它，我们可以使用 getters 来集中这个自定义的状态视图。

### 动作

动作的调用和设置方式与突变相似，但用途不同。从组织的角度来看，突变不应该真的有逻辑，只应该改变给定(可选)输入的状态。另一方面，动作可能有逻辑并导致副作用，比如调用 API。动作不会直接改变状态，它们必须为此调用突变。此外，在调用 API 的上下文中非常重要的是，当动作允许异步代码时，突变必须是同步的。

### 模块

正如我前面提到的，状态是一个包含所有数据的单一对象。随着应用程序的增长，状态也随之增长，商店可能会开始过度增长，并且在开发时变得难以推理。模块允许我们分割存储(包括它的所有部分:状态、突变、动作，...)才能有更好的组织。

## 替换 store.ts

如果你还记得上一篇文章，当我们搭建应用程序时，我们有机会选择我们想要的特性，其中之一就是 Vuex。鉴于此，我们在项目的`src`文件夹中有一个`store.ts`文件。然而，我们将使用多个文件，以便更好地组织商店，所以我们将创建一个名为`store`的新文件夹，将现有的`store.ts`文件移到那里，并将其重命名为`index.ts`。

## 创建状态

让我们最终进入代码！我们将从定义我们的状态开始，也就是它的模型。请记住，我正在做的一些事情并不是真正必要的，但我正在使用 TypeScript，并尽可能使一切都显式，所以这段代码最终比通常的 Vue.js 示例更加冗长，这些示例通常不使用 TypeScript(并且由更有能力的前端开发人员创建😛).

在`store`文件夹中，我们创建了一个`state.ts`文件，在这里我们基本上定义了状态模型。

`store/state.ts`

```
export interface Group {
    id: number;
    name: string;
    rowVersion: string;
}

export interface RootState {
    groups: Group[];
} 
```

Enter fullscreen mode Exit fullscreen mode

`Group`型号与我们之前见过太多次的型号相同，但我不想在商店里提到这些型号。`RootState`将代表我们讨论过的单一状态树对象。现在我们所有的是组的集合，最终我们会添加更多的东西。

现在我们有了状态的模型，让我们在`store/index.ts`文件中使用它们。在编辑它之前，让我们先看看已经存在的东西，它们是由 Vue CLI 搭建的。

`store/index.ts`

```
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);

export default new Vuex.Store({
  state: {

  },
  mutations: {

  },
  actions: {

  },
}); 
```

Enter fullscreen mode Exit fullscreen mode

只发生了几件事，所以理解起来应该很简单，但总而言之，我们通过调用`Vue.use(Vuex)`告诉 Vue 使用 Vuex，然后我们导出一个`Vuex.Store`的新实例，传入一个配置对象。同样，在`src`文件夹的`main.ts`文件中，存储被导入并传递给`Vue`构造函数。

我想在编辑后的版本之前显示原始版本，因为您会注意到我将`Vuex.Store`构造函数参数提取到了一个变量中，所以我可以声明它的类型并在创建对象时得到一些提示。

`store/index.ts`

```
import Vue from 'vue';
import Vuex, { StoreOptions } from 'vuex';
import { RootState } from './state';

Vue.use(Vuex);

export const options: StoreOptions<RootState> = {
    state: {
        groups = [
            { id: 1, name: 'Sample Group', rowVersion: 'aaa' },
            { id: 2, name: 'Another Sample Group', rowVersion: 'bbb' }
        ]
    },
    mutations: {},
    actions: {}    
};

export default new Vuex.Store(options); 
```

Enter fullscreen mode Exit fullscreen mode

实际上没有太多变化，就像前面提到的，我们将`Vuex.Store`构造函数参数提取到一个变量中，将其类型声明为`StoreOptions<RootState>`，然后用一些数据初始化状态，这样我们就可以立即在浏览器中看到一些东西。

## 从组件中访问状态

从组件访问状态相当简单。正如我们在上一集中看到的，我们只需要在`Groups.vue`中进行修改，因为它是我们的智能组件，并将数据传递给它的子组件。

`Groups.vue`

```
<template>
  <GroupList v-bind:groups="groups" v-on:update="onUpdate" v-on:remove="onRemove" v-on:add="onAdd"/>
</template>
<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import GroupList from '@/components/groups/GroupList.vue';
import { GroupViewModel } from '@/components/groups/models';
import { types } from '@/store/modules/groups/actions';

@Component({
  components: {
    GroupList
  }
})
export default class Groups extends Vue {
  private get groups(): GroupViewModel[] {
      return this.$store.state.groups;
  }

  private onUpdate(group: GroupViewModel): void {
    // TODO: use store
  }

  private onRemove(groupId: number): void {
    // TODO: use store
  }

  private onAdd(group: GroupViewModel): void {
    // TODO: use store
  }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

主要的变化是我们拥有的`groups`属性，现在已经变成了一个访问商店的 getter。你不需要特别使用一个 getter，我就是这么做的，但是请记住，如果你只是用存储中的值初始化一个属性，如果状态改变了，我们不会看到它。

```
// if we initialized the property like this and later the state changed
// we wouldn't see it reflected in the UI
private groups: GroupViewModel[] = this.$store.state.groups; 
```

Enter fullscreen mode Exit fullscreen mode

## 利用突变来改变状态

现在我们有了状态，可以在组件中使用它，但是我们缺少改变组的能力，就像我们的事件处理程序(`onUpdate`)一样。`onRemove`和`onAdd`不能像以前一样直接操作数据，因为它不再是组件的本地数据。为了重新获得改变状态的能力，我们将使用突变。

为此，让我们回到`store/index.ts`并创建突变。我们将使用与在`Groups.vue`事件处理程序中相同的逻辑。

`store/index.ts`

```
// ...

let currentId: number = 0;

export const options: StoreOptions<RootState> = {
    // ...
    mutations: {
        add(state: GroupsState, group: Group): void {
            group.id = ++currentId;
            state.groups = [...state.groups, group];
        },
        update(state: GroupsState, group: Group): void {
            const index = state.groups.findIndex(g => g.id === group.id);
            state.groups = [...state.groups.slice(0, index), group, ...state.groups.slice(index + 1, state.groups.length)];
        },
        remove(state: GroupsState, groupId: number): void {
            state.groups = state.groups.filter(g => g.id !== groupId);
        },
    },
    // ...
};
// ... 
```

Enter fullscreen mode Exit fullscreen mode

你可以看到，我们基本上只是把逻辑从一个地方移到了另一个地方。一个细微的区别是，`add`、`update`和`remove`函数不像组件那样直接操作某些属性，而是接收状态作为第一个参数，突变的有效载荷作为第二个参数。`currentId`变量不是很大，但这只是一个临时的解决方案，直到我们连接到 web API(在下一集)，所以我不会打扰。

现在我们需要从组件的事件处理程序中调用突变。

`Groups.vue`

```
// ...

private onUpdate(group: GroupViewModel): void {
  this.$store.commit('update', group);
}

private onRemove(groupId: number): void {
  this.$store.commit('remove', groupId);
}

private onAdd(group: GroupViewModel): void {
  this.$store.commit('add', group);
}

// ... 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果我们在浏览器中检查应用程序，我们会看到与之前相同的功能，现在使用商店。

## 利用动作引起副作用

虽然我们现在不需要动作，因为我们不会引起副作用，也就是向 API 发出请求，但我们会创建它们以备不时之需。我们还将在创建动作时对状态和突变进行一些调整。

`store/index.ts`

```
// ...

export const options: StoreOptions<RootState> = {
    state: {
        groups: []
    },
    mutations: {
        setGroups(state: GroupsState, groups: Group[]): void {
            state.groups = [...groups];
        },
        // ...
    },
    actions: {
        loadGroups({ commit }): void {
            // TODO: fetch groups from the api
            const groups = [
                { id: 1, name: 'Sample Group', rowVersion: 'aaa' },
                { id: 2, name: 'Another Sample Group', rowVersion: 'bbb' }
            ];
            commit('setGroups', groups);
        },
        add({ commit }, group: Group): void {
            // TODO: make the api request before committing to the store
            commit('add', group);
        },
        update({ commit }, group: Group): void {
            // TODO: make the api request before committing to the store
            commit('update', group);
        },
        remove({ commit }, groupId: number): void {
            // TODO: make the api request before committing to the store
            commit('remove', groupId);
        }
    }
    // ...
};

// ... 
```

Enter fullscreen mode Exit fullscreen mode

首先是状态的变化和突变。状态现在用一个空的组集合初始化，因为这些集合应该来自 API。对于突变，我们添加了一个`setGroups`函数，这将允许我们使用来自 API 的响应来初始化数据。

现在的行动，你可以很容易地从 TODOs 看到是非常不完整的。

`loadGroups`动作最终将从 API 中获取数据，然后将其传递给`setGroups`变异来存储它。现在，我们正在用相同的硬编码数据初始化这些组。其他动作最终也会调用 API，并根据响应使用突变来存储数据，但是现在只是简单地使用接收到的有效载荷直接调用突变。

现在我们对`Groups.vue`做一些小的调整来利用动作。

`Groups.vue`

```
// ...

export default class Groups extends Vue {
    private get groups(): GroupViewModel[] {
        return this.$store.state.groups;
    }

    public mounted(): void {
        this.$store.dispatch('loadGroups');
    }

    private onUpdate(group: GroupViewModel): void {
        this.$store.dispatch('update', group);
    }

    private onRemove(groupId: number): void {
        this.$store.dispatch('remove', groupId);
    }

    private onAdd(group: GroupViewModel): void {
        this.$store.dispatch('add', group);
    }
}

// ... 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，在事件处理程序中，唯一的区别是我们现在调用了`dispatch`而不是`commit`，导致动作而不是突变被调用。我还添加了一个`mounted`生命周期钩子，它在组件实例挂载时被调用，也就是说在新创建的元素实例放置到位之前。在这个钩子中，我们调度 load groups 动作，所以当我们加载组件时，我们有一些数据。也许一个更好的地方把这个行动调度将是一个[导航警卫](https://router.vuejs.org/guide/advanced/navigation-guards.html)，但对于这些组件安装的初始测试已经足够好了。

## 用模块组织商店

为了更好地组织我们的商店，我们可以把它分成几个模块。此外，当我们在一个组织注释中时，我们也可以将突变和动作移动到不同的文件中。

在`store`文件夹中，我们创建了一个新的`modules`文件夹，并在其中创建了一个`groups`文件夹，这是我们目前仅有的模块。现在让我们开始创建一些文件，即一个用于状态模型，一个用于突变，一个用于动作，最后一个用于包含模块。

### 状态

对于状态，我们将拥有与在`store/state.ts`中相同的状态，但是我们将拥有一个`GroupsState`，而不是一个`RootState`，它是特定于模块的。

`store/modules/groups/state.ts`

```
export interface Group {
    id: number;
    name: string;
    rowVersion: string;
}

export interface GroupsState {
    groups: Group[];
} 
```

Enter fullscreen mode Exit fullscreen mode

在`store/state.ts`中，我们将把`RootState`保留为一个空接口(尽管 TSLint 不喜欢它)，因为我们将在一些声明中使用它，这样当我们想要向全局状态添加一些东西时，我们就不需要搜索那些声明中的每一个`{}`。

`store/state.ts`

```
export interface RootState {
} 
```

Enter fullscreen mode Exit fullscreen mode

### 突变

突变移动到它们自己的文件中，我们将导出它们的对象声明为`MutationTree<GroupsState>`。

`store/modules/groups/mutations.ts`

```
import { GroupsState, Group } from './state';
import { MutationTree } from 'vuex';

let currentId: number = 2;

export const mutations: MutationTree<GroupsState> = {
    setGroups(state: GroupsState, groups: Group[]): void {
        state.groups = [...groups];
    },
    add(state: GroupsState, group: Group): void {
        group.id = ++currentId;
        state.groups = [...state.groups, group];
    },
    update(state: GroupsState, group: Group): void {
        const index = state.groups.findIndex(g => g.id === group.id);
        state.groups = [...state.groups.slice(0, index), group, ...state.groups.slice(index + 1, state.groups.length)];
    },
    remove(state: GroupsState, groupId: number): void {
        state.groups = state.groups.filter(g => g.id !== groupId);
    },
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 动作

声明为`ActionTree<GroupsState, RootState>`的动作的新文件。

`store/modules/groups/actions.ts`

```
import { ActionTree } from 'vuex';
import { GroupsState, Group } from './state';
import { RootState } from '@/store/state';

export const types = {
    LOAD_GROUPS: 'groups/loadGroups',
    ADD_GROUP: 'groups/add',
    UPDATE_GROUP: 'groups/update',
    REMOVE_GROUP: 'groups/remove'
};

export const actions: ActionTree<GroupsState, RootState> = {
    loadGroups({ commit }): void {
        // TODO: fetch groups from the api
        const groups = [
            { id: 1, name: 'Sample Group', rowVersion: 'aaa' },
            { id: 2, name: 'Another Sample Group', rowVersion: 'bbb' }
        ];
        commit('setGroups', groups);
    },
    add({ commit }, group: Group): void {
        // TODO: make the api request before committing to the store
        commit('add', group);
    },
    update({ commit }, group: Group): void {
        // TODO: make the api request before committing to the store
        commit('update', group);
    },
    remove({ commit }, groupId: number): void {
        // TODO: make the api request before committing to the store
        commit('remove', groupId);
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 模块

最后，我们可以通过创建将利用所有其他模块的`index.ts`文件来包装我们的模块。

`store/modules/groups/index.ts`

```
import { Module } from 'vuex';
import { GroupsState } from './state';
import { RootState } from '@/store/state';
import { actions } from './actions';
import { mutations } from './mutations';

export const groups: Module<GroupsState, RootState> = {
    namespaced: true,
    actions,
    mutations,
    state: {
        groups: []
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

这类似于我们在`store/index.ts`中所做的，不同之处在于它是一个单独的模块(声明为`Module<GroupsState, RootState>`)并从其他文件中导入它的部分。

注意`namespaced`属性，这意味着要访问存储的这个模块部分，我们必须使用一个名称空间。如果这是假的，即使我们将商店分成模块进行组织，为了访问它，我们可以像模块范围内的所有东西都是全局的一样操作。

要总结存储中的更改，我们必须包括模块。

`store/index.ts`

```
import Vue from 'vue';
import Vuex, { StoreOptions, ActionContext, MutationTree, ActionTree } from 'vuex';
import { RootState } from './state';
import { groups } from './modules/groups';

Vue.use(Vuex);

const options: StoreOptions<RootState> = {
  state: {},
  modules: {
    groups
  }
};

export default new Vuex.Store(options); 
```

Enter fullscreen mode Exit fullscreen mode

### 调整组件

假设我们用`namespaced: true`配置了模块，我们需要对`Groups.vue`做一些小的调整。

`Groups.vue`

```
// ...

export default class Groups extends Vue {
    private get groups(): GroupViewModel[] {
        return this.$store.state.groups.groups; 
    }

    public mounted(): void {
        this.$store.dispatch('groups/loadGroups');
    }

    private onUpdate(group: GroupViewModel): void {
        this.$store.dispatch('groups/update', group);
    }

    private onRemove(groupId: number): void {
        this.$store.dispatch('groups/remove', groupId);
    }

    private onAdd(group: GroupViewModel): void {
        this.$store.dispatch('groups/add', group);
    }
}

// ... 
```

Enter fullscreen mode Exit fullscreen mode

在`groups` getter 中有一个额外的`groups`属性访问，用于进入模块。在事件处理程序中，`groups/*`作为前缀被添加到动作名称中(在源代码中，我将这些字符串移动到常量中，但是在这里为了更清楚起见，我直接使用了这些值)。

## 使用注释来访问存储元素

为了指出从组件访问商店的不同方式，有一个名为 [vuex-class](https://github.com/ktsn/vuex-class) 的库提供了一些绑定助手。

安装完库(`npm install --save vuex-class`)后，我们可以更改`Groups.vue`来使用这些绑定。

`Groups.vue`

```
// ...

import { Component, Vue } from 'vue-property-decorator';
import GroupList from '@/components/groups/GroupList.vue';
import { GroupViewModel } from '@/components/groups/models';
import { types } from '@/store/modules/groups/actions';
import { State, namespace } from 'vuex-class';

const groupsModule = namespace('groups');

@Component({
  components: {
    GroupList
  }
})
export default class Groups extends Vue {
    @groupsModule.State('groups') private groups!: GroupViewModel[];
    @groupsModule.Action('loadGroups') private loadGroups!: () => void;

    public mounted(): void {
        this.loadGroups();
    }

    // ...
}

// ... 
```

Enter fullscreen mode Exit fullscreen mode

与其他方法相比，这样使用它可能没有太大的优势，这只是一个偏好问题。

## 其他

最后，从用户的角度来看，我们让应用程序像以前一样工作，但是我们通过集中前端状态为未来的工作奠定了基础。就目前而言，我们看不到太大的价值，但随着应用程序的增长，我们应该会开始看到它。在下一集，我们将最终连接前端和后端。

帖子中的链接:

*   vista . js
*   [Vuex](https://vuex.vuejs.org/)
*   [反应](https://reactjs.org/)
*   [还原](https://redux.js.org/)
*   [角度](https://angular.io/)
*   [NgRx](https://ngrx.io/)
*   [Vue 路由器导航保护装置](https://router.vuejs.org/guide/advanced/navigation-guards.html)
*   vuex-class

这个帖子的源码是[这里](https://github.com/AspNetCoreFromZeroToOverkill/WebFrontend/tree/episode014)。

欢迎随时提出反馈意见。如果你觉得这有用，我感谢你的分享🙂

谢谢你的来访，cyaz！
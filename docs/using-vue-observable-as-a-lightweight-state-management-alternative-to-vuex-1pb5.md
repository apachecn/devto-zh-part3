# 使用 Vue Observable 作为 Vuex 的轻量级状态管理替代方案

> 原文：<https://dev.to/3zzy/using-vue-observable-as-a-lightweight-state-management-alternative-to-vuex-1pb5>

[**Vuex**](https://vuex.vuejs.org/) 对于合适的应用程序来说是很棒的，但是由于它对如何更改商店中的数据施加了很多限制，所以对于较简单的用例来说可能不是理想的。

Vue v2.6.0 中引入的 Vue Observable 可能是小型应用程序中语句管理的轻量级替代方案。我最近用的方式和 Vuex ie 差不多。使用 **getters** 引用一个状态属性，使用**突变**操纵状态，而不直接访问存储。

因此，让我们用对象`users`创建一个商店:

很抱歉这个人为的例子，我第一次写，所以想不出更好的了。 )

```
import Vue from "vue";

const state = Vue.observable({
  users: {
    c6676a9aca4c270086ef31a35cc80446: {
      name: "Ibrahim Ezzy",
      twitter: "3zzy",
      bio: "Software Imagineer. Front-end, UI & Design."
    },
    "4d50982553c3286d65182075c178245f": {
      name: "Tim Apple",
      twitter: "tim_cook",
      bio: "Chief Executive Officer of Apple"
    }
  }
}); 
```

一个通用的`setState`方法来更新状态中的任何对象:

```
const mutations = {
    setState({ object, objectPath, value, upsert = false } = {}) {
        console.log("setState args: ", { object, objectPath, value, upsert });
        if (state[object] === undefined || value === undefined)
            console.error("setState: Invalid Object or Value");
        if (objectPath === undefined) state[object] = value;
        if (objectPath && Array.isArray(objectPath) && objectPath.length) {
            let navigate = [object, ...objectPath.slice(0, -1)],
            valueObj = navigate.reduce((obj, prop) => {
                if (typeof obj[prop] !== "object") {
                if (upsert) {
                    obj[prop] = {};
                } else {
                    console.error(`setState: property '${prop}' doesn't exist`);
                }
                }
                return obj[prop];
            }, state);
            Vue.set(valueObj, objectPath[objectPath.length - 1], value);
        }
    }
    // other specific mutations ...
}; 
```

和一个通用的`getState`方法来从状态:
中获取任何对象

```
const getters = {
    getState({ object, objectPath } = {}) {
        if (state[object] === undefined) console.error("getState: Invalid Object.");
        if (objectPath === undefined) return state[object];
        if (objectPath && Array.isArray(objectPath) && objectPath.length) {
            let navigate = [object, ...objectPath.slice(0, -1)],
            valueObj = navigate.reduce((obj, prop) => {
                if (obj[prop] === undefined) {
                console.error(`getState: property '${prop}' doesn't exist`);
                }
                return obj[prop];
            }, state),
            value = valueObj[objectPath[objectPath.length - 1]];
            if (value === undefined) console.error(`getState: Invalid object path`);
            return value;
        }
    }
    // other specific getters ...
}; 
```

现在你可以像这样访问`users`:

```
data() {
    return {
        users: getters.getState({
            object: "users"
        })
    };
} 
```

并使用`setState` :
更新(或创建)

```
methods: {
    updateName(e, id) {
        console.log(e.target.innerText, id);
        mutations.setState({
            object: "users",
            objectPath: [id, "name"],
            value: e.target.innerText
        });
    }
} 
```

这里最有趣的部分可能是动态的`getState`和`setState`方法，它们可以创建、更新或访问状态中深度嵌套的属性。当然，这些功能并不完美，目前它们只能和`Objects`一起工作，但是你已经明白了。

这里有一个关于 [CodeSandbox](https://codesandbox.io/s/zn85x6pr1p?fontsize=14) 的演示。
# 拼凑全栈拼图。新手的视角

> 原文：<https://dev.to/tangweejieleslie/piecing-together-the-fullstack-puzzle-a-newbie-s-perspective-4k83>

## 概述

这篇文章总结了我在尝试构建我的第一个 web 应用程序时学到的实现要点。这篇文章的目的是作为初学者的快速参考，也是为了我未来的自己，因为我的记忆有问题。😩

所以 4 天后，我终于成功地将前端和后端连接起来，实现了以下预期结果:

*   用户可以输入和提交数据
*   前端显示来自 MongoDB 的数据

* * *

## 前端- Vue 组件

#### 视图模板

在模板部分，有 3 个主要组件:

*   带有对应于 MongoDB 模式的输入字段的表单
*   调用 POST 方法的按钮
*   一种显示 MongoDB 数据的方法

下面是模板部分的一段代码:

```
<template>
    <div>
        <label>User ID:</label>
        <input type="text" v-model.lazy="dataObject.userId" />

        <label>Comments:</label>
        <input type="text" v-model.lazy="dataObject.comments" />

        <button v-on:click="post()">Submit</button>

        <!--  Displays data retrieved from MongoDB -->
        <ul>
            <li v-for="data in dataFromMongo">
                {{ data }}
            </li>
        </ul>

    </div>
</template> 
```

Enter fullscreen mode Exit fullscreen mode

#### 视图脚本

脚本的主要部分是:

*   作为用户输入模式的数据对象
*   另一个存储 MongoDB 数据以供显示的数据对象
*   使用 Axios 的 POST 和 GET 方法
*   生命周期挂钩，以便应用程序在启动时加载 Mongo 数据

```
<script>
const axios = require('axios'); // Dependencies

export default {
    name: "API",
    data(){
        return { 
            // dataObject serves as schema for MongoDB data 
            dataObject: {
                userId: '',
                comments: ''
            },
            dataFromMongo: [],
        }
    },
    methods: {
        post(){
            axios.post('http://localhost:4000/api/post', {                
                dataObject: this.dataObject // key:userInputObject 
            })
            .then(function (response) {
                console.log(response);
            })
            .catch(function (error) {
                console.log(error);
            });
        },
        get(){
            axios.get('http://localhost:4000/api/get')
            .then((response) => {
                // Update data that will be rendered in component
                this.dataFromMongo = response.data; 
            })
            .catch(function (error) {
                console.log(error);
            });
        }
    },
    // this make it so that the application launch with MongoDB data
    mounted(){ 
        this.get();
    }
}
</script> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 后端-快递+猫鼬

后端的主要实现包括:

*   设置路线/端点
*   设置 HTTP 请求处理
*   设置与 MongoDB 的连接

我在 MongoDB 部分停留了一段时间，直到我发现了 [Traversy Media 的教程](https://www.youtube.com/watch?v=j55fHUJqtyw&t=1116s)。我不会为这个部分嵌入代码，因为我几乎只是遵循他的代码。相反，我会留下一个到他的 [Github 库](https://github.com/bradtraversy/microposts_fullstack_vue/tree/master/server)的链接。

* * *

[![](img/2547ceba9778a7379c5a60288e567c56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Yd1PoCvN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v22l88nthskig7jndqgt.jpg) 
照片由[埃拉·利夫兰德](https://unsplash.com/photos/63ZCI1oxIMU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

## 面临的主要挑战

我在拼凑拼图时遇到的一些挑战包括:

*   没有意识到前端和后端应该在不同的端口上运行
    *   这导致了试图让它们在同一个端口上工作的巨大时间浪费
*   缺乏对节点的认识和理解
    *   例如，我不知道 package.json 中的“scripts”属性，这使得我的工作流很麻烦
*   没有寻找现有解决方案的本能(即试图重新发明轮子)
    *   这导致了我最初尝试将 Vue Express 链接到 MongoDB 时的无效和低效
*   Javascript 基础薄弱
    *   事后看来，我觉得如果我的 Javascript 基础更强，我可能会更早获得更好的成功。尤其是因为整个堆栈都是基于 Javascript 的。
*   对异步代码理解不足
    *   在各种教程之后，很明显大部分代码是异步的。例如，让 MongoDB 在 Vue 中显示的解决方案是异步函数的自然派生。

## 结论

在浏览了各种教程后，我意识到目前为止我所有的代码都只是皮毛。事后看来，我觉得我花了太多时间做了这么少的事情。然而，虽然产量很小，但成就仍然是一个巨大的个人胜利！😁

干杯！

另外，如果有人想详细看看代码，你可以找到我的不太整洁的😅Github 上的 MEVN 模板[。](https://github.com/tangweejieleslie/mevn-template)

## 参考文献

特别感谢以下内容的创作者，尤其是帮助我将前端和后端拼图的不同部分拼凑起来的 [Traversy Media](https://www.youtube.com/channel/UC29ju8bIPH5as8OGnQzwJyA) ！

### 教程视频

*   [网络忍者观教程](https://www.youtube.com/watch?v=5LYrN_cAJoA&list=PL4cUxeGkcC9gQcYgjhBoeQH7wiAyZNrYa)
*   [Mosh 关于 Express 的教程](https://www.youtube.com/watch?v=pKd0Rpw7O48&t=775s)
*   [Mosh 关于节点的教程](https://www.youtube.com/watch?v=uVwtVBpw7RQ&list=PLTjRvDozrdlydy3uUBWZlLUTNpJSGGCEm)
*   [Traversy Media 的全栈开发教程](https://www.youtube.com/watch?v=j55fHUJqtyw&t=1116s)

### 文档和其他参考资料

*   [v-model.lazy 将数据同步从“输入时”更改为“更改时”](https://vuejs.org/v2/guide/forms.html#lazy)
*   [修复 Axios GET 请求不更新 Vue 组件。堆栈溢出](https://stackoverflow.com/questions/44454324/vue-js-the-view-doesnt-update-after-data-is-updated)
*   [Axios Git 库](https://github.com/axios/axios)

### 照片来源

*   由[岩田良治](https://unsplash.com/photos/5siQcvSxCP8?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/puzzle?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的封面照片
*   照片由 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的[埃拉·利夫兰德](https://unsplash.com/photos/63ZCI1oxIMU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄
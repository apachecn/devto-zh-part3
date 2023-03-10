# 第 015 集-从前端调用 Web API-ASP.NET 核心:从 0 到过度

> 原文：<https://dev.to/joaofbantunes/episode-015---calling-the-web-api-from-the-frontend---aspnet-core-from-0-to-overkill-2603>

总结一下用 Vue.js 构建的前端应用程序，在这一集里，我们让它与我们目前开发的组管理 Web API 一起工作。

对于演练，您可以查看下一个视频，但如果您喜欢快速阅读，请跳到书面综合。

[https://www.youtube.com/embed/ZF_uhOjMqsU](https://www.youtube.com/embed/ZF_uhOjMqsU)

整个系列的播放列表是[这里是](https://www.youtube.com/playlist?list=PLN0oN9Azm_MMAjk3nhRnmHdr1l0160Dhs)。
T3】

## [T1】简介](#intro)

在这一集里，我们将通过对组服务进行 HTTP 调用来连接前端和后端。为了进行 HTTP 调用，我们将使用 [axios](https://github.com/axios/axios) 。

## 服务客户端界面

我们将首先创建一个接口来表示我们的组服务客户端。像往常一样，在这些前端帖子中，这不是强制性的，但是我倾向于放弃我的 C#习惯🙂

首先，我们在`src`中创建一个名为`data`的新文件夹。在新文件夹中，我们创建了另一个名为`models`的文件夹，用来保存代表我们从服务中获取(和发送)的模型。

`data\models\group-model.ts`

```
export interface GroupModel {
    id: number;
    name: string;
    rowVersion: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

`GroupModel`与我们在其他应用领域看到的一样。让我们进入一些新的东西，服务客户端接口，`GroupsEndpoint`(就命名而言，这不是我最好的一集😛).

`data\groups-endpoint.ts`

```
import { GroupModel } from './models/group-model';

export interface GroupsEndpoint {
    getAll(): Promise<GroupModel[]>;
    getById(id: number): Promise<GroupModel>;
    add(group: GroupModel): Promise<GroupModel>;
    update(group: GroupModel): Promise<GroupModel>;
    remove(id: number): Promise<void>;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你记得我们在《T2》第 12 集中的`GroupsService`，这将是非常熟悉的。基本一样，只是改编成了 TypeScript。所有方法在执行 IO 操作时都返回`Promise`。

## 服务客户端实现

在实现服务客户端之前，我们需要将 axios 添加到项目中。当我使用 npm 时，我是通过运行`npm install --save axios`来完成的。

现在我们可以实现我们定义的接口了。

`data\groups-service.ts`

```
import { GroupsEndpoint } from './groups-endpoint';
import axios from 'axios';
import { GroupModel } from './models/group-model';

// TODO: handle eventual errors

export class GroupsService implements GroupsEndpoint {
    private readonly baseUrl: string = '/api/groups';

    public async getAll(): Promise<GroupModel[]> {
        const response = await axios.get(this.baseUrl);
        return response.data;
    }

    public async getById(id: number): Promise<GroupModel> {
        const response = await axios.get(`${this.baseUrl}/${id}`);
        return response.data;
    }

    public async add(group: GroupModel): Promise<GroupModel> {
        const response = await axios.post(this.baseUrl, group);
        return response.data;
    }

    public async update(group: GroupModel): Promise<GroupModel> {
        const response = await axios.put(`${this.baseUrl}/${group.id}`, group);
        return response.data;
    }

    public async remove(id: number): Promise<void> {
        const response = await axios.delete(`${this.baseUrl}/${id}`);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

没什么好解释的，但是让我们来看看。

首先，我们为请求定义了一个基本 url。注意，它以`/api`开始，但这不是我们在服务中定义的路线的一部分。关于开发服务器代理，我们稍后会看到原因。

关于方法实现，它们都是利用 axios 来发出请求，使用我们在创建 web API 时定义的各种 HTTP 方法。

我们还没有任何错误处理逻辑，我们最终应该实现它，但现在它不是很重要。

## 在 Vuex 动作中使用服务客户端

既然我们已经实现了服务客户机，我们需要在操作中使用它。我们可以只导入`GroupsService`并在动作实现中使用它，但是为了在将来的某个时候更容易测试动作，我创建了一个接收`GroupsEndpoint`实例的工厂。

`store/modules/groups/actions.ts`

```
import { ActionTree } from 'vuex';
import { GroupsState, Group } from './state';
import { RootState } from '@/store/state';
import { GroupsEndpoint } from '@/data/groups/groups-endpoint';

export const types = {
    LOAD_GROUPS: 'groups/loadGroups',
    ADD_GROUP: 'groups/add',
    UPDATE_GROUP: 'groups/update',
    REMOVE_GROUP: 'groups/remove'
};

export const makeActions = (groupsEndpoint: GroupsEndpoint): ActionTree<GroupsState, RootState> => {
    return {
        async loadGroups({ commit }): Promise<void> {
            const groups = await groupsEndpoint.getAll();
            commit('setGroups', groups);
        },
        async add({ commit }, group: Group): Promise<void> {
            const addedGroup = await groupsEndpoint.add(group);
            commit('add', addedGroup);
        },
        async update({ commit }, group: Group): Promise<void> {
            const updatedGroup = await groupsEndpoint.update(group);
            commit('update', updatedGroup);
        },
        async remove({ commit }, groupId: number): Promise<void> {
            await groupsEndpoint.remove(groupId);
            commit('remove', groupId);
        }
    };
}; 
```

Enter fullscreen mode Exit fullscreen mode

看一看，动作逻辑几乎是相同的，但是现在我们在将值传递给更新状态的突变之前发出 API 请求。这些动作也变成了`async`，因为它们也调用了异步服务方法。

注意，在`add`和`update`动作中，我们将来自服务的响应传递给突变，而不是动作负载。这是因为有一些属性是在服务器端初始化或更新的，我们需要保存这些信息。

## 配置开发服务器代理

如果您还记得我们开发 API 时，我们没有启用 CORS，因为我们将从同一个域中服务前端和 API。当所有这些都在一个服务器上一起工作时，我们将使用一个反向代理将请求映射到后端服务，其中路由以`/api`开始，其余的将到达前端。

为了在开发过程中实现类似的目标，我们可以配置 Vue 的开发服务器来代理 API 请求。

在项目的根目录中，在`src`之上，我们创建了一个名为`vue.config.js`的新文件，我们可以在其中设置 dev 服务器来代理 API 请求。

`vue.config.js`

```
module.exports = {
    devServer: {
      proxy: {
        '^/api': {
          target: 'http://localhost:5000',
          ws: true,
          changeOrigin: true,
          pathRewrite: {
            '^/api': ''
          },
        }
      }
    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

在这个配置文件中，我们说所有以`/api`开始的请求都到了`http://localhost:5000`，到目前为止，我们已经在这里托管了 dev 中的 API。还有一些选项，但最相关的是路径重写，它从路由中删除了`/api`，因为它不在组服务 API 中。对于这两个布尔值，`ws`意味着我们也代理 web 套接字请求，而`changeOrigin`将主机头的来源更改为目标 url。

你可以在这里和这里看到更多这些配置[。](https://cli.vuejs.org/config/#devserver-proxy)

## 其他

通过这篇文章，我们结束了这个系列的第一次前端冒险。在这 3 集中，我们构建了一个简单的 Vue.js 应用程序，它允许非常简单的组管理。我们利用了组件，一个商店，并使它与 web API 很好地配合。

在下一篇文章中，我们将回到 C#，但我们最终当然会回到前端。

帖子中的链接:

*   vista . js
*   [轴](https://github.com/axios/axios)
*   [检视 CLI - devServer.proxy](https://cli.vuejs.org/config/#devserver-proxy)
*   [http-代理-中间件](https://github.com/chimurai/http-proxy-middleware)

这个帖子的源码是[这里](https://github.com/AspNetCoreFromZeroToOverkill/WebFrontend/tree/episode015)。

感谢您的反馈！

谢谢你的来访，西阿兹！
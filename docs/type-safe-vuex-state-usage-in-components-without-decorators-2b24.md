# 类型安全的 Vuex 状态在组件中的使用(没有同样多的装饰器)

> 原文：<https://dev.to/sirtimbly/type-safe-vuex-state-usage-in-components-without-decorators-2b24>

如果你最近开始了一个 Vue 项目并使用了 Typescript，你可能会熟悉 [vue-class-component](https://github.com/vuejs/vue-class-component) 或者更广泛的替代物 [vue-property-decorator](https://github.com/kaorun343/vue-property-decorator) 。这些库提供了目前在 typescript 中受支持的[Decorator](https://github.com/tc39/proposal-decorators)，允许 Typescript 开发人员使用 ES6 类语法和声明性“Decorator”语法编写 vue 组件，声明性“Decorator”语法提取属性或方法并将其包装在特定的功能中，如在运行时普通类成员使装饰属性成为最终组件 props 对象的成员之前的`@Prop`。

```
 import { Component, Prop, Vue } from "vue-property-decorator";

@Component
export default class PhotoCard extends Vue {
  @Prop()
  public url!: string;

  public imageSrc: string = this.url || "";
  public isEditing: boolean = false;

  public editPhoto() {
    this.isEditing = true;
  }

  public cancel() {
    this.isEditing = false;
  }
} 
```

我不能肯定地说编写 Vue 组件的装饰者方式完全优于使用`Vue.component()`定义组件的经典方式，但是当你从组件的成员内部引用组件的其他部分时，它确实使语法更加灵活和类型安全。

现在，一旦你决定投入到编写 Vue 应用程序的“打字稿方式”中，你可能会发现自己在想如何让**一切都**打字安全。当你处理 Vuex 时，这个问题很快就会出现。因为，Vuex 是一个相当“间接”的编程范式。您必须通过查看 Vuex 存储本身的源代码来推断状态值的存在。您必须在组件代码中使用“神奇字符串”映射到突变和动作。🤮

你可能已经看过项目 [vuex-class](https://github.com/ktsn/vuex-class/) ，它使得使用 decorators 将组件的成员映射到你的商店成为可能，但是它并没有在商店和它在组件中的使用之间提供一个真正类型安全的耦合。还是“间接”。您必须通过查看 vuex 商店的源代码，在组件内部自己定义参数类型和返回类型。

```
 const authModule = namespace("auth");

  // ...

    @authModule.State
    public authenticated: boolean;

    @authModule.State
    public user: IUser;

    @authModule.Action
    public handleReset: (email: string) => Promise<any>;

    public handleResetPassword() {
      this.handleReset(this.user.email).then(() => {
        this.showResetInstruction = true;
      });
    } 
```

我最近发现了一个解决这个问题的更好的库，并重构了一个项目来使用它，所以，我将与你分享如何使用 [vuex-module-decorators](https://github.com/championswimmer/vuex-module-decorators) 来代替。

如果您正在编写模块将 vuex 存储分成多个代码文件，这种方法最有效。

当你编写和定义你的商店模块时，你将使用一个类装饰器，就像你编写一个 vue 类组件一样。

```
import { Module, VuexModule, Mutation, Action } from "vuex-module-decorators";
import Store from "../index";

@Module({
    dynamic: true,
    store: Store,
    name: "userAdmin",
    namespaced: true,
})
export default class UserAdminModule extends VuexModule {

    public userResults: GetUserDto[] = [];

    public rawResultSet: GetUserDto[] = [];

// ... 

    public get historyForUser(): GetAuditDto[] {
//        ...
    }

    @Mutation
    public loadResults({ data }: AxiosResponse<GetUserDto[]>) {
        if (data && data.length) {
            this.rawResultSet = data;
        }
    }

    @Action({ commit: "loadResults"})
    public async searchUsers(organization: string) {
        return await ApiClient.getUsers({ organization: organization || "1", sort: "name"});
    }
} 
```

该模块将在运行时动态插入到父存储中。类中的任何“getter”都将变成 vuex 模块上“getter”的成员。装潢师按照罐子上说的去做。在一个变异函数中，您可以使用友好的强类型语法`this.`来修改存储的属性。在 promise 从它正在装饰的异步函数中解析出来之后，`@Action`装饰器将自动调用指定的变异，减少了编写动作的样板代码，这些动作在完成后调用变异。

### 如此...如何在组件上以类型安全的方式访问这个模块？

像这样。您可以在组件中使用`getModule()`函数来获取模块的类型化实例。

```
import { getModule } from "vuex-module-decorators";
import UserAdmin from "@/store/modules/UserAdmin";

const userAdminState = getModule(UserAdmin);

@Component({
  components: {
    // ...
  },
})
export default class AdminUsersSearch extends Vue {
  public loading: boolean = false;

  public get filteredUsers(): GetUserDto[] {
    return userAdminState.filteredResults;
  }

  public mounted() {
    this.loading = true;
    userAdminState.searchUsers("1").then(x => this.loading = false);
  }

} 
```

注意:如果你没有创建一个“动态”模块，你需要将存储传递到 getModule 函数- [见文档](https://championswimmer.in/vuex-module-decorators/pages/advanced/dynamic.html)。

在您的 typescript 代码中，只要您键入`userAdminState.`，您就会立即获得 intellisense(在 VS 代码 obv 中。)向您展示了 state 模块中可用的状态、getters、突变和动作。如果您需要更改 vuex 模块的某些属性，您将在任何组件中使用该模块属性的任何地方得到编译器错误，这使得当您重构状态代码时更难破坏您的应用程序。
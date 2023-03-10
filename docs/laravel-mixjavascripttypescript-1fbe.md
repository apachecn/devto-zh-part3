# 拉勒维尔·mixにて途中でJavaScriptをTypeScriptに変える

> 原文：<https://dev.to/dala00/laravel-mixjavascripttypescript-1fbe>

在 Laravel Mix 中使用 Vue 的项目中，虽然已经用 JavaScript 写了组件，但是可能会想换成 TypeScript，但相对来说，这是以最小限度开始的。 这并不是说所有组件都必须重写为 TypeScript，这是推荐的。 先写下具体的步骤。

## 添加必要的文件

我觉得从 vue-cli 或 create-nuxt-app 的模板中拿过来会很快。

*   tsconfig.json
*   resources/assets/js/index.d.ts

## 将 mix.js 变更为 mix.ts

我想在 webpack.mix.js 上有以下设定。

```
mix.js('resources/assets/js/app.js', 'public/js') 
```

Enter fullscreen mode Exit fullscreen mode

把这个换成 ts。

```
mix.ts('resources/assets/js/app.ts', 'public/js') 
```

Enter fullscreen mode Exit fullscreen mode

在整个全部置换为 TypeScript 的开设的情况下，虽然有将文件夹名称也变更为 ts 的记述，但是这次是最小限度的，所以只变更方法名称和 app.js 的文件名。

## app.jsをapp.tsにする

可以说是 app.js 或者是入口点的文件，只有这个必须改为 TypeScript。 因此进行变更，如果构建时出现错误的话，首先在变量的宣言上像`variable: any`那样加上 any。 因为总之不能建立是没办法的，所以模具的设定决定以后再做，一个劲儿地加上 any。

## 对组件进行 TypeScript 化

总之试着改成一个 TypeScript 吧。 这个在其他地方有各种各样的解说，详细的话请您看看就好了，不过还是先大致写一下要做什么。

### 属于类

这大概是基本形。

```
<script lang="ts">
import { Vue, Component, Prop } from 'vue-property-decorator'

@Component
export default class YourComponentName extends Vue { 
```

Enter fullscreen mode Exit fullscreen mode

### 一些设定向记录器侧

使用的组件的设定等在收集器侧设定。

```
@Component({
  components: { SubComponent }
}) 
```

Enter fullscreen mode Exit fullscreen mode

### 属性也是编写者

```
@Prop({
  type: String
  required: true
})
name: string 
```

Enter fullscreen mode Exit fullscreen mode

### 不使用 data，全部作为类的属性

在
之前

```
data() {
  return {
    val1: false,
    val2: 'aaa'
    val3: null
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在
之后

```
val1 = false
val2 = 'aaa'
val3: YourType 
```

Enter fullscreen mode Exit fullscreen mode

### 方法是将普通方法转换为

不写在 methods 下面，直接作为类的方法

### computedはgetterに

```
get computedId() {
  return `ID:${this.id}`
} 
```

Enter fullscreen mode Exit fullscreen mode

## 其他

*   ts-loader 发生错误时，根据版本进行安装
*   如果@types 中有模具，则安装没有模具的外部模块等
*   没有类型的外部模块在 index.d.ts 中添加 declare (如果是 VSCode 等，我想应该可以看到处理方法)
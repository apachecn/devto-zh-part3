# 使用原子设计与 Nuxt.js 和有一个伟大的黑客时间。

> 原文：<https://dev.to/weezer924/using-atomic-design-with-nuxt-js-and-have-a-great-hacking-time-3lc5>

这是我第一次写英文帖子。我是一名在日本工作的网络应用程序开发人员。是的，我是中国人。无红利(ex-dividend)

## 我在这个项目中使用了什么

nuxt . js v 2.0
Express v 4.0
bootstrap-vue v 4.0

## 什么是原子设计

以防你不知道什么是原子设计。这里有一些关于它的好文件。

[http://atomicdesign.bradfrost.com/](http://atomicdesign.bradfrost.com/)
T3】https://www.indetail.co.jp/blog/10234/

TL；Dr:这是一个设计系统，向你展示如何创建你需要使用的 UI 组件。

## 如何分离你的文件夹

组件
|
| -原子
|-|-album . vue
|-|-back BTN . vue
|-分子
| - | - AlbumList.vue
| -有机体
|-|-album book . vue
|-page
|-|-album books . vue

## 原子样品

项目中最小的 UI 组件。
像这样。只是一个 nuxt-lint，div 和 img。

```
<template>
  <div>
    <nuxt-link to="/album">
      <img class="album-image" :src="album.coverUrl" />
    </nuxt-link>
    <div>
      {{ album.title }}
    </div>
  </div>
</template>

<script>
export default {
  props: {
    album: {
      type: Object,
      required: true,
      default: null
    }
  }
}
</script>

<style scoped>  <-- Use scoped to make the css only in this component
.album-image {
  height: 175px;
  width: 175px;
  object-fit: cover;
  border-radius: 10px;
  margin: 1px;
}
</style> 
```

## 分子

包含一些原子
在我的例子中，我把它做成一个原子
的数组

```
<template>
  <b-row>
    <b-col v-for="album in albums" :key="album.id">
      <Album :album="album"></Album>  <!-- use props in atom to receive the data -->
    </b-col>
  </b-row>
</template>

<script>
import Album from '~/components/atoms/Album.vue'  //  import the atom file

export default {
  components: {
    Album // This is the atom componet
  },

  props: {
    data: {
      type: Object,
      required: true,
      default: () => null
    }
  }
}
</script> 
```

## 生物体

结合不同的分子

```
<template>
  <section>
    <nav class="navbar navbar-light bg-light">
      <BackBtn path="/activity"></BackBtn>  <--- You can use atom aslo
    </nav>
    <div class="container-fluid">
      <AlbumList :data="albums"></AlbumList>  
      <CommentList :comments="comments"></CommentList>
    </div>
  </section>
</template>

<script>
import BackBtn from '~/components/atoms/BackBtn.vue'
import CommentList from '~/components/molecules/CommentList.vue'
import AlbumList from '~/components/molecules/AlbumList.vue'

export default { 
  components: {
    BackBtn, 
    CommentList, // different molecules
    AlbumList    // some molecules
  }
  data() {
    comments: [
       ... // 
    ]
  },
  computed: {
    albums() {
      return this.$store.state.albums // Using Vuex with Atomic Design is aslo a great idea, you can get your data in different ways.In your atom or your molecules.
    }
  },
}
</script> 
```

## 页面

最后是页面。确保你的页面使用更少的代码。如果你的页面比较大，你应该考虑把它分成像 atom 这样的小东西。

```
<template>
  <section>
    <AlbumBook></AlbumBook>
  </section>
</template>

<script>
import AlbumBook from '~/components/organisms/AlbumBook.vue'

export default {
  components: {
    AlbumBook
  }
}
</script> 
```

## 终于

分开你的文件，决定哪个部分应该更小。也许是你的项目中最具挑战性的事情。但是一旦你说到重点。你会从中受益的。
尤其是在你的早期发展阶段。

1.每次你想改变你的组件。你不需要把它们都换掉。就在一个文件里。导入该文件的任何其他页面都可以更改。

2.便于管理。需要的代码越少，bug 越少。

页（page 的缩写）这个帖子是我在博客里写的。(日本科技博客)
【https://qiita.com/jakushin/items/e8b19f6a2c7bcd037573 T2】
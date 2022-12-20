# Next.js + TypeScript 模板

> 原文：<https://dev.to/ryohlan/nextjs--typescript-template-109n>

【更新】1.1.0 版本出来了。删除下一条路线。基于[https://github.com/zeit/next.js#routing](https://github.com/zeit/next.js#routing)

我为 Next.js 和 TypeScript 创建一个模板。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [凉兰](https://github.com/ryohlan) / [下一个 ts 模板](https://github.com/ryohlan/next-ts-template)

### 使用参数化类型化路由的 Next.js 模板

<article class="markdown-body entry-content container-lg" itemprop="text">

# Next.js 9 出来了！！

这个版本包括使用文件系统的官方动态路由。

因此，您可能不需要这个存储库。看这个。

[https://nextjs.org/blog/next-9#dynamic-route-segments](https://nextjs.org/blog/next-9#dynamic-route-segments)

# Next.js TypeScript 项目模板

## 要求

节点> 10.12.0

## 这是什么？

这是 Next.js 的模板。模板包括以下内容:

*   以打字打的文件
*   参数化路由
*   定制服务器
*   样式组件
*   新页面的 cli

这个项目提供了一个创建新页面的命令行界面。例如，如果您想要添加一个名为 profile 的新页面，运行`npm run new:page profile`命令:

```
npm run new:page profile
create new page
  path: /next-ts-template/pages/profile/index.tsx
create new controller
  path: /next-ts-template/controllers/profile/index.tsx
create new layout
  path: /next-ts-template/layouts/profile/index.tsx
update pattern.json
  pattern:  { page: '/profile', pattern: '/profile' }
update createRoute.ts
  export const profile = () => ({
      as: `/profile`
      href: `/profile`
    })
```

该命令创建 3 个文件并更新 2 个文件。

### 页

cli 运行后，一个文件…

</article>

[View on GitHub](https://github.com/ryohlan/next-ts-template)

Next.js 是一个很棒的框架。但是它没有参数化的路由功能。我经常需要它。

因此，我创建了项目模板和模板生成器 CLI。

它的最大特点是自动提供参数化路由。

例如，如果我们需要' users/:user_id '路由，运行以下命令:

```
npm run new:page users/:user_id

create new page
  path: /{PROJECT_PATH}/next-ts-template/pages/users/show.tsx
create new controller
  path: /{PROJECT_PATH}/next-ts-template/controllers/users/show.tsx
create new layout
  path: /{PROJECT_PATH}/next-ts-template/layouts/users/show.tsx
create new routes
  pattern:  
    page: 'users/show',
    pattern: '/users/:user_id' } 
```

然后，我们可以访问'/users/100 '。并且查询参数类型在控制器文件中自动定义。

```
// /controllers/users/show
import React from 'react'
import { NextContext } from 'next'
import { AppInitialProps, AppProps } from '@src/app'
import Layout from '@layouts/users/show'

interface InitialProps {}

type Query = {
  user_id: string
}

const getInitialProps = async ({

}: NextContext<Query> & AppInitialProps): Promise<InitialProps> => {
  return {}
}

const Page = ({  }: AppProps & InitialProps) => <Layout />

Page.getInitialProps = getInitialProps

export default Page 
```

多个查询参数也是可以的。

```
npm run new:page users/:user_id/items/:item_id

create new page
  path: /{PROJECT_PATH}/next-ts-template/pages/users/items/show.tsx
create new controller
  path: /{PROJECT_PATH}/next-ts-template/controllers/users/items/show.tsx
create new layout
  path: /{PROJECT_PATH}/next-ts-template/layouts/users/items/show.tsx
create new routes
  pattern:  {
    page: 'users/items/show',
    pattern: '/users/:user_id/items/:item_id' } 
```

```
// users/items/show.tsx

...

type Query = {
  user_id: string,
  item_id: string
}

... 
```

您可以安全地为链接组件创建参数化的属性。

```
export const users_items_show = ({
  user_id,
  item_id
}: {
  user_id: string
  item_id: string
}) => ({
  as: `/users/${user_id}/items/${item_id}`,
  href: `/users/items/show?user_id=${user_id}&item_id=${item_id}`
}) 
```

同时`users_items_show`也被创建。

```
<Link {...users_items_show({ user_id: '2', item_id: '300' })} >
... 
```
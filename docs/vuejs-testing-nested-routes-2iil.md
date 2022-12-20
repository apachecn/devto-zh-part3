# VueJS 测试嵌套路由

> 原文：<https://dev.to/erenyatkin/vuejs-testing-nested-routes-2iil>

你好，我有一个应用程序，其中有一堆子路线，我想测试他们没有错误的渲染。

## router.spec.js

```
import { mount, createLocalVue } from "@vue/test-utils"
import App from "@/App.vue"
import VueRouter from "vue-router"
import Fullpage from "@/layouts/full-page/FullPage.vue"
import routes from "@/router/routes.js"

const localVue = createLocalVue()
localVue.use(VueRouter)

describe("App", () => {
  it("renders a child component via routing", () => {
    const router = new VueRouter({ routes })
    const wrapper = mount(App, { 
      localVue,
      router,
      stubs: ['vx-card']
    })

    router.push("/pages/login");
    expect(wrapper.find(Fullpage).exists()).toBe(true)
  })
}) 
```

`expect(wrapper.find(Fullpage).exist())`返回 false。

## routes.js

```
// Routes.js
export default [
  {
    // =============================================================================
    // MAIN LAYOUT ROUTES
    // =============================================================================
    path: '',
    component: () => import('../layouts/main/Main.vue'),
    children: [
      // =============================================================================
      // Theme Routes
      // =============================================================================
      {
        path: '/',
        name: 'Home',
        component: () => import('../views/system/Home.vue'),
        meta: { requiresAuth: true, abKey: 'getHome1' }, //! Prevent public access and check if user has access rights or not
      },
      {
        path: '/page2',
        name: 'page2',
        component: () => import('../views/Page2.vue'),
        meta: { requiresAuth: true, abKey: 'postCreate1' },
      },
      {
        path: '/admin',
        name: 'Admin',
        component: () => import('../views/admin/index.vue'),
        meta: { requiresAuth: true, abKey: 'adminMain' },
      },
    ],
  },
  // =============================================================================
  // FULL PAGE LAYOUTS
  // =============================================================================
  {
    path: '',
    component: () => import('@/layouts/full-page/FullPage.vue'),
    children: [
      // =============================================================================
      // PAGES
      // =============================================================================
      {
        path: '/pages/login',
        name: 'pageLogin',
        component: () => import('@/views/system/Login.vue'),
      },
      {
        path: '/pages/error-404',
        name: 'pageError404',
        component: () => import('@/views/system/Error404.vue'),
      },
    ],
  },
  // Redirect to 404 page, if no match found
  {
    path: '*',
    redirect: '/pages/error-404',
  },
]; 
```

有人能帮忙吗？
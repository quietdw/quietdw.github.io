---
title: Vue Router的基本使用
tags:
  - Vue
  - Vue Router
---

Vue Router 是 Vue.js 官方的路由管理器。现总结一下之前的使用方式。

<!-- more -->

# 使用 Vue Router

在安装 Vue 的时候，会有选项是否安装 Vue Router ，选择 `Y` 即可，如果当时选择了否，则：

```
npm i  vue-router
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)
```

# 动态路由

```javascript
const router = new Router({
  routes: [
    {
      name: 'topic',
      path: '/topic/:id&:loginname',
      components: {
        main: Article
      }
    }
  ]
})
```
形如上述形式的路径即为动态路由，冒号后是参数，可以跟多段参数，每个参数都被设置到this.$route.params中。

在 Vue Router 的官方文档中有说明：当参数变化时，组件会被复用，因此组件生命周期钩子不会被再次调用，即当路由一致，参数不一致时，页面不会跳转。

复用组建时，可以通过watch监听$route对象的变化来监测路由是否变化。路由钩子beforeRouterUpdate也可以行。

# 命名视图

有时候想同时（同级）展示多个视图，例如创建一个布局，有 sidebar（侧导航） 和 main（主内容） 两个视图，这个时候命名视图就派上用场了。你可以在界面中拥有多个单独命名的视图，而不是只有一个单独的出口。如果 router-view 没有设置名字，那么默认为 default。

```html
<router-view name="main"></router-view>
<router-view name="slidebar"></router-view>
```

```javascript
const router = new Router({
  routes: [
    {
      name: 'topic',
      path: '/topic/:id&:loginname',
      components: {
        main: Article,
        slidebar: Slidebar
      }
    }
  ]
})
```

# 路由元信息

```javascript
const router = new Router({
  routes: [
  {
    path: '/Create',
    component: () => import('@/pages/Create/template.vue'), //懒加载
    meta: {requiresAuth: true}
  }
  ]
})

router.beforeEach((to, from, next) => {
  if (to.matched.some(record => record.meta.requiresAuth)) {
    // this route requires auth, check if logged in
    // if not, redirect to login page.
    store.dispatch('checkLogin').then((isLogin) => {
      if (!isLogin) {
        next({
          path: '/login',
          query: { redirect: to.fullPath }
        })
      } else {
        next()
      }
    })
  } else {
    next() // 确保一定要调用 next()
  }
})
```

上面的例子 `/Create` 路由设置了 `meta: {requiresAuth: true}`，访问该页面需要认证。如未认证，则会跳转到登录页面。

# 编程式导航

除了使用 <router-link> 创建 a 标签来定义导航链接，我们还可以借助 router 的实例方法，通过编写代码来实现。

```
router.push(location)
```

想要导航到不同的 URL，则使用 router.push 方法。这个方法会向 history 栈添加一个新的记录，所以，当用户点击浏览器后退按钮时，则回到之前的 URL。

当你点击 <router-link> 时，这个方法会在内部调用，所以说，点击 <router-link :to="..."> 等同于调用 router.push(...)。

# History 模式

vue-router 默认 hash 模式(url有个符号#) —— 使用 URL 的 hash 来模拟一个完整的 URL，于是当 URL 改变时，页面不会重新加载。

如果不想要很丑的 hash，我们可以用路由的 history 模式，这种模式充分利用 history.pushState API 来完成 URL 跳转而无须重新加载页面。不过这种模式要玩好，还需要后台配置支持。因为我们的应用是个单页客户端应用，如果后台没有正确的配置，当用户在浏览器直接访问就会返回 404，这就不好看了。




> 引用
- [vue-router使用总结](https://juejin.im/entry/597ab13d5188253e0a62efcb)
- [router安装及使用](https://www.jianshu.com/p/ac873b45da36)
- [Vue Router](https://router.vuejs.org/zh/)

















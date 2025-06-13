---
title: vue-router梳理
categories: 技术总结
date: 2020-05-17 14:16:49
tags:
  - vue
  - 面试
---

用 Vue + Vue Router 创建单页面应用是非常简单的，其原理就是 将组件映射到路由，然后使用`<router-view>`告诉 Vue Router 在哪里渲染它们。具体

1. 定义组件
2. 定义路由
3. 创建路由实例
4. 将路由事件挂载到 vue 根实例

```js
// 0. 如果使用模块化机制编程，导入Vue和VueRouter，要调用 Vue.use(VueRouter)
// 1. 定义 (路由) 组件。
// 可以从其他文件 import 进来
const Foo = { template: "<div>foo</div>" };
const Bar = { template: "<div>bar</div>" };

// 2. 定义路由
// 每个路由应该映射一个组件。 其中"component" 可以是
// 通过 Vue.extend() 创建的组件构造器，
// 或者，只是一个组件配置对象。
const routes = [
  { path: "/foo", component: Foo },
  { path: "/bar", component: Bar },
];

// 3. 创建 router 实例，然后传 `routes` 配置
const router = new VueRouter({
  routes, // (缩写) 相当于 routes: routes
});

// 4. 创建和挂载根实例。
// 记得要通过 router 配置参数注入路由，
// 从而让整个应用都有路由功能
const app = new Vue({
  router,
}).$mount("#app");
// 现在，应用已经启动了！
```

通过注入路由器，可在任何组件内通过`this.$router`访问路由器，通过`this.$route`访问当前路由

## 动态路由匹配

把某种模式匹配到所有路由，全部映射到同个组件。在路由路径中使用**动态路径参数，以冒号`:`标记**。当匹配 到一个路由时，参数值会被 设置到`$route.params`，多段的路径参数，对应的值也会被设置到`$route.params`。例如：

| 模式                          | 匹配路径             | $route.params                     |
| ----------------------------- | -------------------- | --------------------------------- |
| /user/:username               | /user/jiang          | {username: "jiang"}               |
| /user/:username/post/:post_id | /user/jiang/post/123 | {username:"jiang", post_id:"123"} |

### 响应路由参数的变化

当使用路由参数时，参数的变化，例如`/user/foo`导航到`/user/jjiang`，不会引起 user 组件的重新渲染，它会复用原来的组件实例，故页面不会对路由变化做出响应，没有任何变化。这样意味着：**组件的生命周期钩子函数不会再被调用**

2 个解决方法

1. 简单的 watch`$route`对象

   ```js
   watch: {
     '$route': function(to, from) {
       //
     }
   }
   ```

2. 使用 组件内的 `beforeRouteUpdate`导航守卫，**在当前路由改变，且改组件被复用时调用**

   ```js
   beforeRouteUpdate(to,from,next) {
     //
   }
   ```

### 捕获 所有路由 / 404 路由

使用通配符（`*`）,可匹配任意路径。

**当使用通配符路由时，要确保路由的顺序，应该将其放在最后**。

当使用一个通配符时，`$route.params`内会自动添加一个名名为`pathMatch`参数

| 模式     | 匹配路径      | $route.params               |
| -------- | ------------- | --------------------------- |
| /user-\* | /user-admin   | {pathMatch: "admin"}        |
| \*       | /non-existing | {pathMatch: "non-existing"} |

### 匹配优先级

同一个路径可匹配多个路由时，匹配的优先级按照路由的定义顺序，**先定义的优先级高**

## 嵌套路由

一个被渲染的组件可以嵌套`<router-view>`,它是该组件匹配路由的嵌套路由的出口。要在嵌套的出口中渲染组件，使用`children`配置

```
/user/foo/profile                     /user/foo/posts
+------------------+                  +-----------------+
| User             |                  | User            |
| +--------------+ |                  | +-------------+ |
| | Profile      | |  +------------&gt;  | | Posts       | |
| |              | |                  | |             | |
| +--------------+ |                  | +-------------+ |
+------------------+                  +-----------------+
```

使用嵌套路由，表达上述关系

```js
let routes = [
  {
    path: "/user/:id",
    component: User,
    children: [
      {
        // 当 /user/:id/profile 匹配成功，
        // UserProfile 会被渲染在 User 的 <router-view> 中
        path: "profile",
        component: UserProfile,
      },
      {
        // 当 /user/:id/posts 匹配成功
        // UserPosts 会被渲染在 User 的 <router-view> 中
        path: "posts",
        component: UserPosts,
      },
    ],
  },
];
```

**基于上面配置，当访问`/user/foo`时，`User`的出口不会渲染任何东西，因为没有匹配到合适的子路由，如果需要渲染什么，需要提供一个`空的子路由`**

```js
let routes = [
  {
    path: "/user/:id",
    component: User,
    children: [
      // 当 /user/:id 匹配成功，
      // UserHome 会被渲染在 User 的 <router-view> 中
      {
        path: "",
        component: UserHome,
      },
      {
        path: "profile",
        component: UserProfile,
      },
      {
        path: "posts",
        component: UserPosts,
      },
    ],
  },
];
```

## 命名路由

通过一个名称来标识一个路由在使用时会更方便些，在创建 Router 实例的时候，在`routes`配置中给路由设置名称`name`

```
const router = new VueRouter({
  routes: [
    {
      path: '/user/:userId',
      name: 'user',
      component: User
    }
  ]
})



<router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>

router.push({ name: 'user', params: { userId: 123 }})
```

## 命名视图

想要同时/同级的展示多个视图，而不是嵌套展示，则需要命名视图，即在一个页面/组件中拥有多个单独命名的视图，而不是只用一个单独的出口。

一个视图需要一个组件渲染，因此，对于同个路由，多个视图就需要多个组件，使用`components`配置

```
<router-view class="view one"></router-view>
<router-view class="view two" name="a"></router-view>
<router-view class="view three" name="b"></router-view>

const router = new VueRouter({
  routes: [
    {
      path: '/',
      components: {
        default: Foo,
        a: Bar,
        b: Baz
      }
    }
  ]
})

```

## 重定向和别名

```js
routes: [
  {
    path: "/a",
    redirect: "/b",
  },
  {
    path: "/c",
    alias: "/d",
  },
];
```

**重定向：当用户访问`/a`，URL 会被替换成`/b`，然后匹配路由为`/b`,且为`/a`路由添加`beforeEach`守卫并 不会有任何效果**

**别名 ：当用户访问`/d`时，URL 会保持`/d`,但是路由匹配则为`/c`,就像用户访问`/c`**

## 路由组件传参

因为在组件中使用`$route`，会使组件同其对应路由形成高度耦合，从而使组件只能在特定的 URL 上使用。使用`props`配置，可以将组件与路由解耦。

```js
const User = {
  props: ["id"],
  template: "<div>User {{ id }}</div>",
};
const router = new VueRouter({
  routes: [
    { path: "/user/:id", component: User, props: true },

    // 对于包含命名视图的路由，你必须分别为每个命名视图添加 `props` 选项：
    {
      path: "/user/:id",
      components: { default: User, sidebar: Sidebar },
      props: { default: true, sidebar: false },
    },
  ],
});
```

**函数模式**，创建一个函数返回`props`，可以将参数转化成其他类型，可将静态值与路由的值结合

```js
const router = new VueRouter({
  routes: [
    {
      path: "/search",
      component: SearchUser,
      props: (route) => ({ query: route.query.q }),
    },
  ],
});
```

URL`/search?q=vue`，会将`{query:'vue'}`作为属性传递给`SearchUser`组件

## 编程式的导航

除了`<router-link>`创建 a 标签来定义导航链接，还可以借助`$router`的实例方法，通过编写代码来实现

- `$router.push(location,onComplete?, onAbort?)`,向 history 栈中添加一个新的记录
- `$router.replace(location,nComplete?, onAbort?)`,替换掉当前的 history 记录
- `$router.go(n)`,在 history 记录中向前，或后退多少步

**以上的方法是效仿`window.history` API 的，`window.history.pushState`,`window.history.replaceState`,`window.history.go`**

## H5 History 模式

`vue-router`默认 **hash 模式**，使用 URL 的 hash 来模拟一个完整的 URL，所以当 URL 改变时，页面不会重新加载

**history 模式** 是利用`history.pushState`API 来完成 URL 跳转，而无须重新加载页面。**但需要后台配置支持，因为应用是单页面的，如果后台没有给正确的配置，当用户在浏览器直接访问`http://oursite.com/user/id`,就会返回 404 .所以服务器需要增加一个覆盖所有情况的候选资源。在 URL 匹配不到任何静态资源，返回同一个`index.html`页面 ； 同时 在 Vue 的 路由配置中，添加覆盖所有的路由情况，并给返回 404 页面 **

## 导航守卫

”导航“表示路由正发生改变，导航守卫，通过跳转、取消的方式守卫导航。

**参数或查询的改变不会触发进入/离开的导航守卫**，2 种解决方法

1. 通过 watch `$route`对象来应对改变化
2. 使用组件内的导航守卫 `beforeRouteUpdate`

### 守卫方法接受的参数

守卫方法可接受三个参数”

- `to`， 即将要进入的目标 路由对象
- `from`，当前导航要离开 的路由 对象
- `next`， 一个函数，**一定要调用该函数来 resolve 该钩子**，执行效果由`next`函数的调用参数决定
  - `next()`，进行下一个守卫钩子。如果全部的钩子执行完，则导航的状态就是 confirmed（确认的）
  - `next(false)`，中断当前的导航，URL 地址重置到`from`路由对应的地址
  - `next(location)`， 跳转到一个不同的地址。当前导航中断，然后进入一个新的导航

**要确保调用`next`函数，否则钩子不会被 resolved**

### 全局的守卫

- `beforeEach`（to, from, next）：全局前置 守卫，**是异步解析执行**，此时导航在所有守卫*resolved*完之前一直处于 pending （等待中）。
- `beforeResolve`(to, from ,next)：全局解析守卫，在导航被 confirmed（确认）之前，**同时在所有组件内守卫，异步路由组件被解析之后**，解析守卫才被调用
- `afterEach(to, from)`：全局后置守卫，导航被 confirmed(确认) 之后，该守卫才调用

```js
const router = new VueRouter({ ... })
router.beforeEach((to,from,next) => {
  // ...
})
router.beforeResolve((to,from,next) => {
  // ...
})
router.afterEach((to,from) => {
  // ...
})
```

### 路由独享的守卫

- `beferEnter`：在单个路由上定义，匹配该路由时被调用

### 组件内守卫

- `beforeRouteEnter(to, from, next)`：在导航确认前被调用，因此新组件还没被创建，守卫**不能访问`this`**，但可以通过传一个回调给`next`来访问组件实例。在导航被确认的时候，执行回调，并把组件实例作为回调函数的参数

  ```js
  beforeRouteEnter(to,from, next) {
    // ....
    next(vm => {
      // 通过`vm`可访问该组件实例
    })
  }
  ```

- `beforeRouteUpdate(to, from, next)`： **在当前路由改变，且该组件被复用时调用**，可以访问组件内的`this`

- `beforeRouteLeave(to, from, next)`：导航离开该组件的对应路由时调用

### 导航解析流程

1. 导航被 触发
2. 在失活的组件调用离开守卫`beforeRouteLeave`
3. 调用全局的 `beforeEach`
4. 如果重用组件，在重用组件里调用`beforeRouteUpdate`
5. 在路由配置里调用`beforeEnter`
6. 解析异步路由组件
7. 在被激活的组件里调用`beforeRouteEnter`
8. 调用全局的`beforeResolve`
9. 导航被确认
10. 调用全局的`afterEnach`
11. 触发 DOM 更新
12. 用创建好的组件实例调用`beforeRouteEnter`守卫中传给`next`的回调函数

## 路由 元 信息

定义路由时配置`meta`字段，通过`$route.meta`来访问它

`$route.matched`，一个路由 匹配到的所有路由记录（`routes`配置中的每个路由对象称之为 路由记录）的数组

```js
router.beforeEach((to, from, next) => {
  if (to.matched.some((record) => record.meta.requiresAuth)) {
    // this route requires auth, check if logged in
    // if not, redirect to login page.
    if (!auth.loggedIn()) {
      next({
        path: "/login",
        query: { redirect: to.fullPath },
      });
    } else {
      next();
    }
  } else {
    next(); // 确保一定要调用 next()
  }
});
```

## 过渡效果

因为`<router-view>`是基本的动态组件，所以可以用`<transition>`组件包裹它，并添加过渡效果

```html
<transition>
  <router-view></router-view>
</transition>
```

### 单个路由的过渡

在各路由组件内使用`<transition>`并设置不同的`name`,可让每个路由组件都有各自的过渡效果

```js
const Foo = {
  template: `
    <transition name="slide">
      <div class="foo">...</div>
    </transition>
  `,
};

const Bar = {
  template: `
    <transition name="fade">
      <div class="bar">...</div>
    </transition>
  `,
};
```

## 动态设置过渡

基于当前路由与目标路由的变化关系，动态设置过渡效果

```vue
<transition :name="transitionName">
  <router-view></router-view>
</transition>

<script>
// ...
  watch: {
    '$route'(to,from) {
       	const toDepth = to.path.split('/').length
        const fromDepth = from.path.split('/').length
        this.transitionName = toDepth < fromDepth ? 'slide-right' : 'slide-left'
		}
  }
</script>
```

## 数据获取

进入路由后，需要从服务器获取数据，有 2 种选择

- 导航完之后获取：先完成导航,会进入新页面并马上渲染组件，然后在组件生命周期钩子函数`created`获取数据，期间展示 loading 状态

  ```js
  export default {
    data() {
      return {
        loading: false,
        post: null,
        error: null,
      };
    },
    created() {
      // 组件创建完后获取数据，
      // 此时 data 已经被 observed 了
      this.fetchData();
    },
    watch: {
      // 如果路由有变化，会再次执行该方法
      $route: "fetchData",
    },
    methods: {
      fetchData() {
        this.error = this.post = null;
        this.loading = true;
        // replace getPost with your data fetching util / API wrapper
        getPost(this.$route.params.id, (err, post) => {
          this.loading = false;
          if (err) {
            this.error = err.toString();
          } else {
            this.post = post;
          }
        });
      },
    },
  };
  ```

- 导航完成之前获取：在导航转入新的路由前获取数据,在组件的`beforeRouteEnter`或复用组件的`beforeRouteUpdate`守卫中获取数据，当获取数据成功后调用`next`函数。在为后面视图获取数据时，用户停留在当前界面，建议显示进度条，或其他提示。

  ```js
  export default {
    data() {
      return {
        post: null,
        error: null,
      };
    },
    beforeRouteEnter(to, from, next) {
      getPost(to.params.id, (err, post) => {
        next((vm) => vm.setData(err, post));
      });
    },
    // 路由改变前，组件就已经渲染完了
    // 逻辑稍稍不同
    beforeRouteUpdate(to, from, next) {
      this.post = null;
      getPost(to.params.id, (err, post) => {
        this.setData(err, post);
        next();
      });
    },
    methods: {
      setData(err, post) {
        if (err) {
          this.error = err.toString();
        } else {
          this.post = post;
        }
      },
    },
  };
  ```

  ## 路由懒加载

  将不同路由对应的组件分割成不同的代码块，并在被访问的时候才加载对应的组件。结合 Vue 的异步组件和 Webpack 的代码分割功能，可轻松实现路由组件的懒加载。

  用**命名 chunk**，可把某个路由下的所有组件都打包在同一个异步块（chunk）中。

  ```js
  const router = new VueRouter({
    routes: [
      {
        path: "/foo",
        component: Foo,
      },
      {
        path: "/about",
        component: () =>
          import(/* webpackChunkName: "about" */ "../views/About.vue"),
      },
    ],
  });
  ```

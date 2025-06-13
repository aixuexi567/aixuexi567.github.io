---
title: Vue调试工具(Vue-DevTools)使用指南
categories: 技术总结
date: 2019-11-21 18:47:25
tags: vue
---

### 安装

可查看 [vue-DevTools 文档](https://github.com/vuejs/vue-devtools#installation)

### 5 种常用调试方法

Vue DevTools 使得在运行时调试 Vue 应用程序变得很容易，以下会介绍在调试 Vue 中常用的 5 中方法

#### 1. 实时编辑组件 `data` 数据

当在使用 Vue 构建组件时，我们希望能实时去操作组件 data 数据。IDE 中更新 data 后，然后再去浏览器中查看结果比较麻烦，已经不适合了。

Vue-DevTools 允许 我们在运行时去 编辑组件 data

1. 打开浏览器调试工具，导航到 Vue tab
2. 在 tab 左列选择组件
3. 在 tab 右列 编辑 组件 `data`数据

![实时编辑组件 data ](/image/liveEditComponentData.gif)

默认情况下，`prop` 数据是不可编辑的，但我们可以在 `settings`tab 设置为可编辑状态

使用此功能，我们可以编辑所以 `data` 类型,切换 boolean 值，操作数组，我们还可以操作来自远端 API 的数据，只要我们把它加载到 vue 组件内，就可以对其实时操作了。

#### 2. 组件/ DOM 快速定位

- 组件 tab `Inspect DOM`,可快速定位该组件在 DOM 中位置
- 组件 tab `select in component in page`，可点击页面元素，快速定位其所属组件

![DOM/组件 快速定位](/image/inspectDom&Component.gif)

#### 3. 跟踪自定义事件

Vue Devtools 可以跟踪我们在 Vue 应用中使用的定义事件，查看事件是否正确触发，检查它们携带是数据（payload）  
Vue Devtools 只能跟踪自定义事件，即使用`this.$emit('myEvent')`触发的事件，不能跟踪绑定到组件上的事件比如`v-on:ckick`、`v-on:keyup`

![跟踪自定义事件](/image/trackCostomEvents.gif)

#### 监视 路由历史和数据

当我们使用 Vue 构建单页面应用时，希望能实时调试路由并跟踪整个应用的导航流。Vue DevTools 有一个 `Routing` tab，记录了路由变化时的历史和数据。  
`Routing` tab 有 2 个选项：

1. `Hishtory` 显示 路由的历史和其数据
2. `Routes` 显示 应用中所有的路由及其 options

![监视 路由历史和数据](/image/trackRoute.gif)

#### 调试 Vuex 行为，回到过去状态

`Vuex`是 Vue 的状态管理库，它运行我们以可预知的形式管理和更新应用的状态。Vue Devtools 可以让我们在运行时检查状态，以便调试。  
`Vuex` tab ，记录了应用状态，每当`Vuex`派发一个更新（dispatch a mutation）。它还允许我们在运行时 回退到以前 Vuex 的状态。

![Vuex behavior](/image/Vuex.gif)

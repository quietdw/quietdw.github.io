---
title: Vuex 小记
date: 2019-04-02 00:00:00
updated: 2019-04-02 00:00:00
tags:
  - Vuex
---

Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

<!-- more -->

简单的数据共享可以用 eventBus，复杂的可以选择用 Vuex。

每一个 Vuex 应用的核心就是 store（仓库）。“store”基本上就是一个容器，它包含着你的应用中大部分的状态 (state)。Vuex 和单纯的全局对象有以下两点不同：

Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。

你不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地提交 (commit) mutation。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。

# 核心概念

## State

state 里存储的是状态（数据），当需要生成多个状态时，可使用 mapState 辅助函数帮助我们生成计算属性，让你少按几次键。

```javascript
import { mapState } from 'vuex'
...mapState['state1','state2']
```

Vuex 通过 store 选项，提供了一种机制将状态从根组件“注入”到每一个子组件中（需调用 Vue.use(Vuex)）：

## Getter

类似计算属性，使用 Getter 获取 state 里的状态。mapGetters 辅助函数可将 store 中的 getter 映射到局部计算属性中。

## Mutation

提交 mutation 是改变 Vuex 状态的唯一方法。Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数。使用 `store.commit('increment')` 这种形式调用一个 mutation handler。注意 Mutation 必须是同步函数，异步的可以将其放在 Action 中。

在组件中使用 this.$store.commit('xxx') 提交 mutation，或者使用 mapMutations 辅助函数将组件中的 methods 映射为 store.commit 调用（需要在根节点注入 store）

## Action

Action 类似于 mutation，不同在于：

1. Action 提交的是 mutation，而不是直接变更状态。
2. Action 可以包含任意异步操作。

Action 通过 store.dispatch 方法触发

## Module

当应用变得非常复杂时，store 对象就有可能变得相当臃肿。Vuex 允许我们将 store 分割成模块（module）。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块。

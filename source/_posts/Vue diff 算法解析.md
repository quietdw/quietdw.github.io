---
title: Vue diff 算法解析
date: 2020-08-26 00:00:00
updated: 2020-08-26 00:00:00
tags:
  - Vue
  - diff
  - 算法
---

diff 算法是一种通过同层的树节点进行比较的高效算法，避免了对树进行逐层搜索遍历，所以时间复杂度只有 O(n)。在 Vue 虚拟 DOM 渲染成真实 DOM 的新旧 VNode 节点比较更新时，就用到了该算法。

<!-- more -->

### 当数据发生变化时，Vue 是怎么更新节点的？

Vue 根据真实 DOM 生成一颗 virtual DOM，当 virtual DOM 某个节点的`数据`改变后会生成一个新的 Vnode，然后 Vnode 和 oldVnode 作对比，发现有不一样的地方就直接修改在真实的 DOM 上，然后使 oldVnode 的值为 Vnode。

diff 的过程就是调用名为 patch 的函数，比较新旧节点，一边比较一边给真实的 DOM 打补丁。

当数据发生改变时，defineProperty set 方法会让调用 Dep.notify 通知所有订阅者 Watcher，订阅者就会调用 patch 给真实的 DOM 打补丁，更新相应的视图。

### diff 算法特点

1. 比较只会在同层级进行, 不会跨层级比较。
2. 在 diff 比较的过程中，循环从两边向中间收拢。

### diff 流程

1. 对新老 VNode 的开始和结束位置进行标记：oldStartIdx、oldEndIdx、newStartIdx、newEndIdx。

2. 标记好节点位置之后，就开始进入到的 while 循环处理中，这里是 diff 算法的核心流程，分情况进行了新老节点的比较并移动对应的 VNode 节点。while 循环的退出条件是直到老节点或者新节点的开始位置大于结束位置。循环过程中首先对新老 VNode 节点的头尾进行比较，寻找相同节点，当其中两个能匹配上那么真实 DOM 中的相应节点会移到 Vnode 相应的位置。

   1. 如果是 oldStartIdx 和 newEndIdx 匹配上了，那么将当前真实 DOM 移到 oldEndIdx 后面，匹配上的两个指针向中间移动；
   2. 如果是 oldEndIdx 和 newStartIdx 匹配上了，那么将当前真实 DOM 移到 oldStartIdx 前面，匹配上的两个指针向中间移动；
   3. 如果 oldStartIdx 和 newStartIdx ，真实 DOM 中的相应节点移到 Vnode 相应的位置，匹配上的两个指针向中间移动；
   4. 如果 oldEndIdx 和 newEndIdx 匹配上了，匹配上的两个指针向中间移动；
   5. 如果四种匹配没有一对是成功，那么会根据旧的 VNode 的 key 生成一张 hash 表，用 newStartIdx 的 key 与 hash 表做匹配，匹配成功就判断 newStartIdx 和匹配节点是否为 sameNode，如果是，就将真实 DOM 移到 oldStartIdx 前面，否则则说明当前索引下的新的 VNode 节点在旧的 VNode 队列中不存在，无法进行节点的复用，那么就只能调用 createElm 创建一个新的 DOM 节点放到当前 oldStartIdx 的位置。newStartIdx 指针向中间移动，被匹配 old 中的节点置为 null。

### 案例演练

假设以下所有节点都是有 key 的，且 key 为自身的值

- old:a-b-c-d
- new:a-c-b-e-f

```
oldStartIdx:a;
oldEndIdx:d;
newStartIdx:a
newEndIdx:f

oldStartIdx 和 newStartIdx 匹配，说明位置没变，此时 DOM 位置 a-b-c-d，此时指针：

oldStartIdx: b;
oldEndIdx: d;
newStartIdx: c
newEndIdx: f

一个都没有匹配到，然后 c 匹配到旧的 VNode，将 c 放到 oldStartIdx前，此时 DOM 位置 a-c-b-d，此时指针：

oldStartIdx: b;
oldEndIdx: d;
newStartIdx: b
newEndIdx: f

oldStartIdx 和 newStartIdx 匹配，说明位置没变，此时 DOM 位置 a-c-b-d，此时指针：

oldStartIdx: d;
oldEndIdx: d;
newStartIdx: e
newEndIdx: f

一个都没有匹配到，然后 e 未匹配到旧的 VNode，将创建的 e 放到 oldStartIdx前，被匹配 old 中的节点置为 null。此时 DOM 位置 a-c-b-e

遍历结束，说明旧的 VNode先遍历完。就将剩余的 VNode 节点根据自己的的index插入到真实DOM中去，此时 DOM 位置为：a-c-b-e-f

一次模拟完成。

这个匹配过程的结束有两个条件：

1. oldStartIdx > oldEndIdx 表示旧 VNode 先遍历完，那么就将多余的 VNode 根据index添加到DOM中去；
2. newStartIdx > newEndIdx 表示新 VNode 先遍历完，那么就在真实 DOM 中将区间为[oldStartIdx, oldEndIdx]的多余节点删掉
```

> 引用

- [Vue 的 diff 算法解析](https://www.infoq.cn/article/uDLCPKH4iQb0cR5wGY7f)
- [详解 Vue 的 diff 算法](https://juejin.im/post/6844903607913938951#heading-3)

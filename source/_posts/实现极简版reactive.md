---
title: 实现极简版reactive
tags:
  - Vue3
  - Proxy
---

Vue3.0 出来有一段是时间了，趁着有点空，跟着大佬一起学习一下新的响应式系统。

<!-- more -->

为了收集数据依赖、以及在数据变化时更新视图，Vue2 通过 defineProperty 劫持 set 和 get 属性访问描述符。

defineProperty 存在一些问题，最常见的问题就是无法监听对象以及数组动态添加的属性，即使 Vue2 重写了数组原型相关方法，但仍旧无法监听到`arr[1]=xxx`这种形式。

Vue3 使用了 ES6 新增的 Proxy 接口来替代 defineProperty。

Proxy 对象用于定义基本操作的自定义行为

```
const p = new Proxy(target, handler)
```

其中 handler 对象是一个容纳一批特定属性的占位符对象，它包含有 Proxy 的各个捕获器 trap，如 set、get`等

Map 对象保存键值对，并且能够记住键的原始插入顺序。任何值(对象或者原始值) 都可以作为一个键或一个值。

Set 对象允许你存储任何类型的唯一值，无论是原始值或者是对象引用。

- set 方法应该返回一个布尔值，在严格模式下，如果 set 方法返回 falsish(包括 undefined、false 等)，会抛出异常，这些细节比较麻烦，可以通过 Reflect 来处理

- 如果代理对象是数组，当调用 push、pop 等方法时，不仅会改变数组元素，也会改变 length 等属性，此时如果代理了 set，则会被触发多次。

```
let arr = [100,200,300]
let p = new Proxy(arr, {
  get(target, key) {
    return target[key]
  },
  set(target, key, value, receiver) {
    console.log('set value', key)
    target[key] = value
    return true
  }
})

p.push(400)
// set value 3 第一次data[3] = 400
// set value length 第二次 data.length = 4
```

60 行代码实现简易的 reactive

`index.js`

```
let activeEffect
let targetMap = new Map()

function reactive(obj){
    return new Proxy(obj, {
        get(target, key){
            track(target, key)
            return target[key]
        },
        set(target, key, value){
            target[key] = value
            trigger(target, key)
            return true
        }
    })
}

function track(target, key){
    let depMap = targetMap.get(target) // 获取 key 是 target 对象的值，感觉是对象的地址
    if(!depMap) { // 如果没有，就新增加一个，target 相关联的值初始化程一个Map
        targetMap.set(target, (depMap = new Map()))
    }
    let dep = depMap.get(key) // 获取 key 的相关联的数据
    if(!dep) { // 没有就初始化成一个 Set
        depMap.set(key, ( dep = new Set()))
    }

     // 给 key 添加 activeEffect，值变动了会执行trigger函数，执行该函数
    if(!dep.has(activeEffect)){
        dep.add(activeEffect)
    }
}

function watchEffect(cb){
    activeEffect = cb
    cb()
}

function trigger(target, key){

    let depMap = targetMap.get(target)
    if(!depMap) return
    let effects =  depMap.get(key)
    if(!effects) return

    effects.forEach((effect)=>{
        effect()
    })
}
```

`index.html`

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <script src="./index.js"></script>
    <script>
      let state = reactive({
        x: 100,
      })

      function render() {
        let msg = `render template with state.x = ${state.x}`
        console.log(msg)
      }

      watchEffect(render)

      setTimeout(() => {
        state.x = 200
      }, 1000)
    </script>
  </body>
</html>
```

> 引用

- [Proxy - MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)
- [Vue3 源码分析——数据侦测](https://juejin.im/post/5ec68bc8518825434980044e#heading-10)
- [vue-next](https://github.com/vuejs/vue-next)

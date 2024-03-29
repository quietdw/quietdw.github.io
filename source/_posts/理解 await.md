---
title: 理解 await
date: 2021-06-08 00:00:00
updated: 2021-06-08 00:00:00
tags:
  - await
---

输出以下代码执行结果

<!-- more -->

```javascript
function wait() {
  return new Promise(resolve => setTimeout(resolve, 10 * 1000))
}

async function main() {
  console.time()
  const x = wait()
  const y = wait()
  const z = wait()
  await x
  await y
  await z
  console.timeEnd()
}
main()
```

当执行到 `console.time();` 时，后面三句代码是同步的，因此会依次执行 `wait()` 里的代码，当执行到后面的 `await x` 时，10 秒后执行 `await y`，此时 x 、 y 也结束了。因同步任务也会耗时，故输出结果会比 10000ms 多一点。

..脑筋急转弯呢

> 引用

- [第 129 题：输出以下代码执行结果](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/251)

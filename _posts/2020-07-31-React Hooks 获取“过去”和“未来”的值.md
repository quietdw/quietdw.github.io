---
title: React Hooks 获取“过去”和“未来”的值
tags:
  - React
  - Hooks
---

这个标题看起来有点唬啊，其实主要是讲在使用 `useState` 时，因为使用异步函数而出现的一些问题。

<!-- more -->

先讲一下 `useState`

```
const [state, setState] = useState(initialState);
```

当组件被创建而不是重用时，该状态将被赋予初始值 initialState，而之后的重用过程中，不会被重复赋予初始值。

在实例中，可以通过 useState 等方式拥有局部状态。在重用的过程中，这些状态会得到保留。而如果无法重用，状态会被销毁。

需要明确的是，`state` 作为函数中的一个常量，就是普通的数据，并不存在诸如数据绑定这样的操作来驱使 DOM 发生更新。在调用 `setState` 后，React 将重新执行 `render` 函数。

举两个例子，例子中会使用 `useRef`，useRef 返回一个可变的 ref 对象，其 .current 属性被初始化为传入的参数（initialValue）。返回的 ref 对象在组件的整个生命周期内保持不变。

### 使用“未来”的值

```
function Example() {
  const [count, setCount] = useState(0);

  const currentCount = useRef(count);

  currentCount.current = count;

  const handleClick = () => {
    setTimeout(() => {
      setCount(currentCount.current + 1);
     // setCount(count + 1);
    }, 3000);
  };

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>setCount</button>
      <button onClick={handleClick}>Delay setCount</button>
    </div>
  );
}
```

上面这个例子，如果我们在 `handleClick` 中使用 `count`而不是 `currentCount.current`，当我们点击
`Delay setCount` 按钮，然后三秒内点两次 `setCount` 按钮，页面展示的值，先回变成 1、2，然后又会变成 1。

`count`，`handleClick` 都是 Example 函数作用域中的常量。定时器设置 3000ms 到期后的执行函数为第一次点击 `Delay setCount` 按钮时的函数，此时 `count` 的值是 0，和我们点击 `setCount` 两次时的 `count` 的值无关。

我们使用 useRef ，设置它的值不会重新触发 render 函数。此时 `currentCount.current` 的值是最新的 `count` 的值，因此页面展示的值分别是 1，2，3。

### 使用“过去”的值

```

理解上面的例子后，我们再理解下面的例子就方便多了。

function Example() {
    const [count, setCount] = useState(1);

    const prevCountRef = useRef(1);
    const prevCount = prevCountRef.current;
    prevCountRef.current = count;

    const handleClick = () => {
        setCount(prevCount + count);
    };

    return (
        <div>
            <p>{count}</p>
            <button onClick={handleClick}>SetCount</button>
        </div>
    );
}
```

页面首次渲染时，`count` 的值是初始值 1，`prevCountRef.current`的值是初始值 1

第一次点击 `SetCount` 按钮，重新执行函数，此时 `count` 的值是 2，`prevCount` 的值是上次存的值 1，然后`prevCountRef.current`更新成了 2。

页面展示的值 1，2，3，5……

### 总结

理解上面的例子，主要是理解每次 `SetCount` 后，页面都会重新渲染，每次渲染后的 count、handleClick 的值都是不相关的。

> 引用

- [React Hooks 最佳实践](https://juejin.im/post/5ec7372cf265da76de5cd0c9)
- [useRef](https://zh-hans.reactjs.org/docs/hooks-reference.html#useref)

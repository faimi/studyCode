# useAntdTable

结合了 Ant Design 的 Table 组件和 useRequest Hook，主要用于简化数据表格的使用，特别是数据的获取和分页

# useMount

用于替代只在组件初始渲染时运行的 useEffect（相当于 useEffect 带空依赖数组 []）

# useUpdateEffect

类似于 useEffect 的 Hook ， 它不会在组件初始渲染时执行副作用，而是在依赖项发生变化时才会触发。适用于需要跳过组件首次渲染时的副作用，仅在依赖项变化时执行。

# useBoolean

管理布尔状态的 Hook

```tsx
import React from "react";
import { useBoolean } from "ahooks";

const ToggleComponent = () => {
  const [state, { toggle, setTrue, setFalse }] = useBoolean(false);

  return (
    <div>
      <p>Current state: {state ? "True" : "False"}</p>
      <button onClick={toggle}>Toggle</button>
      <button onClick={setTrue}>Set True</button>
      <button onClick={setFalse}>Set False</button>
    </div>
  );
};

export default ToggleComponent;
```

# useToggle

在两个状态值之间切换（可以是布尔值，也可以是任意两个状态值）

# useSize

用于监听某个 DOM 元素大小变化的 Hook

# useSetState

适用于管理对象类型的状态，尤其是在状态是一个复杂对象时，可以方便地更新部分属性而不覆盖整个对象。

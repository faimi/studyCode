# useRequest

## mutate

mutate 的核心功能是 直接修改 useRequest 返回的 data 数据，而不触发实际的请求。它可以用来实现 乐观更新（Optimistic Updates），即在发起请求之前，先假设请求会成功，直接更新页面的状态，提升用户体验，等到请求结果回来后再进行最终确认。


mutate 的第一个参数：新的数据，用于更新 useRequest 返回的 data。
mutate 的第二个参数：布尔值，控制是否触发组件的重新渲染。
true：触发组件的重新渲染（默认值）。
false：不触发组件的重新渲染。
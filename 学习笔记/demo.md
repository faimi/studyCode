0、扩展脚本 yarn page：用于快速创建页面，执行 yarn page <页面名称>，会在 pages 下创建页面代码

1、路径在 app.config.ts 里定义， pages 数组的第一个元素 `pages/index/index` 就是小程序的初始页面。

2、在开发者工具中禁用缓存可以帮助你在调试过程中避免加载缓存中的旧数据，确保你看到的是最新的代码和资源。

3、测量一段代码执行时间的函数，标签是用来匹配计时块的开始和结束的。

```js
console.time("process");
console.timeEnd("process");
```

4、看 Network 的 Time 和 Waterfall

看 Network 才知道有两次图片请求，所以应该减少为一次

5、优化手段：
（1）在长宽变化结束后渲染
（2）两次图片请求变为一次，使用本地路径

6、样式覆盖问题

问题：封装时定义了 `border` ，引用时定义了 `border-bottom` ，但是渲染时，只出现了 `border-bottom` 的样式
原因：因为 `className` 被覆盖了，需要将 `className` 进行组合

```js
className={`${styles['image-borders']} ${className}`}
```

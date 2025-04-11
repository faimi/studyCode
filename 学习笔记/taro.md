#

app.config.js 配置路由

#

每次 commit 都要手动修改报错的地方，林哥说 taro 会自动修改，但是我这边不会

# Uniapp

Uniapp ：基于 Vue.js 进行开发。
Taro ：基于 React.js 进行开发，支持 JSX 语法。
Uniapp 和 Taro 则提供了一层抽象，开发者不必直接使用微信小程序的原生开发工具，而是使用 Vue 或 React 等现代前端框架进行开发。

# imageUrlLoaderOption

```
module.exports = {
  mini: {
    imageUrlLoaderOption: {
      limit: 2048, // 2 KB 以下的文件会转换为 Base64
      name: 'assets/images/[name].[hash:8].[ext]', // 输出的文件路径和命名格式
    },
  },
};
```

# PostCSS

## 自动添加浏览器前缀

```
display: flex;
```

开启自动前缀后，构建工具会自动添加前缀以支持旧版浏览器：

```
display: -webkit-box;
display: -webkit-flex;
display: -ms-flexbox;
display: flex;
```

## 开启 CSS Modules

在 Taro 或其他项目中启用 CSS Modules 后，可以将样式文件作为局部样式引入到组件中，并且生成的类名会自动带有哈希值，从而保证样式作用范围在该组件内。

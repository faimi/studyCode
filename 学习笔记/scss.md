#

希望 .fmy-test 类同时具有 .fmy-color 的样式，可以使用 CSS 的组合选择器。

```scss
.fmy-test {
  color: yellow;

  &,
  .fmy-color {
    background: red;
  }
}
```

#

为什么通过设置`flex`，不用设置宽高，内容占满一页？

因为`flex`纵向排列，`personal-info__content`设置了`flex: 1`，而`personal-info__footer`并没有。

> 若`personal-info__footer`设置了`flex: 1`，那么页面就会五五分

```scss
.personal-info {
  display: flex;
  flex-direction: column;

  &__content {
    flex: 1;
  }

  &__footer {
  }
}
```

##

设置`flex`和`flex-direction`，并将`min-height`设置为`100vh`，那么内容就占满了全页。通过对`common-address-footer`设置`fixed`，可以将其固定在底部。

```scss
.common-address {
  display: flex;
  flex-direction: column;
  min-height: 100vh;

  &-content {
  }

  &-footer {
    position: fixed;
    bottom: 0;
    left: 0;
  }
}
```

#

background 的渐变是可以设置范围的

从上到下的线性渐变，红色从顶部开始，黄色在 100px 位置出现，蓝色在 200px 位置结束。

```css
background: linear-gradient(to bottom, red 0px, yellow 100px, blue 200px);
```

从左到右的渐变，红色从 0% 到 50%，蓝色从 50% 到 100%。

```css
background: linear-gradient(to right, red 0%, red 50%, blue 50%, blue 100%);
```

#

为什么有时候:global 有用，有时候不用它才有用

maybe：index.scss 不需要，index.modules.scss 需要

```scss
// 没用
:global {
  .mj-dialog .nut-dialog-footer {
    flex-direction: row-reverse;
  }
}

// 有用
.mj-dialog .nut-dialog-footer {
  flex-direction: row-reverse;
}
```

##

我想的比较简单，直接使用固定的 px 值，栗子是通过获取某个值；她会加判断再去添加 class，我是直接添加

#

`gap` `row-gap` `column-gap`

设置容器内项目之间的间距，只控制项目与项目的间距，对项目与容器的间距不生效。

#

设置了 padding，就不要设置 margin

#

transform: rotate(0deg);

#

为什么去掉 div，会少一些 px

#

为什么使用 z-index 想要把某个 div 覆盖到另外一个 div 上，但是没用

#

flex 布局中的元素会在一行上排列，默认情况下按照从左到右的顺序排列。
当对第二个按钮（drawer-footer-button right）设置 margin-left: auto 时，它会把自己与前一个元素（第一个按钮）之间的空间尽可能拉大，导致第二个按钮移到容器的最右边。

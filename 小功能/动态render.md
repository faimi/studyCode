# 构建动态用户界面

Table 组件的“操作”列在不同场景下有不同的按钮需要 render

```tsx
const record = {
  actionList: ["edit", "delete", "view"],
};
const actionConf =
  record.actionList?.reduce((pre, cur) => {
    return {
      ...pre,
      [cur]: true,
    };
  }, {} as ActionConf) ?? {};
console.log(actionConf);
```

```tsx
{
    "edit": true,
    "delete": true,
    "view": true
}
```

再加上 dom 组件的展示方法

```tsx
const conditionShow = (dom: React.ReactNode, condition?: boolean) => {
  return condition ? dom : null;
};
```

最终形成如何根据条件展示 dom 组件的写法

```tsx
{
  conditionShow(
    <Button type="link" onClick={() => openDetail("detail")}>
      查看
    </Button>,
    actionConf.view_detail
  );
}
```

## keyof

在 TypeScript 中，keyof Type 获取某个类型 Type 所有键的联合类型。

```tsx
type ActionConf = {
  edit: boolean;
  delete: boolean;
  view: boolean;
};

const actionConf: ActionConf = {
  edit: true,
  delete: false,
  view: true,
};

const item = { id: "edit" };

const hasPermission = actionConf[item.id as keyof ActionConf];
console.log(hasPermission); // true
```

item.id 为 "edit"，它属于 keyof ActionConf，所以访问 actionConf["edit"] 返回 true。

```tsx
keyof ActionConf  // "edit" | "delete" | "view"
```

这表示 actionConf 只能有 edit、delete 或 view 这些键。

## in

in 关键字是 JavaScript 原生的运算符。它用于检查某个属性是否存在于对象中。在 TypeScript 中，你可以用 in 进行类型保护，确保访问对象属性时不会出错。

```tsx
const obj = { name: "Alice", age: 25 };

console.log("name" in obj); // true
console.log("age" in obj); // true
console.log("gender" in obj); // false
```

## as

as 是 TypeScript 的类型断言，告诉 TypeScript “相信我，这个值是特定类型”。

actionConf[item.id as keyof ActionConf] 强制 TypeScript 认为 item.id 是 ActionConf 的合法键，防止类型错误。

as 只在 编译时生效，不会改变 JavaScript 运行时的行为。

```tsx
let someValue: any = "Hello, TypeScript";

// 方式 1：使用 `as`
let strLength1: number = (someValue as string).length;

// 方式 2：使用 `<类型>` 语法（等价写法）
let strLength2: number = (<string>someValue).length;
```

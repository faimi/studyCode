#

```typescript
interface IProps extends UploadProps {
  icon?: ReactNode;
  label?: string;
  extra?: string | ReactNode;
  className?: string;
  loading?: boolean;
}
```

IProps 继承了 UploadProps 中定义的所有属性，同时也定义了 icon、label、extra、className 和 loading 等额外的属性。因此，IProps 包含了两个来源的属性。

# 高级类型

## Omit

Omit 是 TypeScript 提供的一个实用类型，用于从一个类型中排除指定的属性。

假设有一个接口 Person，其中包含 name, age, address 三个属性。现在我们希望创建一个新的类型，不包含 address 属性，可以使用 Omit 。

```
type PersonWithoutAddress = Omit<Person, 'address'>;
```

## Parameters

Parameters 用于提取函数类型的参数类型，并将其作为一个元组返回。

```js
function sum(a: number, b: number): number {
  return a + b;
}
```

```js
type SumParameters = Parameters<typeof sum>;
// SumParameters 的类型是 [number, number]
```

Parameters 类型只会提取函数的参数类型，不会提取返回类型。

#

```js
const isValid = !!applyConfig; // 如果 applyConfig 存在，isValid 为 true；否则为 false。
```

# 简化写法

## 短路运算符 (&&)

当条件为 true 时执行某个操作，常见于条件渲染，可以代替 if 语句。

```
if (isLoading) {
  return <Spinner />;
}
```

```
{isLoading && <Spinner />}
```

## 逻辑非 (!) 用于布尔值判断

```
if (isNotActive === false) {
  doSomething();
}
```

```
if (!isNotActive) {
  doSomething();
}
```

## 空值合并运算符 (??)

用于判断变量是否为 null 或 undefined，如果是，则返回后面的默认值。

```
const value = inputValue !== null && inputValue !== undefined ? inputValue : '默认值';
```

```
const value = inputValue ?? '默认值';
```

## 数组 map 和 filter 的简化

```
const items = [];
for (let i = 0; i < array.length; i++) {
  if (array[i].active) {
    items.push(array[i].name);
  }
}
```

```
const items = array.filter(item => item.active).map(item => item.name);
```

## !!

```
const value = 'hello';

// 使用 `!` 将其取反
console.log(!value);  // false

// 再次取反，使其成为布尔值
console.log(!!value); // true
```

0 false
null false
undefined false
NaN false
'' (空字符串) false
false false
true true
非空字符串 true
数字（非 0） true
对象 true
数组 true

## 模板字符串

```
const greeting = 'Hello, ' + name + '!';
```

```
const greeting = `Hello, ${name}!`;
```

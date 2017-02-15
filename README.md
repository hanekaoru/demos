## 数据类型和变量

#### 浮点数的比较

要比较两个浮点数是否相等，只能计算它们之差的绝对值，看是否小于某个阈值：

```js
Math.abs(1 / 3 - (1 - 2 / 3)) < 0.0000001; // true
```

#### null 和 undefined

大多数情况下，我们都应该用 ```null```，```undefined``` 仅仅在判断函数参数是否传递的情况下有用


#### 数组

实战，按顺序输出 A, B, C 和 D

```js
var arr = ["C", "B", "D", "A"]

console.log(` ${arr.sort().slice(0, 3).join(",")} 和 ${arr.sort().slice(-1)} `)
```

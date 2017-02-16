JavaScript全栈之路，参考 [JavaScript全栈](http://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000)

进阶之路，顺带记录一些平时遗漏的知识

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


## 对象

#### "." 操作符 和 "[ ]"

访问属性是通过 ```.``` 操作符完成的，但这要求属性名必须是一个有效的变量名。如果属性名包含特殊字符，就必须使用冒号("")包裹起来：

```js
var xiaohong = {
    name: "小红",
    "middle-school": "No.1 Middle School"
};
```

```xiaohong``` 的属性名 ```middle-school``` 不是一个有效的变量，就需要用 ```""``` 括起来，访问这个属性也无法使用 ```.``` 操作符，必须用 ```["xxx"]``` 来访问

也可以用 ```xiaohong["name"]``` 来访问 ```xiaohong``` 的 ```name``` 属性，不过 ```xiaohong.name``` 的写法更简洁，属性名尽量使用标准的变量名，这样就可以直接通过 ```object.prop``` 的形式访问一个属性了


#### in 和 hasOwnProperty()

使用 ```in``` 来判断一个属性是否存在，这个属性不一定是它本身的，它可能是继承得到的，要判断一个属性是否是其自身拥有的，而不是继承得到的，可以用 ```hasOwnProperty()``` 方法




## 循环

#### for ... in

请注意，```for ... in``` 对 ```Array``` 的循环得到的是 ```String``` 而不是 ```Number```

```js
var a = ["A", "B", "C"];
for (var i in a) {
    alert(i);  // "0", "1", "2" （注意：类型为 String）
    alert(a[i]);  // "A", "B", "C"
}
```


## Map

Map是一组键值对的结构，具有极快的查找速度

```js
var m = new Map([["Michael", 95], ["Bob", 75], ["Tracy", 85]]);
m.get("Michael"); // 95
```

Map 结构的方法有：```.set```，```.get```，```.has```，```.delete```

一个 ```key``` 只能对应一个 ```value```，所以多次对一个 ```key``` 放入 ```value```，后面的值会把前面的值覆盖掉

```js
var m = new Map();
m.set("Adam", 67);
m.set("Adam", 88);
m.get("Adam"); // 88
```


## Set

和 ```Map``` 类似，也是一组 ```key``` 的集合，但不存储 ```value```

```key``` 不能重复，所以在 ```Set``` 中，没有重复的 ```key```（重复元素在 ```Set``` 中自动被过滤）

```js
var s1 = new Set(); // 空 Set
var s2 = new Set([1, 2, 3]); // 含1, 2, 3
```


```Map``` 和 ```Set``` 数据结构有一个 ```has``` 方法，需要注意与 ```includes``` 区分

* ```Map``` 结构的 ```has``` 方法，是用来查找**键名**的，比如 ```Map.prototype.has(key)```、```WeakMap.prototype.has(key)```、```Reflect.has(target, propertyKey)```

* ```Set``` 结构的 ```has``` 方法，是用来查找**值**的，比如 ```Set.prototype.has(value)```、```WeakSet.prototype.has(value)```























官方文档：http://underscorejs.org/#collections

----

```underscore``` 提供了一套完善的函数式编程的接口，让我们更方便地在 ```JavaScript``` 中实现函数式编程

```js
"use strict"

_.map([1, 2, 3], (x) => x * x);  // [1, 4, 9]
```

重要的一点，是 ```underscore``` 的 ```map()``` 还可以作用于 ```Object```

```js
"use strict"

_.map( { a: 1, b: 2, c: 3 }, (v, k) => k + "=" + v);  // ["a=1", "b=2", "c=3"]
```


## map / filter

和 ```Array``` 的 ```map()``` 与 ```filter()``` 类似，但是可以作用于 ```Object```，当作用于 ```Object``` 时，传入的函数为 ```function (value, key)```，第一个参数接收 ```value```，第二个参数接收 ```key```

需要注意的是，对 ```Object``` 作 ```map()``` 操作的返回结果是 ```Array```（若是需要返回 ```Object```，请使用 ```_.mapObject()```）


## every / some

* 当集合的所有元素都满足条件的时候，```_.every()``` 函数返回 ```true```

* 当集合的至少一个元素满足条件的时候，```_.some()``` 函数返回 ```true```

```js
_.every([1, 4, 5, 6, 8, -2], (x) => x > 0);  // false

_some([1, 4, 5, 6, 8, -2], (x) => x > 0);  // true
```

```js
"use strict";

var obj = {
    name: "bob",
    school: "No.1 middle school",
    address: "xueyuan road"
};

// 判断 key 和 value 是否全部是小写：
var r1 = _.every(obj, function (value, key) {
    return (value + key) === (value + key).toLowerCase();
});

var r2 = _.some(obj, function (value, key) {
    return (value + key) === (value + key).toLowerCase();
});

alert("every key-value are lowercase: " + r1 + "\nsome key-value are lowercase: " + r2);
```



## max / min

返回集合中最大和最小的数

```js
var arr = [3, 5, 7, 9];

_.max(arr);  // 9

_.min(arr);  // 3


// 需要注意的是，空集合会返回 -Infinity 和 Infinity，所以要先判断集合不为空

_.max([]);  // -Infinity

_.min([]);  // Infinity
```

如果集合是 ```Object```，```max()``` 和 ```min()``` 只作用于 ```value```，会忽略掉 ```key```：

```js
_.max({ a: 1, b: 2, c: 3 });  // 3
```


## groupBy

```groupBy()``` 会把集合的元素按照 ```key``` 归类，```key``` 由传入的函数返回，一般用于数组分组

```js
"use strict"

var scores = [20, 81, 75, 40, 91, 59, 77, 66, 72, 88, 99];

var groups = _.groupBy(scores, function (x) {
    if (x < 60) {
        return "C";
    } else if (x < 80) {
        return "B";
    } else {
        return "A";
    }
})

// 结果:
// {
//   A: [81, 91, 88, 99],
//   B: [75, 77, 66, 72],
//   C: [20, 40, 59]
// }
```


## shuffle / sample

```shuffle()``` 用于洗牌算法随机打乱一个集合：

```js
"use strict"

_.shuffle([1, 2, 3, 4, 5, 6]);  // [3, 5, 4, 6, 2, 1]
```

```sample()``` 则是随机选择一个或多个元素：

```js
"use strict"

// 随机选取 1 个
_.sample([1, 2, 3, 4, 5, 6]);  // 2

// 随机选取 3 个
_.sample([1, 2, 3, 4, 5, 6], 3);  // [6, 1, 4]
```



## Arrays

#### first / last

```js
var arr = [2, 4, 6, 8];

_.first(arr);  // 2

_.last(arr);  // 8
```

#### flatten

flatten() 接收一个 Array，无论这个 Array 里面嵌套了多少个 Array，flatten() 最后都会把他们变成一个一维数组

```js
_.flatten([1, [2], [3, [[4], [5]]]]);  // [1, 2, 3, 4, 5]
```

#### zip / unzip

zip() 把两个或多个数组的所有元素按**索引对齐**，然后**按索引合并成新数组**。例如，你有一个 Array 保存了名字，另一个 Array 保存了分数，现在，要把名字和分数给对上，用 zip() 轻松实现：

```js
var names = ["Adam", "Lisa", "Bart"];

var scores = [85, 92, 59];

_.zip(names, scores);  // [["Adam", 85], ["Lisa", 92], ["Bart", 59]]
```

unzip() 则是反过来

```js
var namesAndScores = [["Adam", 85], ["Lisa", 92], ["Bart", 59]];

_.unzip(namesAndScores);  // [["Adam", "Lisa", "Bart"], [85, 92, 59]]
```


#### object

与 zip() 类似，不过是把名字和分数直接对应为 Object

```js
var names = ["Adam", "Lisa", "Bart"];

var scores = [85, 92, 59];

_.object(names, scores);  // {Adam: 85, Lisa: 92, Bart: 59}
```


#### range

range() 可以快速生成一个序列，不再需要使用 for 循环实现，如果只传入一个参数，默认是从 0 开始

```js
// 从 0 开始小于 10:
_.range(10); // [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

// 从 1 开始小于 11：
_.range(1, 11); // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

// 从 0 开始小于 30，步长 5:
_.range(0, 30, 5); // [0, 5, 10, 15, 20, 25]

// 从 0 开始大于 -10，步长 -1:
_.range(0, -10, -1); // [0, -1, -2, -3, -4, -5, -6, -7, -8, -9]
```







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

```Map``` 是一组键值对的结构，具有极快的查找速度

```js
var m = new Map([["Michael", 95], ["Bob", 75], ["Tracy", 85]]);

m.get("Michael"); // 95
```

```Map``` 结构的方法有：```.set```，```.get```，```.has```，```.delete```

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


## Map 和 Set

```Map``` 接受的参数为一个数组，数组内的成员是表示一个一个键值对的数组

```Set``` 函数可以接受一个数组（或类似数组的对象）作为参数，用来初始化

```Map``` 和 ```Set``` 数据结构有一个 ```has``` 方法，需要注意与 ```includes``` 区分

* ```Map``` 结构的 ```has``` 方法，是用来查找**键名**的，比如 ```Map.prototype.has(key)```、```WeakMap.prototype.has(key)```、```Reflect.has(target, propertyKey)```

* ```Set``` 结构的 ```has``` 方法，是用来查找**值**的，比如 ```Set.prototype.has(value)```、```WeakSet.prototype.has(value)```





## iterable

#### for ... of 循环和 for ... in 循环有何区别

当我们手动给 ```Array``` 对象添加了额外的属性后，```for ... in``` 循环将带来意想不到的意外效果：

```js
var a = ["A", "B", "C"];

a.name = "Hello";

for (var x in a) {
    alert(x); // "0", "1", "2", "name"
}
```

```for ... in``` 循环将把 ```name``` 包括在内，但 ```Array``` 的 ```length``` 属性却不包括在内

```for ... of``` 循环则完全修复了这些问题，它只循环集合本身的元素：

```js
var a = ["A", "B", "C"];

a.name = "Hello";

for (var x of a) {
    alert(x); // "A", "B", "C"
}
```

然而，更好的方式是直接使用 ```iterable``` 内置的 ```forEach``` 方法，它接收一个函数，每次迭代就自动回调该函数。以 ```Array``` 为例：

```js
var a = ["A", "B", "C"];

a.forEach(function (element, index, array) {
    // element 当前元素的值
    // index 当前索引
    // array 指向 Array 对象本身
    console.log(element);
})
```

```Set``` 与 ```Array``` 类似，但 ```Set``` 没有索引，因此回调函数的前两个参数都是元素本身：

```js
var s = new Set(["A", "B", "C"]);

s.forEach(function (element, sameElement, set) {
    console.log(element);
})
```

```Map``` 的回调函数参数依次为 ```value```、```key``` 和 ```map``` 本身：

```js
var m = new Map([[1, "x"], [2, "y"], [3, "z"]]);

m.forEach(function (value, key, map) {
    console.log(value);
})
```

如果对某些参数不感兴趣，可以忽略它们。例如只需要获得 ```Array``` 的 ```element```：

```js
var a = ["A", "B", "C"];

a.forEach(function (element) {
    console.log(element);
})
```


## 函数

#### rest 参数

```rest``` 参数只能写在最后，前面用 ```...``` 标识

```js
// 接收任意个数参数，返回它们的和

// 方法一
function sum (...rest) {

    var s = 0;

    rest.forEach(function (x) {
        s += x;
    })

    return s;
    
}

// 方法二
function sum (...rest) {

    var s = 0;
    
    for (var i of rest) {
        s += i;
    }

    return s;

}
```


## 方法

#### 装饰器

想统计一下代码一共调用了多少次 ```parseInt()```

```js
var count = 0;
var oldParseInt = parseInt;  // 保存原函数

window.parseInt = function () {
    count++;
    return oldParseInt.call(null, arguments);  // 调用原函数
}

parseInt("10");
parseInt("20");
parseInt("30");
count;  // 3
```

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

```Map``` 是一组键值对的结构，具有极快的查找速度

```js
var m = new Map([["Michael", 95], ["Bob", 75], ["Tracy", 85]]);

m.get("Michael"); // 95
```

```Map``` 结构的方法有：```.set```，```.get```，```.has```，```.delete```

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


## Map 和 Set

```Map``` 接受的参数为一个数组，数组内的成员是表示一个一个键值对的数组

```Set``` 函数可以接受一个数组（或类似数组的对象）作为参数，用来初始化

```Map``` 和 ```Set``` 数据结构有一个 ```has``` 方法，需要注意与 ```includes``` 区分

* ```Map``` 结构的 ```has``` 方法，是用来查找**键名**的，比如 ```Map.prototype.has(key)```、```WeakMap.prototype.has(key)```、```Reflect.has(target, propertyKey)```

* ```Set``` 结构的 ```has``` 方法，是用来查找**值**的，比如 ```Set.prototype.has(value)```、```WeakSet.prototype.has(value)```





## iterable

#### for ... of 循环和 for ... in 循环有何区别

当我们手动给 ```Array``` 对象添加了额外的属性后，```for ... in``` 循环将带来意想不到的意外效果：

```js
var a = ["A", "B", "C"];

a.name = "Hello";

for (var x in a) {
    alert(x); // "0", "1", "2", "name"
}
```

```for ... in``` 循环将把 ```name``` 包括在内，但 ```Array``` 的 ```length``` 属性却不包括在内

```for ... of``` 循环则完全修复了这些问题，它只循环集合本身的元素：

```js
var a = ["A", "B", "C"];

a.name = "Hello";

for (var x of a) {
    alert(x); // "A", "B", "C"
}
```

然而，更好的方式是直接使用 ```iterable``` 内置的 ```forEach``` 方法，它接收一个函数，每次迭代就自动回调该函数。以 ```Array``` 为例：

```js
var a = ["A", "B", "C"];

a.forEach(function (element, index, array) {
    // element 当前元素的值
    // index 当前索引
    // array 指向 Array 对象本身
    console.log(element);
})
```

```Set``` 与 ```Array``` 类似，但 ```Set``` 没有索引，因此回调函数的前两个参数都是元素本身：

```js
var s = new Set(["A", "B", "C"]);

s.forEach(function (element, sameElement, set) {
    console.log(element);
})
```

```Map``` 的回调函数参数依次为 ```value```、```key``` 和 ```map``` 本身：

```js
var m = new Map([[1, "x"], [2, "y"], [3, "z"]]);

m.forEach(function (value, key, map) {
    console.log(value);
})
```

如果对某些参数不感兴趣，可以忽略它们。例如只需要获得 ```Array``` 的 ```element```：

```js
var a = ["A", "B", "C"];

a.forEach(function (element) {
    console.log(element);
})
```


## 函数

#### rest 参数

```rest``` 参数只能写在最后，前面用 ```...``` 标识

```js
// 接收任意个数参数，返回它们的和

// 方法一
function sum (...rest) {

    var s = 0;

    rest.forEach(function (x) {
        s += x;
    })

    return s;
    
}

// 方法二
function sum (...rest) {

    var s = 0;
    
    for (var i of rest) {
        s += i;
    }

    return s;

}
```


## 方法

#### 装饰器

想统计一下代码一共调用了多少次 ```parseInt()```

```js
var count = 0;
var oldParseInt = parseInt;  // 保存原函数

window.parseInt = function () {
    count++;
    return oldParseInt.call(null, arguments);  // 调用原函数
}

parseInt("10");
parseInt("20");
parseInt("30");
count;  // 3
```





## 高阶函数

```JavaScript``` 的函数其实都指向某个变量，既然变量可以指向函数，函数的参数能接收变量，那么一个函数就可以接收另一个函数作为参数，这种函数就称之为高阶函数

```js
function add(x, y, f) {
    return f(x) + f(y);
}

add(-5, -10, Math.abs);  // 15
```

#### reduce

```Array``` 的 ```reduce()``` 把一个函数作用在这个 ```Array``` 的 ```[x1, x2, x3...]``` 上，这个函数必须接收两个参数，```reduce()``` 把结果继续和序列的下一个元素做累积计算，其效果就是：

```js
[x1, x2, x3, x4].reduce(f) = f(f(f(x1, x2), x3), x4)
```

利用 ```reduce``` 求和：

```js
var arr = [1, 3, 5, 7, 9];

arr.reduce(function (x, y) {
    return x + y;
});  // 25
```

利用 ```map``` 和 ```reduce``` 实现 ```parseInt()``` 函数：

```js
var str = "664651513136"

str.split().map(function(x){return x - 0}).reduce(function(){return x * 10 + y});

```

利用 ```map``` 实现将数组的首字母大写，其余小写：

```js
var arr = ["adam", "LISA", "barT", "APpLe", "banNEr"];

arr.map(function(str){return str[0].toUpperCase() + str.substring(1, str.length).toLowerCase()})

```



#### filter

和 map() 类似，简单的来说就是，map 是处理，而 filter 则是过滤

```js
var arr = ["A", "B", "C"];
var r = arr.filter(function (element, index, self) {
    console.log(element); // 依次打印"A", "B", "C"
    console.log(index); // 依次打印0, 1, 2
    console.log(self); // self就是变量arr
    return true;
});
```

数组去重

```js
var arr = ["apple", "strawberry", "banana", "pear", "apple", "orange", "orange", "strawberry"];

arr.filter(function (element, index, self) {
    return self.indexOf(element) === index;
})

```


#### sort

对于两个元素 ```x``` 和 ```y```，如果认为 ```x < y```，则返回 ```-1```，如果认为 ```x == y```，则返回 ```0```，如果认为 ```x > y```，则返回 ```1```

对字符串排序，是按照ASCII的大小比较的，只要我们能定义出忽略大小写的比较算法就可以：

```js
var arr = ["Google", "apple", "Microsoft"];
arr.sort(function (s1, s2) {
    x1 = s1.toUpperCase();
    x2 = s2.toUpperCase();
    if (x1 < x2) {
        return -1;
    }
    if (x1 > x2) {
        return 1;
    }
    return 0;
}); // ["apple", "Google", "Microsoft"] 
```


## 闭包

#### 函数作为返回值

在前面提到过利用 reduce 来对 Array 进行求和，但是如果不需要立即求和，而是在后面的代码中，根据需要在计算，这个时候就需要返回求和的函数

```js
function lazySum (arr) {
    var sum = function () {
        return arr.reduce(function (x, y) {
            return x + y;
        })
    }

    return sum;
    
}

var f = lazySum([1, 2, 3, 4, 5]);  // function sum()

f();  // 15
```

需要注意的是，当我们调用 ```lazySum()``` 的时候，每次调用都会返回一个新的函数，即使传入相同的参数，而且每次调用的结果互不影响




## 箭头函数

```js
// 两个参数
(x, y) => x * x + y * k

// 无参数
() => 3.14

// 可变参数
(x, y, ...rest) => {
    var i, sum = x + y;
    for (i = 0; i < rest.length; i++) {
        sum += rest[i];
    }
    return sum;
}

// 如果是表达式，记得要用括号括起来，否则会报错
x => ({ foo: x })
```

几点注意事项：

* 函数体内的 ```this``` 对象就是定义时所在的对象，而不是使用时所在对象

* 不可以当作构造函数使用，也就是不能用 ```new``` 命令实例化一个对象，否则会抛出一个错误

* 不可以使用 ```arguments``` 对象，该对象在函数体内不存在，如果要用的话，可以用 ```rest``` 参数代替

* 不可以使用 ```yield``` 命令，箭头函数不能用作 ```Generator``` 函数

再次强调：

```js
let a = {
    foo: 1,
    bar: () => console.log(this.foo)
}

a.bar();  // undefined
```

以上代码中的 this 并不是指向 ```a``` 这个对象，对象 ```a``` 并不能构成一个作用域，所以在往上到达全局作用域，```this``` 就指向全局作用域

```js
let a = {
  foo: 1,
  bar: function () {
      return () => this.foo + 1
  }
}

a.bar()()  // 2
```

## generator

```generator``` 跟函数很像

```js
function* foo (x) {
    yield x + 1;
    yield x + 2;
    return x + 3;
}
```

不同之处在于，除了 return，还可以使用 yield 返回多次

调用 generator 对象有两个方法，一是不断地调用 generator 对象的 next() 方法

```next()``` 方法会执行 ```generator``` 的代码，然后每次遇到 ```yield x;``` 就返回一个对象 ```{value: x, done: true/false}```，然后"暂停"。返回的 ```value``` 就是 ```yield``` 的返回值，```done``` 表示这个 ```generator``` 是否已经执行结束了。如果 ```done``` 为 ```true```，则 ```value``` 就是 ```return``` 的返回值。

当执行到 ```done``` 为 ```true``` 时，这个 ```generator``` 对象就已经全部执行完毕，不要再继续调用 ```next()``` 了

第二个方法是直接用 ```for ... of``` 循环迭代 ```generator``` 对象，这种方式不需要我们自己判断 ```done```

另外一个巨大的好处，可以使异步回调代码变成"同步"代码：

```js
try {
    r1 = yield ajax('url-1', data1);
    r2 = yield ajax('url-2', data2);
    r3 = yield ajax('url-3', data3);
    success(r3);
}
catch (err) {
    handle(err);
}
```


## 面向对象

#### 原型继承

```js
function Student(props) {
    this.name = props.name || "Unnamed";
}

Student.prototype.hello = function () {
    alert("Hello, " + this.name + "!");
}

function PrimaryStudent(props) {
    Student.call(this, props);
    this.grade = props.grade || 1;
}
```

现在 PrimaryStudent 创建的对象的原型是

```js
new PrimaryStudent() ----> PrimaryStudent.prototype ----> Object.prototype ----> null
```

必须把原型链修改为：

```js
new PrimaryStudent() ----> PrimaryStudent.prototype ----> Student.prototype ----> Object.prototype ----> null
```

这个时候就可以用一个空函数 F() 来用于桥接：

```js
// PrimaryStudent 构造函数
function PrimaryStudent(props) {
    Student.call(this, props);
    this.grade = props.grade || 1;
}

// 空函数 F
function F() { }

// 把 F 的原型指向 Student.prototype
F.prototype = Student.prototype;

// 把 PrimaryStudent 的原型指向一个新的 F 对象，F 对象的原型正好指向 Student.prototype
PrimaryStudent.prototype = new F();

// 把 PrimaryStudent 原型的构造函数修复为 PrimaryStudent
PrimaryStudent.prototype.constructor = PrimaryStudent;

// 继续在 PrimaryStudent 原型（就是 new F() 对象）上定义方法
PrimaryStudent.prototype.getGrade = function () {
    return this.grade;
};

// 创建 xiaoming
var xiaoming = new PrimaryStudent({
    name: "小明",
    grade: 2
});

xiaoming.name; // "小明"
xiaoming.grade; // 2

// 验证原型
xiaoming.__proto__ === PrimaryStudent.prototype; // true
xiaoming.__proto__.__proto__ === Student.prototype; // true

// 验证继承关系
xiaoming instanceof PrimaryStudent; // true
xiaoming instanceof Student; // true
```

可以把这个模式封装起来，留以复用：

```js
function inherits(Child, Perent) {
    var F = function () { };
    F.prototype = Perent.prototype;
    Child.prototype = new F();
    Child.prototype.constructor = Child;
}

function Student(props) {
    this.name = props.name || 'Unnamed';
}

Student.prototype.hello = function () {
    alert('Hello, ' + this.name + '!');
}

function PrimaryStudent(props) {
    Student.call(this, props);
    this.grade = props.grade || 1;
}

// 实现原型继承链
inherits(PrimaryStudent, Student);

// 绑定其他方法到PrimaryStudent原型
PrimaryStudent.prototype.getGrade = function () {
    return this.grade;
};
```

简单总结：

* 定义新的构造函数，并在内部用 ```call()``` 调用希望"继承"的构造函数，并绑定 ```this```

* 借助中间函数 ```F``` 实现原型链继承（```inherits```）

* 继续在新的构造函数的原型上定义新方法



#### class 继承

```js
class Student {
    constructor (name) {
        this.name = name;
    }

    hello () {
        console.log("Hello " + this.name + "!");
    }
}

var xiaoming = new Student("小明");
xiaoming.hello()

class PrimaryStudent extends Student {
    constructor (name, grade) {
        super(name);
        this.grade = grade;
    }

    myGarder () {
        console.log("I am at grade " + this.grade);
    }
}
```

练习：

```js
"use strict";

class Animal {
    constructor(name) {
        this.name = name;
    }
}

class Cat extends Animal {
    constructor (name) {
        super(name);
        this.name = name;
    }

    say () {
        return `Hello, ${this.name}!`
    }
}
```



## promise

Promise 的主要作用就是用于封装异步操作，以便根据异步操作是否成功来进行后续的操作

后续操作是通过 ```then()``` 和 ```catch()``` 来申明的，但是如何触发，以及应该触发成功还是失败呢？这就靠 ```resolve``` 和 ```reject``` 这两个函数了，在 Promise 对象产生时，它们是为作封装的函数的参数的，如下

```js
new Promise(function(resolve, reject) {
    // ...
});
```

先看一个例子，生成一个 0 ~ 2 之间的随机数，如果小于 1，则等待一段时间后返回成功，否则返回失败

```js
function test (resolve, reject) {
    var timeOut = Math.random() * 2;
    console.log("set timeOut to: " + timeOut + " seconds.")

    setTimeout(function () {

        // 如果执行成功
        if (timeOut < 1) {
            console.log("call resolve()...");
            resolve("200 OK")

        // 如果执行失败
        } else {
            console.log("call reject()...")
            reject("timeOut in " + timeOut + " seconds.")
        }
    }, timeOut * 1000)
}

// 变量 p1 是一个 Promise 对象，它负责执行 test 函数
var p1 = new Promise(test);

// 如果成功，执行这个函数：
var p2 = p1.then(function (result) {
    console.log("成功：" + result);
})

// 当 test 函数执行失败时，我们告诉 Promise 对象
var p3 = p1.catch(function (reason) {
    console.log("失败：" + reason)
})
```

Peomise 对象可以串联，所以上述代码可以简化为：

```js
new Promise(test).then(function (result) {
    console.log("成功：" + result);
}).catch(function (reason) {
    console.log("失败：" + reason);
});
```

Promise 最大的好处是在异步执行的流程中，把执行代码和处理结果的代码清晰地分离了：

![img](http://www.liaoxuefeng.com/files/attachments/001436512391628944d5da9a5654a35b0ace38246f30b9c000/l)

比如，有若干个异步任务，需要先做任务1，如果成功后再做任务2，任何任务失败则不再继续并执行错误处理函数：

```js
job1.then(job2).then(job3).catch(haneleError)
```

用 Promise 如何简化异步处理：

```js
// ajax 函数将返回 Promise 对象
function ajax(method, url, data) {
    var request = new XMLHttpRequest();
    return new Promise(function (resolve, reject) {
        request.onreadystatechange = function () {
            if (request.readyState === 4) {
                if (request.status === 200) {
                    resolve(request.responseText);
                } else {
                    reject(request.status);
                }
            }
        };
        request.open(method, url);
        request.send(data);
    });
}

var p = ajax("GET", url);

p.then(function (text) { // 如果 AJAX 成功，获得响应内容
    console.log("成功")
}).catch(function (status) { // 如果 AJAX 失败，获得响应代码
    console.log("失败")
});
```



#### Promise.all()

```js
var p1 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 500, "P1");
});

var p2 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 600, "P2");
});

// 同时执行p1和p2，并在它们都完成后执行then:
Promise.all([p1, p2]).then(function (results) {
    console.log(results); // 获得一个Array: ["P1", "P2"]
});
```



#### Promise.race()

```js
var p1 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 500, "P1");
});

var p2 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 600, "P2");
});

Promise.race([p1, p2]).then(function (result) {
    console.log(result); // "P1" / p2仍在继续执行，但执行结果将被丢弃
});
```

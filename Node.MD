## 开篇

```Node``` 的交互模式和直接运行 ```.js``` 文件有什么区别？

直接输入 ```node``` 进入交互模式，相当于启动了 ```node``` 解释器，但是等待你一行一行地输入源代码，每输入一行就执行一行。

直接运行 ```node hello.js``` 文件相当于启动了 ```node``` 解释器，然后一次性把 ```hello.js``` 文件的源代码给执行了，你是没有机会以交互的方式输入源代码的。


#### CommonJS 规范

```js
// index.js
"use strict"

function getName (name) {
    console.log(`hello ${name}!`)
}

module.exports = getName;


// main.js
"use strict"

var getName = require("./index");

getName("luna");  // hello luna!
```

这种模块加载机制被称为 CommonJS 规范。在这个规范下，每个 ```.js``` 文件都是一个模块，它们内部各自使用的变量名和函数名都互不冲突

一个模块想要对外暴露变量（函数也是变量，输出的变量可以是任意对象、函数、数组等等），可以用 ```module.exports = variable```，一个模块要引用其他模块暴露的变量，用 ```var ref = require('module_name')``` 就拿到了引用模块的变量


#### 深入模块原理

其实要实现 "模块" 这个功能，并不需要语法层面的支持，实现 "模块" 功能的奥妙就在于 ```JavaScript``` 是一种函数式编程语言，它支持**闭包**

```Node.js``` 加载了 ```index.js``` 后，它可以把代码包装一下，变成这样执行：

```js
(function () {
    
    // ...

})()
```

所以，```Node``` 利用 ```JavaScript``` 的函数式编程的特性，轻而易举地实现了模块的隔离

至于模块的输出，也就是所谓的 ```module.exports```

```Node``` 可以先准备一个对象 ```module```

```js
// 准备module对象:

var module = {
    id: "index",
    exports: {}
};

var load = function (module) {

    // 读取的 index.js 代码:
    function getName (name) {
        console.log(`hello ${name}!`)
    }

    module.exports = getName;

    // index.js 代码结束
    return module.exports;
    
};

var exported = load(module);

// 保存 module:
save(module, exported);
```

可见，变量 ```module``` 是 ```Node``` 在加载 ```js``` 文件前准备的一个变量，并将其传入加载函数，我们在 ```index.js``` 中可以直接使用变量 ```module``` 原因就在于它实际上是函数的一个参数：

```js
module.exports = getName;
```

通过把参数 ```module``` 传递给 ```load()```，```index.js``` 就顺利地把一个变量传递给了 ```Node``` 执行环境，```Node``` 会把 ```module``` 变量保存到某个地方

由于 ```Node``` 保存了所有导入的 ```module```，当我们用 ```require()``` 获取 ```module``` 时，```Node``` 找到对应的 ```module```，把这个 ```module``` 的 ```exports``` 变量返回，这样，另一个模块就顺利拿到了模块的输出

```js
var getName = require("./index");
```


#### module.exports 和 exports

很多时候，你会看到，在 ```Node``` 环境中，有两种方法可以在一个模块中输出变量：

方法一：对 ```module.exports``` 赋值

```js
module.exports = {
    hello: hello,
    index: index
}
```

方法二：直接使用 ```exports```

```js
exports.hello = hello;
```

针对方法二，但是你不可以直接对 ```exports``` 赋值：

```js
exports = {
    hello: hello,
    index: index
}
```

代码虽然可以执行，但是模块并没有输出任何变量

在深入了解之前我们可以先看一下官方 ```API``` 的一个经典的例子，把 ```require``` 方法简化成了下面这个函数：

```js
function require(...) {

    // ...  

    function (module, exports) {

        // Your module code here  
        exports = some_func;        // re-assigns exports, exports is no longer  
        
        // a shortcut, and nothing is exported.  
        module.exports = some_func; // makes your module export 0  

    } (module, module.exports);

    return module;

}  
```

通过观察传入进去的参数，我们可以看到 ```exports = module.exports```，所以说，一开始这两个东西是指向同一个对象实例的（实际上是同一个变量，并且初始化为空对象 ```{}``` ）

![img](http://img.my.csdn.net/uploads/201503/05/1425555505_4273.png)

如果直接添加一个方法，比如 ```exports.fn = func```，那么由于 ```exports``` 指向 ```module.exports```，由于复制引用的关系，相当于 ```module.exports``` 也添加了同样的方法，如下图所示：

![img](http://img.my.csdn.net/uploads/201503/05/1425555505_2279.png)

```js
// 在 exports 上添加了一个方法，module.exports 也添加了同样的方法，因为它们指向了同样的对象实例  
exports.fn = function () { 
    console.log("hello world!");
}

module.exports.fn(); // hello world! 
```

如果给 ```exports``` 直接赋值，比如这里 ```exports = some_func```，那么 ```exports``` 的指向就变了，但最后导出的是 ```module```，因此，```exports``` 指向的方法或者今后在 ```exports``` 上添加的方法都不会影响到 ```module.exports```，因此不会被导出

![img](http://img.my.csdn.net/uploads/201503/05/1425555506_5192.png)

引用官方 API 的一句话：

> As a guideline, if the relationship between exports and module.exports seems like magic to you, ignore exports and only use module.exports.

所以还是建议尽量使用 ```module.exports```

在有的时候，比如我们要输出的是一个函数或者数组，那么，只能给 ```module.exports``` 赋值：

```js
module.exports = function () { return "foo"; };
```

给 ```exports``` 赋值是无效的，因为赋值后，```module.exports``` 仍然是空对象 ```{}``` （因为最后导出的是 ```module```）

小结：

* 如果要输出一个键值对象 ```{}```，可以利用 ```exports``` 这个已存在的空对象 ```{}```，并继续在上面添加新的键值

* 如果要输出一个函数或数组，必须直接对 ```module.exports``` 对象赋值

所以我们可以得出结论：直接对 ```module.exports``` 赋值，可以应对任何情况：

```js
module.exports = {
    foo: function () { return "foo"; }
};
```

或者：

```js
module.exports = function () { return "foo"; };
```

总之，尽量使用 ```module.exports``` 来输出模块变量

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




## 基本模块

#### global

在浏览器中的顶层对象是 ```window``` 对象，而在 ```Node.js``` 环境中，也有唯一的全局对象，就是 ```global```

可以据此来判断 ```javaScript``` 的执行环境：

```js
if (typeof (window) == "undefined") {
    console.log("node.js");
} else {
    console.log("browser");
}
```

#### process

代表当前 ```Node.js``` 的进程

如果我们想在下一次事件响应中执行代码，可以调用 ```process.nextTick()```：

```js
process.nextTick(function () {
    console.log("nextTick callback!");
})

console.log("nextTick was set!");

// nextTick was set!
// nextTick callback!
```

这说明传入 ```process.nextTick()``` 的函数不会立刻执行，而是要等到下一次事件循环

如果我们响应 ```exit``` 事件，就可以在程序即将退出的的时候执行某个回调函数：

```js
process.on("exit", function (code) {
    console.log("about to exit with code: " + code);
})
```


## fs

#### 异步读取文件

```js
"use strict"

var fs = require("fs");

fs.readFile("index.txt", "utf-8", function (err, data) {
    if (err) {
        console.log(err);
    } else {
        console.log(data);
    }
})
```

正常读取的时候，```err``` 参数为 ```null```，```data``` 为读取到的的 ```String```

当发生错误的时候，```err``` 参数代表一个错误对象，```data``` 为 ```undefined```

下面是一个读取图片的例子：

```js
"use strict"

var fs = require("fs");

fs.readFile("index.png", function (err, data) {
    if (err) {
        console.log(err);
    } else {
        console.log(data);
        console.log(data.length + " bytes")
    }
})
```

当读取二进制文件的时候，不传入文件编码的情况下，回调函数的 ```data``` 参数将返回一个 ```Buffer``` 对象（就是一个**包含零个或任意字节的数组**，注意和 ```Array``` 不同）

```Buffer``` 对象可以和 ```String``` 作转换：

```js
var text = data.toString("utf-8");

console.log(text)
```

或者把一个 ```String``` 转换为 ```Buffer```

```js
var buf = new Buffer(text, "urf-8");

console.log(buf)
```


#### 同步读取文件

与异步函数相比，多了一个 ```Sync``` 后缀，并且不接收回调函数，函数直接返回结果

```js
"use strict"

var fs = require("fs");

var data = fs.readFileSync("index.txt", "utf-8");

console.log(data)
```

如果同步文件发生错误，则需要用 ```try ... catch``` 捕获：

```js
try {
    var data = fs.readFileSync("index,tex", data);

    console.log(data)
} catch (err) {
    // ...
}
```


#### 写入文件

```js
"use strict"

var fs = require("fs");

var data = "hello world"

fs.writeFile("index.txt", data, function (err) {
    if (err) {
        console.log(err)
    } else {
        console.log("ok")
    }
})
```

如果传入的数据是 ```String```，默认按照 ```utf-8``` 编码写入文件，如果传入的参数是 ```Buffer```，则写入二进制文件

但是有一点需要注意，```writeFile()``` 写入的时候，会先把文件清空，这个时候可以使用 ```fs.appendFile()```

```js
"use strict"

var fs = require("fs");

var data = "hello world"

fs.appendFile("index.txt", data, function (err) {
    if (err) {
        console.log(err)
    } else {
        console.log("ok")
    }
})
```

与 ```readFile()``` 类似，```writeFile()``` 也有一个同步方法，为 ```writeFileSync()```


#### stat

如果我们要获取文件大小，创建时间等信息，可以使用 ```fs.stat()```，它返回一个 ```Stat``` 对象，包含文件或目录的详细信息：

```js
"use strict"

var fs = require("fs");

fs.stat("index.txt", function (err, stat) {
    if (err) {
        console.log(err)
    } else {

        // 是否是文件
        console.log("isFile: " + stat.isFile());

        // 是否是目录
        console.log("isDirectory: " + stat.isDirectory())

        if (stat.isFile()) {
            
            // 文件大小
            console.log("Size: " + stat.size);

            // 创建时间，Date 对象
            console.log("birth time: +" + stat.birthtime);

            // 修改时间，Date 对象
            console.log("modified time: " + stat.modisied);
        }
    }
})
```

```stat()``` 也有一个对应的同步方法，```statSync()```


#### 异步 和 同步

在 ```fs``` 模块中，提供同步方法是为了方便使用，由于 node 环境执行的是服务器端代码，所以，绝大部分需要在服务器运行期反复执行业务逻辑的代码，必须使用**异步代码**，否则，同步代码在执行时期，服务器将停止响应，因为 ```JavaScript``` 只有一个执行线程

服务器启动时如果需要读取配置文件，或者结束时需要写入到状态文件时，可以使用同步代码，因为这些代码只在启动和结束时执行一次，不影响服务器正常运行时的异步执行







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

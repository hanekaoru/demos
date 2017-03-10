## koa 

koa 是 express 的下一代基于 Node.js 的 Web 框架

#### Express

Express 是第一代最流行的 Web 框架，它对 Node.js 进行了封装：

```js
var express = require("express");

var app = express();

app.get("/", function (req, res) {
    res.send("hello world");
})

app.listen(3000, function () {
    console.log("Example app listening on port 3000");
})
```

虽然 Express 的 API 很简单，但是它是基于 ES5 的语法，要实现异步代码，只有使用 **回调**，如果异步嵌套层次过多：

待续...

## koa 

```koa``` 是 ```express``` 的下一代基于 ```Node.js``` 的 ```Web``` 框架

#### Express

```Express``` 是第一代最流行的 ```Web``` 框架，它对 ```Node.js``` 进行了封装：

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

虽然 ```Express``` 的 ```API``` 很简单，但是它是基于 ```ES5``` 的语法，要实现异步代码，只有使用 **回调**，如果异步嵌套层次过多：

```js
app.get("/test", function (req, res) {
    fs.readFile("/file1", function (err, data) {
        if (err) {
            res.status(500).send("read file1 error");
        }
        fs.readFile("/file2", function (err, data) {
            if (err) {
                res.status(500).send("read file2 error");
            }
            res.type("text/plain");
            res.send(data);
        });
    });
});
```

虽然可以使用例如 ```async``` 这样的库来组织代码，但是利用回调来处理异步还是太麻烦


## koa 1.0

和 ```Express``` 相比，```koa 1.0``` 使用 ```generator``` 来实现异步：

```js
var koa = require("koa");
var app = koa();

app.use("./test", function * () {
    
    yield doReadFile1();

    var data = yield doReadFile2();

    this.body = data;
    
})

app.listen(3000)
```

利用 ```generator``` 实现异步是简单了不少，但是 ```generator``` 的本意并不是异步（```promise``` 才是）

```ES7```（草案，未来标准） 中引入了新的关键字 ```async``` 和 ```await```，可以轻松把 ```function``` 变为异步模式：

```js
async function () {
    var data = await fs.read("./test")
}
```


## koa 2

和 ```koa 1``` 相比，```koa 2``` 完全使用 ```Promise``` 并配合 ```async``` 来实现异步

```js
app.use(async (ctx, next) => {

    await next();

    var data = await doReadFile();

    ctx.response.type = "text/plain";

    ctx.response.body = data;
    
});
```

#### 创建 koa2 工程

```js
// 导入 koa 和 koa 1.x 不同，在 koa2 中，我们导入的是一个 class，因此用大写的 Koa 来表示
const Koa = require("koa");

// 创建一个 Koa 对象表示 web app 本身
const app = new Koa();

// 对于任何请求，app 将调用该异步函数处理请求
app.use(async (ctx, next) => {

    await next();

    // 设置 response 的 Content-Type
    ctx.response.type = "text/html";

    // 设置 response 的内容
    ctx.response.body = "<h1>hello world</h1>"

})

// 箭头 3000 端口
app.listen(3000);

console.log("app started at port 3000")
```

* 关于 ```ctx``` 参数，是由 ```koa``` 传入的封装了 ```request``` 和 ```response``` 的变量，我们可以通过它们来访问 ```request``` 和 ```response```

* 关于 ```next``` 参数，是 ```koa``` 传入的将要处理的下一个异步函数

* ```node``` 版本过低的话，会执行报错

上面的异步函数中，我们首先调用 ```await next()``` 来处理下一个异步函数，然后，设置 ```response``` 的 ```Content-Type``` 内容

由 ```async``` 标记的函数称为 异步函数，在异步函数中，可以用 ```await``` 调用另一个 异步函数（这两个关键字将在 ```ES7``` 中引入）



#### koa 中间件（middleware）

在来看看 ```koa``` 执行的核心逻辑代码：

```js
app.use(async (ctx, next) => {

    await next();

    ctx.response.type = "text/html";

    ctx.response.body = "<h1></h1>"
    
})
```

每收到一个 ```http``` 请求，```koa``` 就会调用通过 ```app.use()``` 注册的 ```async``` 函数，并传入 ```ctx``` 和 ```next``` 参数，我们可以对 ```ctx``` 操作，并设置返回内容



#### 为什么需要调用 await next()

原因在于 ```koa``` 把很多 ```async``` 函数组成一个处理链，每个 ```async``` 函数都可以做一些自己的事情，然后使用 ```await next()``` 来调用下一个 ```async``` 函数

例如，可以使用下面 3 个 ```middleware``` 组成处理链，依次打印日志，记录处理事件，输出 ```html```：

```js
app.use(async (ctx, next) => {
    console.log(`${ctx.request.method} ${ctx.request.url}`);
    await next();
})

app.use(async (ctx, next) => {
    const start = new Date().getTime();
    await next();
    const ms = new Date().getTime() - start;
    console.log(`Time: ${ms}ms`)
})

app.use(async (ctx, next) => {
    await next();
    ctx.response.type = "text/html"
    ctx.response.body = "<h1></h1>"
})
```

```middleware``` 的顺序很重要，也就是调用 ```app.use()``` 的顺序决定了 ```middleware``` 的顺序

需要注意的是：如果其中一个 ```middleware``` 没有调用 ```await next()```，那么后续的 ```middleware``` 将不会在继续往下执行了

例如一个检测用户权限的 ```middleware```：

```js
// 决定是否继续处理请求，还是直接返回 403 错误
app.use(async (ctx, next) => {
    if (await checkUserPermission(ctx)) {
        await next();
    } else {
        ctx.response.status = 403;
    }
})
```

```ctx``` 对象还有一些简写的方法：

* ```ctx.url``` 相当于 ```ctx.reaponse.url```

* ```ctx.type``` 相当于 ```ctx.response.type```


## 处理 URL

正常情况下，我们应该对不同的 ```URL``` 调用不同的处理函数，这样才能返回正确的结果：

```js
app.use(async (ctx, next) => {
    if (ctx.request.path == "/") {
        ctx.response.body = "index.page";
    } else {
        await next();
    }
})

app.use(async (ctx, next) => {
    if (ctx.request.path == "/test") {
        ctx.response.body = "test.page"
    } else {
        await next();
    }
})

app.use(async (ctx, next) => {
    if (ctx.request.path ==  "error") {
        ctx.response.body = "error.page"
    } else {
        await next();
    }
})
```

这样写是可以运行的，但是好像有点蠢


#### koa-router

为了处理 ```URL```，我们引入 ```kos-router``` 这个 ```middleware```，它负责处理 ```URL``` 映射：

```js
const Koa = require("koa");

// 需要注意的是，koa-router 返回的是函数（直接拿到调用后的结果）
// 相当于 
// const fn_router = require("koa-router");
// const touter = fn_router();
const router = require("koa-router")();

const app = new Koa();

// log request URL
app.use(async (ctx, next) => {
    console.log(`process ${ctx.request.method} ${ctx.request.url}...`);
    await next();
})

// add url-router
router.get("/hello/:name", async (ctx, next) => {
    var name = ctx.params.name;
    ctx.response.body = `<h1>hello ${name}</h1>`
    await next();
})

router.get("/", async (ctx, next) => {
    ctx.response.body = "<h1>Index</h1>"
})

// add router middleware
app.use(router.routes());

app.listen(3000);

console.log("app start at port 3000")
```


## 处理 POST 请求

使用 ```router.get("/path", async fn)``` 处理的是 ```get``` 请求，如果要处理 ```POST``` 请求，可以使用 ```router.post("/path" async fn)```

使用 ```POST``` 请求处理 ```URL``` 的时候，我们会遇到一个问题，```POST``` 请求通常会发送一个表单，或者 ```JSON```，它作为 ```request``` 的 ```body``` 发送，但无论是 ```Node.js``` 提供的原始 ```request``` 对象，还是 ```koa``` 提供的 ```request（ctx）```对象，都**不提供**解析 ```request``` 的 ```body``` 的功能

所以我们需要引入另外一个中间件 ```koa-bodyparser``` 来解析原始的 ```request``` 请求，然后把解析后的参数绑定到 ```ctx.request.body``` 中

```js
// 然后在 app.js 中引入 koa-bodyparser
const bodyParser = require("koa-bodyparser");

// 然后在合适的位置调用
app.use(bodyParser());
```

待续...




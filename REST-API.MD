## 组织 URL

一个简单的方法是通过固定的前缀区分，例如 

* ```/static/``` 开头的 ```url``` 是静态资源文件

* ```/api/``` 开头的 ```url``` 就是 ```REST API```

* 其他的 ```url``` 就是普通的 ```MVC``` 请求

## 统一输出 REST

我们可以通过一个 ```middleware``` 给 ```ctx``` 添加一个 ```rest()``` 方法，用于直接输出 ```JSON``` 数据，这样就不必每个异步函数都需要编写一次

```js
module.exports = {
    restify: (pathPrefix) => {
        // REST API 前缀，默认为 /api/
        pathPrefix = pathPrefix || "/api/";
        return async (ctx, next) => {
            // 判断是否是 REST API 前缀
            if (ctx.request.path.startsWith(pathPrefix)) {
                // 绑定 rest() 方法
                ctx.rest = (data) => {
                    ctx.response.type = "application/json"
                    ctx.response.body = data;
                }
                await next();
            } else {
                await next();
            }
        }
    }
}
```

这样一来，任何支持 ```REST``` 的异步函数只需要简单的调用：

```js
ctx.rest({
    data: 123
})
```

就可以完成 ```REST``` 请求的处理

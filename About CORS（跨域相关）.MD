如果浏览器支持 ```HTML5```，那么就可以一劳永逸地使用新的跨域策略：```CORS```

```CORS``` 全称 ```Cross-Origin Resource Sharing```，是 ```HTML5``` 规范定义的如何跨域访问资源。

```Origin``` 表示本域，也就是浏览器当前页面的域。当 ```JavaScript``` 向外域（如 ```sina.com```）发起请求后，浏览器收到响应后，首先检查 ```Access-Control-Allow-Origin``` 是否包含本域，如果是，则此次跨域请求成功，如果不是，则请求失败，```JavaScript``` 将无法获取到响应的任何数据。



假设本域是 ```my.com```，外域是 ```sina.com```，只要响应头 ```Access-Control-Allow-Origin``` 为 ```http://my.com```，或者是 ```*```，本次请求就可以成功。

可见，跨域能否成功，取决于对方服务器是否愿意给你设置一个正确的 ```Access-Control-Allow-Origin```，决定权始终在对方手中。

![img](http://www.liaoxuefeng.com/files/attachments/00143640805071744d58164a40e42ef92b9973824451595000/l)

上面这种跨域请求，称之为"简单请求"。简单请求包括 ```GET```、 ```HEAD``` 和 ```POST```（```POST``` 的 ```Content-Type``` 类型
仅限 ```application/x-www-form-urlencoded```、 ```multipart/form-data``` 和 ```text/plain```），并且不能出现任何自定义头（例如， ```X-Custom: 12345``` ），通常能满足90%的需求。

无论你是否需要用 ```JavaScript``` 通过 ```CORS``` 跨域请求资源，你都要了解 ```CORS``` 的原理。最新的浏览器全面支持 ```HTML5```。在引用外域资源时，除了 ```JavaScript``` 和 ```CSS``` 外，都要验证 ```CORS```。例如，当你引用了某个第三方 CDN 上的字体文件时：

```css
@font-face {
    font-family: "FontAwesome";
    src: url("http://cdn.com/fonts/fontawesome.ttf") format("truetype");
}
```

如果该 ```CDN``` 服务商未正确设置 ```Access-Control-Allow-Origin```，那么浏览器无法加载字体资源。



对于 ```PUT```、```DELETE``` 以及其他类型如 ```application/json``` 的 ```POST``` 请求，在发送 ```AJAX``` 请求之前，浏览器会先发送一个 ```OPTIONS``` 请求（称为 ```preflighted``` 请求）到这个 ```URL``` 上，询问目标服务器是否接受：

```
OPTIONS /path/to/resource HTTP/1.1
Host: bar.com
Origin: http://my.com
Access-Control-Request-Method: POST
```

服务器必须响应并明确指出允许的 ```Method```：

```
HTTP/1.1 200 OK
Access-Control-Allow-Origin: http://my.com
Access-Control-Allow-Methods: POST, GET, PUT, OPTIONS
Access-Control-Max-Age: 86400
```

浏览器确认服务器响应的 ```Access-Control-Allow-Methods``` 头确实包含将要发送的 ```AJAX``` 请求的 ```Method```，才会继续发送 ```AJAX```，否则，抛出一个错误。

由于以 ```POST```、```PUT``` 方式传送 ```JSON``` 格式的数据在 ```REST``` 中很常见，所以要跨域正确处理 ```POST``` 和 ```PUT``` 请求，服务器端必须正确响应 ```OPTIONS``` 请求。

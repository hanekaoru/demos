如果浏览器支持 ```HTML5```，那么就可以一劳永逸地使用新的跨域策略：```CORS```

```CORS``` 全称 ```Cross-Origin Resource Sharing```，是 ```HTML5``` 规范定义的如何跨域访问资源。

```Origin``` 表示本域，也就是浏览器当前页面的域。当 ```JavaScript``` 向外域（如 ```sina.com```）发起请求后，浏览器收到响应后，首先检查 ```Access-Control-Allow-Origin``` 是否包含本域，如果是，则此次跨域请求成功，如果不是，则请求失败，```JavaScript``` 将无法获取到响应的任何数据。



假设本域是 ```my.com```，外域是 ```sina.com```，只要响应头 ```Access-Control-Allow-Origin``` 为 ```http://my.com```，或者是 ```*```，本次请求就可以成功。

可见，跨域能否成功，取决于对方服务器是否愿意给你设置一个正确的 ```Access-Control-Allow-Origin```，决定权始终在对方手中。

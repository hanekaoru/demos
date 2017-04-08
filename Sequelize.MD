參考：[Sequelize 和 MySQL 对照](https://segmentfault.com/a/1190000003987871)

官方文档：[Sequelize](http://sequelize.readthedocs.io/en/latest/)

----

## 安装

主要使用 ```MySQL```、```Sequelize```、```co```

```js
$ npm install --save co
$ npm install --save sequelize
$ npm install --save mysql
```

代码模板：

```js
var Sequelize = require("sequelize");
var co = require("co");

co(function* () {
    // code here
}).catch(function(e) {
    console.log(e);
});
```

```Sequelize``` 的操作都会返回一个 ```Promise```，在 ```co``` 的框架里面可以直接进行 ```yield```


## 连接数据库

```js
var sequelize = new Sequelize(
    "user", // 数据库名
    "root",   // 用户名
    "",   // 用户密码
    {
        "dialect": "mysql",  // 数据库使用 mysql
        "host": "localhost", // 数据库服务器 ip
        "port": 3000,        // 数据库服务器端口
        "define": {
            // 字段以下划线（_）来分割（默认是驼峰命名风格）
            "underscored": true
        }
    }
);
```


## 定义表单




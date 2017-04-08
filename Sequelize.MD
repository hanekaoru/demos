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


## 定义表

```js
var User = sequelize.define(
    // 默认表名（一般这里写单数），生成时会自动转换成复数形式
    // 这个值还会作为访问模型相关的模型时的属性名，所以建议用小写形式
    "user",
    // 字段定义（主键、created_at、updated_at 默认包含，不用特殊定义）
    {
        "emp_id": {
            "type": Sequelize.CHAR(10), // 字段类型
            "allowNull": false,         // 是否允许为 NULL
            "unique": true              // 字段是否 UNIQUE
        },
        "nick": {
            "type": Sequelize.CHAR(10),
            "allowNull": false
        },
        "department": {
            "type": Sequelize.STRING(64),
            "allowNull": true
        }
    }
);
```

#### SQL

```js
CREATE TABLE IF NOT EXISTS `users`(
    `id` INTEGER NOT NULL auto_increment ,
    `emp_id` CHAR(10) NOT NULL UNIQUE,
    `nick` CHAR(10) NOT NULL,
    `department` VARCHAR(64),
    `created_at` DATETIME NOT NULL,
    `updated_at` DATETIME NOT NULL,
    PRIMARY KEY (`id`)
) ENGINE= InnoDB;
```



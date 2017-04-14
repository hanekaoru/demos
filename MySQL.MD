## 访问MySQL

当安装好了 ```MySQL``` 以后，访问 ```MySQL``` 数据库只有一种方法，就是通过网络发送 ```SQL``` 命令，```MySQL``` 服务器执行后返回结果

启动和关闭方式如下：

* 启动 MySQL 服务：```net start mysql``` 

* 关闭 MySQL 服务：```net stop mysql```

* 退出MySQL命令行：```exit```

可以使用 ```mysql -u root -p``` 来连接本地数据库（没有指定 ```--host``` 参数，所以我们连接到的是 ```localhost```）

```js
// 注意有个分号
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
+--------------------+
1 row in set (0.00 sec)
```

对于 ```node.js``` 程序，访问 ```mysql``` 也是通过网络发送 ```sql``` 命令给 ```mysql``` 服务器（通常称为 ```mysql``` 驱动程序）

## ORM

在使用 ```node.js``` 来处理关系型操作数据库时，为了方便，通常都会选择一个合适的 ORM（Object Relationship Model）框架。毕竟直接操作 ```SQL``` 比较繁琐，通过 ```ORM``` 框架，我们可以使用面向对象的方式来操作表，这里主要介绍 ```Sequelize```

更多关于 ```Sequelize``` 内容可参考：[Sequelize](https://github.com/hanekaoru/full-stack/blob/master/Sequelize.MD)

选择 ```Sequelize``` 的原因：只要 ```API``` 返回 ```Promise```，就可以用 ```await``` 调用


#### 用 Sequelize 查询 pets 表

```js
// 因为 Sequelize 返回的对象是 Promise，所以我们可以用 then() 和 catch() 分别异步响应成功和失败
Pet.findAll().then(function (pets) {
    for (let pet in pets) {
        console.log(`${pet.id}: ${pet.name}`)
    }
}).catch(function (err) {
    // err
})
```

可以改用 ES7 的 ```await()``` 来调用：

```js
var pets = await Pet.findAll();
```

但是 ```await``` 有一个限制，必须在 ```async``` 函数中调用，所以修改为：

```js
(async () => {
    var pets = await Pet.findAll()
})();
```


#### 实战

我们可以在 ```test``` 数据库中创建一个 ```pets``` 表：

```js
grant all privileges on test.* to 'www'@'%' identified by 'www';

use test;

create table pets (
    id varchar(50) not null,
    name varchar(100) not null,
    gender bool not null,
    birth varchar(10) not null,
    createdAt bigint not null,
    updatedAt bigint not null,
    version bigint not null,
    primary key (id)
) engine=innodb;
```

* 第一条 ```grant``` 命令是创建 ```mysql``` 的用户名和口令，并赋予操作 ```test``` 数据库的所有权限

* 第二条 ```use``` 命令把当前库切换为 ```test```

* 创建 ```pets``` 表

然后新建 ```config.js``` 用以配置 ```mysql```

```js
var config = {
    database: "test", // 使用的数据库名
    username: "root",
    password: "root", 
    host: "localhost", // 主机名
    port: 3306 // mysql 默认 3306
}

module.exports = config;
```







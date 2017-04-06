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

更多关于 ```Sequelize``` 内容可参考：[Sequelize 和 MySQL 对照](https://segmentfault.com/a/1190000003987871)

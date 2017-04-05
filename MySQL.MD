## 访问MySQL

当安装好了 ```MySQL``` 以后，访问 ```MySQL``` 数据库只有一种方法，就是通过网络发送 ```SQL``` 命令，```MySQL``` 服务器执行后返回结果

启动和关闭方式如下：

* 启动 MySQL 服务：```net start mysql``` 

* 关闭 MySQL 服务：```net stop mysql```

首先使用 ```mysql -u root -p``` 来连接本地数据库（没有指定 ```--host``` 参数，所以我们连接到的是 ```localhost```）

参考：

[理解RESTful架构](http://www.ruanyifeng.com/blog/2011/09/restful.html)

[RESTful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)

## REST

```REST``` 就是一种设计 ```API``` 的模式。最常用的数据格式是 ```JSON```。由于 ```JSON``` 能直接被 ```JavaScript``` 读取，所以，以 ```JSON``` 格式编写的 ```REST``` 风格的 ```API``` 具有简单、易读、易用的特点

举例来说，有一个 ```API``` 提供动物园（zoo）的信息，还包括各种动物和雇员的信息，则它的路径应该设计成下面这样

```js
https://api.example.com/v1/zoos

https://api.example.com/v1/animals

https://api.example.com/v1/employees
```

常用的请求有下面五个：

* GET（SELECT）：从服务器取出资源（一项或多项）

* POST（CREATE）：在服务器新建一个资源

* PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）

* PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）

* DELETE（DELETE）：从服务器删除资源

```js
// 获取 id 为 123 的 Product
GET /api/products/123

// 新建一个Product使用POST请求
POST /api/products

// 更新一个Product使用PUT请求
PUT /api/products/123

// 删除一个Product使用DELETE请求
DELETE /api/products/123

// 还可以按层次组织 例如获取某个 Product 的所有评论
GET /api/products/123/reviews

// 只需要获取部分数据时
GET /api/products/123/reviews?page=2&size=10&sort=time
```

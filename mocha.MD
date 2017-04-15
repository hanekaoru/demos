## 单元测试

单元测试是用来对一个模块，一个函数或者一个类来进行正确性检验的测试工作

#### 单元测试的意义

如果我们对原函数代码做了修改，只需要再跑一遍单元测试，如果通过，说明我们的修改不会对函数原有的行为造成影响，如果测试不通过，说明我们的修改与原有行为不一致，要么修改代码要么修改测试

这种以测试为驱动的开发模式最大的好处就是确保一个程序模块的行为符合我们设计的测试用例。在将来修改的时候，可以极大程度地保证该模块行为仍然是正确的。

#### mocha

```mocha``` 是 ```JavaScript``` 的一种单元测试款框架，其特点是：

* 既可以测试简单的 ```JavaScript``` 函数，又可以测试异步代码，因为异步是 ```JavaScript``` 的特性之一

* 可以自动运行所有测试，也可以只运行特定的测试

* 可以支持 ```before```，```after```，```beforeEach``` 和 ```afterEach``` 来编写初始化代码


## 测试脚本

例如一个 ```add``` 函数：

```js
function add (x, y) {
    return x + y;
}

module.exports = add;
```

要测试这个加法模块是否正确，就需要写测试脚本

通常，测试脚本与所要测试的源码脚本同名，但是其后缀为 ```test.js```：

```js
var add = require("./add.js");
var expect = require("chai").expect;

describe("测试 add 函数", function () {
	it("1 加 1 应该为 2", function () {
		expect(add(1, 1)).to.be.equal(2);
	});
});
```

测试脚本应该包括一个或多个 ```describe``` 块，每个 ```describe``` 块应该包括一个或多个 ```it``` 块

```describe``` 块称为 "测试套件"，表示一组相关的测试，它是一个函数，第一个参数是测试套件的名称，第二个参数是一个实际执行的函数

```it``` 块称为 "测试用例"，表示一个单独的测试，是测试的最小单位，第一个参数是测试用例的名称，第二个参数是一个实际执行的函数


## 断言库

```mocha``` 本身不带断言库，所以必须先引入断言库

```js
var expect = require("chai").expect;

...

// 使用
expect(add(1, 1)).to.be.equal(2);
```

这里引入的是 ```chai```：

```js
// 相等或不相等
expect(4 + 5).to.be.equal(9);
expect(4 + 5).to.be.not.equal(10);
expect(foo).to.be.deep.equal({ bar: "baz" });

// 布尔值为true
expect("everthing").to.be.ok;
expect(false).to.not.be.ok;

// typeof
expect("test").to.be.a("string");
expect({ foo: "bar" }).to.be.an("object");
expect(foo).to.be.an.instanceof(Foo);

// include
expect([1, 2, 3]).to.include(2);
expect("foobar").to.contain("foo");
expect({ foo: "bar", hello: "universe" }).to.include.keys("foo");

// empty
expect([]).to.be.empty;
expect("").to.be.empty;
expect({}).to.be.empty;

// match
expect("foobar").to.match(/^foo/); 
```

如果 expect 断言不成立，就会抛出一个错误，事实上，只要不抛出错误，测试用例就算通过：

```js
// 内部没有任何代码，但是由于没有抛出错误，所以还是会通过
if ("1 + 1 应该为 2", function () { })
```


 
 

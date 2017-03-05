## $

```jQuery``` 把所有功能全部封装在一个全局变量 ```jQuery``` 中，而 ```$``` 也是一个合法的变量名，它是变量 ```jQuery``` 的别名：

```js
window.jQuery; // jQuery(selector, context)

window.$; // jQuery(selector, context)

$ === jQuery; // true

typeof ($); // "function""
```

```$``` 本质上就是一个函数，但是函数也是对象，于是 ```$``` 除了可以直接调用外，也可以有很多其他属性

但是，如果 ```$``` 这个变量不幸地被占用了，而且还不能改，那我们就只能让 ```jQuery``` 把 ```$``` 变量交出来，然后就只能使用 ```jQuery``` 这个变量

```js
// 原理是：先在内部保存了原来的 $,调用 jQuery.noConflict() 时会把原来保存的变量还原

$; // jQuery(selector, context)

jQuery.noConflict();

$; // undefined

jQuery; // jQuery(selector, context)
```



#### 针对表单元素

```:input``` 可以选择 ```<input>```，```<textarea>```，```<select>```和```<button>```



#### 查找

对于位于同一层级的节点，可以通过 ```next()``` 和 ```prev()``` 方法



#### 过滤

```filter()``` 方法可以过滤掉不符合选择器条件的节点

或者传入一个函数，要特别注意函数内部的 ```this``` 被绑定为 ```DOM``` 对象，而不是 ```jQuery``` 对象

```js
var langs = $("ul.lang li"); 

langs.filter(function () {
    return this.innerHTML.indexOf("S") === 0; // 返回S开头的节点 
});
```

```map()``` 方法把一个 ```jQuery``` 对象包含的若干 ```DOM``` 节点转化为其他对象

```js
var langs = $("ul.lang li"); 

var arr = langs.map(function () {
    return this.innerHTML;
}).get(); // 数组
```



#### 操作 DOM

```attr()``` 和 ```prop()``` 对于属性 ```checked``` 处理有所不同

```js
var radio = $("#test-radio");

radio.attr("checked"); // "checked"
radio.prop("checked"); // true
```

```prop()``` 返回值更合理一些。不过，用 ```is()``` 方法判断更好：

```js
var radio = $("#test-radio");

radio.is(":checked"); // true
```

类似的属性还有 ```selected```，处理时最好用 ```is(":selected")```



#### 修改 DOM 结构

```append()``` 把 ```DOM``` 添加到最后，```prepend()``` 则把 ```DOM``` 添加到最前

同级节点可以用 ```after()``` 或者 ```before()``` 方法

```js
var js = $("#test-div>ul>li:first-child");

js.after("<li><span>Lua</span></li>");
```





#### 事件触发条件

当用户在文本框中输入时，就会触发 ```change``` 事件。但是，如果用 ```JavaScript``` 代码去改动文本框的值，将不会触发 ```change``` 事件

但是如果我们希望用代码触发 ```change``` 事件，可以直接调用无参数的 ```change()``` 方法来触发该事件：

```js
var input = $("#test-input");

input.val("change it!");
input.change(); // 触发 change 事件
```



#### 浏览器安全限制

```window.open()``` 函数 如果是延迟执行（比如放到 ```setTimeout()``` 中）的，将会被浏览器拦截。


#### 动画

```jQuery``` 没有实现对 ```background-color``` 的动画效果，用 ```animate()``` 设置 ```background-color``` 是没有效果的。这种情况下可以使用 ```CSS3``` 的 ```transition``` 实现动画效果



#### AJAX

如果 ```get()``` 方法的第二个参数如果是 ```object```，```jQuery``` 自动把它变成 ```query string``` 然后加到 ```URL``` 后面

```
var jqxhr = $.get("/path/to/resource", {
    name: "Bob Lee",
    check: 1
});
```

得到的 ```URL``` 为：```/path/to/resource?name=Bob%20Lee&check=1```



#### 扩展

给 ```jQuery``` 对象绑定一个新方法是通过扩展 ```$.fn``` 对象实现的。让我们来编写第一个扩展 - ```highlight()```

```js
$.fn.highlight1 = function () {

    // this 已绑定为当前 jQuery 对象
    this.css("backgroundColor", "#fffceb").css("color", "#d85030");
    return this;

}
```

```js
$.fn.highlight2 = function (options) {

    // 要考虑到各种情况:
    // options 为 undefined
    // options 只有部分 key
    var bgcolor = options && options.backgroundColor || "#fffceb";
    var color = options && options.color || "#d85030";

    this.css("backgroundColor", bgcolor).css("color", color);
    return this;

}
```

对于默认值的处理，我们用了一个简单的 ```&&``` 和 ```||``` 短路操作符，总能得到一个有效的值

另一种方法是使用 ```jQuery``` 提供的辅助方法 ```$.extend(target, obj1, obj2, ...)```，它把多个 ```object``` 对象的属性合并到第一个 ```target``` 对象中，**遇到同名属性，总是使用靠后的对象的值，也就是越往后优先级越高**：

```js
// 把默认值和用户传入的 options 合并到对象 {} 中并返回
var opts = $.extend({}, {
    backgroundColor: "#00a8e6",
    color: "#ffffff"
}, options);
```

#### 最终版

```js
$.fn.highlight = function (options) {

    // 合并默认值和用户设定值
    var opts = $.extend({}, $.fn.highlight.defaults, options);
    this.css("backgroundColor", opts.backgroundColor).css("color", opts.color);

    return this;

}

// 设定默认值
$.fn.highlight.defaults = {
    color: "#d85030",
    backgroundColor: "#fff8de"
}
```

大致流程为：

* 给 ```$.fn``` 绑定函数，实现插件的代码逻辑

* 插件函数最后要 ```return this;``` 以支持链式调用

* 插件函数要有默认值，绑定在 ```$.fn.<pluginName>.defaults``` 上

* 用户在调用时可传入设定值以便覆盖默认值




#### 错误处理 try ... catch ... finally

有错误发生时，执行流程像这样：

* 先执行 ```try { ... }``` 的代码

* 执行到出错的语句时，后续语句不再继续执行，转而执行 ```catch (e) { ... }``` 代码

* 最后执行 ```finally { ... }``` 代码

而没有错误发生时，执行流程像这样

* 先执行 ```try { ... }``` 的代码

* 因为没有出错，```catch (e) { ... }``` 代码不会被执行

* 最后执行 ```finally { ... }``` 代码

最后请注意，```catch``` 和 ```finally``` 可以不必都出现

需要注意的是：如果在一个函数内部发生了错误，它自身没有捕获，错误就会被抛到外层调用函数，如果外层函数也没有捕获，该错误会一直沿着函数调用链向上抛出，直到被 ```JavaScript``` 引擎捕获，代码终止执行

但是涉及到异步代码，无法在调用时捕获，原因就是在捕获的当时，回调函数并未执行

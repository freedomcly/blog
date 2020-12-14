# JavaScript 之 this 指向

this 指向取决于函数调用的方式。

this 指向优先级：

* new 绑定。this 指向 new 创建的新对象，也就是构造函数的实例。
* 显示绑定。显示绑定指定的对象。
* 隐式绑定。把函数作为方法调用的那个对象。
* 默认绑定。非严格模式指向全局对象，严格模式指向 undefined。

举个栗子：

    var value = 1

    var foo = {
      value: 2,
      bar: function () {
        return this.value
      }
    }

    console.log(foo.bar()) // 2
    console.log((foo.bar)()) // 2
    console.log((foo.bar = foo.bar)()) // 1
    console.log((false || foo.bar)()) // 1
    console.log((foo.bar, foo.bar)()) // 1

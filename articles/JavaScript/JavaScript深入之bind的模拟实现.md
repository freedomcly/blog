# JavaScript 深入之 bind 的模拟实现

ES5 提供了 bind 函数，用于返回一个新函数，这个新函数修改了 this 指向，也可以提供前若干项参数。

    function foo() {
      return this.value + arguments[0] + arguments[1] + arguments[2]
    }

    var value = 2

    var obj = {value: 1}

    var bindObj = foo.bind(obj, 1, 2)

    bindObj(3) // 7

先实现一个简单版本的 bind，修改 this 指向：

    // 第 1 版
    Function.prototype.bind = function (context) {
      var self = this
      return function() {
        self.call(context)
      }
    }

添加参数拼接：

    Function.prototype.bind = function (context) {
      var self = this
      var args1 = Array.prototype.slice.call(arguments, 1)
      return function() {
        var args2 = Array.prototype.slice.call(arguments)
        self.apply(context, args1.concat(args2))
      }
    }

当函数有返回值时，还不能返回，修改一下：

    // 第 2 版
    Function.prototype.bind = function (context) {
      var self = this
      var args1 = Array.prototype.slice.call(arguments, 1)
      return function() {
        var args2 = Array.prototype.slice.call(arguments)
        return self.apply(context, args1.concat(args2))
      }
    }

看起来很不错，然而，bind 函数还需要满足一个条件：**作为构造函数时，忽略原来提供的 this，提供的参数列表仍然需要插入到构造函数调用时的参数列表之前**。

当前版本的 bind 函数能否满足呢？

    function OriginPerson(name, age) {
      this.name = name
      this.age = age
    }

    var context = {name: 'outer'}

    var Person = OriginPerson.bindy(context, 'maomao')

    var person = new Person(3)
    
    console.log(person) // {}
    console.log(context) // {name: 'maomao', age: 3}

期望的 person 值应为 `{name: 'maomao', age: 3}`，然而当前 person 值为空对象，this 没有被忽略，context 被当做 this，修改了其内部属性。

再来一版：

    Function.prototype.bind = function (context) {
      var self = this
      var args1 = Array.prototype.slice.call(arguments, 1)

      var resultFunc = function () {
        var isNew = this instanceof resultFunc

        var args2 = Array.prototype.slice.call(arguments)
        return self.apply(isNew ? this : context, args1.concat(args2))
      }
      return resultFunc
    }

通过`this instanceof resultFunc`来判断是否是用`new`操作符执行函数，已达到目的。新的问题又来了，`person`的构造函数本应是`OriginPerson`，现在是`Person`，需要修改原型关系：



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

    Function.prototype.bind = function (context) {
      var self = this
      var args1 = Array.prototype.slice.call(arguments, 1)
      return function() {
        var args2 = Array.prototype.slice.call(arguments)
        return self.apply(context, args1.concat(args2))
      }
    }

看起来很不错，然而，bind 函数还需要满足一个条件：作为构造函数时


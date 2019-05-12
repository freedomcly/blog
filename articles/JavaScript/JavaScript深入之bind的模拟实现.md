# JavaScript 深入之 bind 的模拟实现

ES5 提供了 bind 函数，用于返回一个新函数，这个新函数修改了 this 指向，也可以提供前若干项参数。

```javascript
function foo() {
  return this.value + arguments[0] + arguments[1] + arguments[2]
}

var value = 2

var obj = {value: 1}

var bindObj = foo.bind(obj, 1, 2)

bindObj(3) // 7
```

先实现一个简单版本的 bind，修改 this 指向：

```javascript
// 第 1 版
Function.prototype.bind = function (context) {
  var self = this
  return function() {
    self.call(context)
  }
}
```

添加参数拼接：

```javascript
Function.prototype.bind = function (context) {
  var self = this
  var args1 = Array.prototype.slice.call(arguments, 1)
  return function() {
    var args2 = Array.prototype.slice.call(arguments)
    self.apply(context, args1.concat(args2))
  }
}
```

当函数有返回值时，还不能返回，修改一下：

```javascript
// 第 2 版
Function.prototype.bind = function (context) {
  var self = this
  var args1 = Array.prototype.slice.call(arguments, 1)
  return function() {
    var args2 = Array.prototype.slice.call(arguments)
    return self.apply(context, args1.concat(args2))
  }
}
```

看起来很不错，然而，bind 函数还需要满足一个条件：**作为构造函数时，忽略原来提供的 this，提供的参数列表仍然需要插入到构造函数调用时的参数列表之前**。

当前版本的 bind 函数能否满足呢？

```javascript
function OriginPerson(name, age) {
  this.name = name
  this.age = age
}

var context = {name: 'outer'}

var Person = OriginPerson.bind(context, 'maomao')

var person = new Person(3)
    
console.log(person) // {}
console.log(context) // {name: 'maomao', age: 3}
```

期望的 person 值应为 `{name: 'maomao', age: 3}`，然而当前 person 值为空对象，this 没有被忽略，context 被当做 this，修改了其内部属性。

再来一版：

```javascript
// 第 3 版
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
```

通过`this instanceof resultFunc`来判断是否是用`new`操作符执行函数，已达到目的。新的问题又来了，实例 person 无法继承 OriginPerson，比如

```javascript
function OriginPerson(name, age) {
  this.name = name
  this.age = age
}
OriginPerson.prototype.getName = function(){return this.name}
    
var context = {name: 'outer'}

var Person = OriginPerson.bind(context, 'maomao')
Person.prototype.getAge = function(){return this.age}

var person = new Person(3)
person.getAge()
person.getName() // Uncaught TypeError: person.getName is not a function
```
    
希望 person 既能拿到 OriginPerson 的实例方法，又能拿到 Person 的实例方法。

```javascript
Function.prototype.bind = function (context) {
  var self = this
  var args1 = Array.prototype.slice.call(arguments, 1)

  var resultFunc = function () {
    var isNew = this instanceof resultFunc

    var args2 = Array.prototype.slice.call(arguments)
    return self.apply(isNew ? this : context, args1.concat(args2))
  }

  resultFunc.prototype = this.prototype

  return resultFunc
}
```

添加`resultFunc.prototype = this.prototype`，可以达到目的，但是目前 OriginPerson 和 Person 共用了 prototype，Person 添加实例方法时，比如上面的例子`Person.prototype.getAge = function(){return this.age}`会写入 `OriginPerson.prototype` 中，因此我们需要中转一下：

```javascript
// 第 4 版
Function.prototype.bind = function (context) {
  var self = this
  var args1 = Array.prototype.slice.call(arguments, 1)

  var resultFunc = function () {
    var isNew = this instanceof resultFunc

    var args2 = Array.prototype.slice.call(arguments)
    return self.apply(isNew ? this : context, args1.concat(args2))
  }

  var fNOP = function(){}
  if (this.prototype) {
    fNOP.prototype = this.prototype
  }

  resultFunc.prototype = new fNOP()

  return resultFunc
}
```

这里中转的原理类似于：

```javascript
Object.create = function (obj) {
  function F() {}
  F.prototype = obj
  return new F()
}
```

再优化：
1. 判断调用 bind 的对象是否为函数
2. 把 bind 函数写为不可枚举

```javascript
if (!Function.prototype.bind) {
  Object.defineProperty(Function.prototype, 'bind', {
    value: function (context) {
      if (typeof this !== 'function') {
        throw new TypeError('Function.prototype.bind - what is trying to be bound is not callable')
      }
      var self = this
      var args1 = Array.prototype.slice.call(arguments, 1)

      var resultFunc = function () {
        var isNew = this instanceof resultFunc

        var args2 = Array.prototype.slice.call(arguments)
        return self.apply(isNew ? this : context, args1.concat(args2))
      }

      var fNOP = function(){}
      if (this.prototype) {
        fNOP.prototype = this.prototype
      }

      resultFunc.prototype = new fNOP()

      return resultFunc
    }
  })
}
```

bind 函数完成。

## 参考资料

* https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind
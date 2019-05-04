# 箭头函数

`a => a`等价于`function(a){return a}`

箭头函数和普通函数对比一览：

| **项目** | **箭头函数** | **普通函数** |
| :--- | :--- | :--- |
| this指向 | 没有this，指向外层作用域链的this | 绑定了this |
| 是否有arguments变量 | 没有，可以使用rest参数 | 有 |
| 是否能使用new命令 | 不能 | 能 |
| 是否有new.target | 没有 | 有，使用new操作符时指向构造函数，否则为undefined |
| 是否有原型对象 | 没有 | 有 |
| 是否有super | 没有 | 有 |
| 是否能作为generator函数（使用yield命令） | 不能 | 能 |

## 箭头函数的特征

**1. 没有this**

    var obj = {
	    myname: 'obj',
	    arrow: () => {console.log(this.myname, this)},
	    regular: function() {console.log(this.myname, this)} 
    }

    obj.arrow() // undefined, Window
    obj.regular() // obj, Object {...}

箭头函数没有this，因此箭头函数中的this需要查找作用域链，拿到外层作用域的this，如果外层作用域也没有this（另一个箭头函数），会一直向外追溯一直到全局作用域拿到全局对象。

    var arrow = () => {
      return () => {
        return () => {
          return this
        }
      }
    }

    arrow()()() // Window

由于箭头函数没有this，与this相关的方法`apply``call``bind`也无效。

    var a = 2
    var arrow = () => {console.log(this.a)}

    arrow.bind({a: 1})() // 2
    arrow.apply({a: 1}) // 2

为什么箭头函数没有 this？函数中的 this 来自哪里呢？

函数执行前要做点准备工作，也就是创建函数的执行上下文。箭头函数没有完整的执行上下文，它的执行上下文只有变量对象和作用域链，没有 this，因此它的 this 要查找作用域链。

**2. 没有arguments**

    var arrow = () => {console.log(arguments)}
    arrow() // Uncaught ReferenceError: arguments is not defined
    
虽然箭头函数没有arguments，但可以使用命名参数或者rest参数。
    
    var arrow = (...args) => {console.log(args)}
    arrow(1) // [1]

与this一样，箭头函数中的arguments需要查找作用域链，因此可以获取外层函数的arguments：

    var regular = function () {
      return () => arguments
    }
    regular(1)() // Arguments [1, ...]

**3. 不能使用new命令调用**

    var Arrow = name => {this.myname = name}
    var arrow = new Arrow('arrow') // Uncaught TypeError: Arrow is not a constructor

JavaScript函数内部有两个方法：`[[Call]]`和`[[Construct]]`。

当通过 new 调用函数时，执行`[[Construct]]`方法，创建一个实例对象，将 this 绑定到实例上，然后再执行函数体。

当直接调用的时候，执行`[[Call]]`方法，直接执行函数体。

箭头函数并没有`[[Construct]]`方法，不能被用作构造函数，如果通过 new 的方式调用，会报错。

**4. 没有`new.target`**

    function Person(name) {
      if (new.target === Person) {
        this.name = name
      } else {
        throw new Error('必须使用 new 命令生成实例')
      }
    }

    var person1 = new Person('maomao')
    var person2 = Person('eric') // Uncaught Error: 必须使用 new 命令生成实例

ES6中，new命令调用的构造函数中引入了`new.target`属性，指向构造函数。判断`new.target`可以避免构造函数没有使用new命令的情况。

当然也用`instanceof`判断，如下：

    function Person(name) {
      if (this instanceof Person) {
        this.name = name
      } else {
        throw new Error('必须使用 new 命令生成实例')
      }
    }

    var person1 = new Person('maomao')
    var person2 = Person('eric') // Uncaught Error: 必须使用 new 命令生成实例

箭头函数不能使用new命令调用，也没有`new.target`。

    var Arrow = () => {console.log(new.target)}
    var arrow = new Arrow() // Uncaught SyntaxError: new.target expression is not allowed here

**4. 没有原型**

    var Arrow = () => {}
    var Regular = function () {}
    console.log(Arrow.prototype) // undefined
    console.log(Regular.prototype) // {constructor: ƒ}
    
**5. 没有super**

箭头函数没有原型，自然也不能用`super`关键字。

## 总结

引用一段MDN上箭头函数的解释：

> An arrow function expression is a syntactically compact alternative to a regular function expression, although without its own bindings to the this, arguments, super, or new.target keywords. Arrow function expressions are ill suited as methods, and they cannot be used as constructors.

翻译过来即，箭头函数表达式与普通函数表达式相比，没有`this`、`arguments`、`super`和`new.target`。箭头函数表达式不适用于方法，也不能用作构造函数。

为什么箭头函数不适用于方法呢？还是开头的那个例子：

    var obj = {
	    myname: 'obj',
	    arrow: () => {console.log(this.myname, this)},
	    regular: function() {console.log(this.myname, this)} 
    }

    obj.arrow() // undefined, Window
    obj.regular() // obj, Object {...}

由于箭头函数没有绑定this，作为方法使用没有意义。

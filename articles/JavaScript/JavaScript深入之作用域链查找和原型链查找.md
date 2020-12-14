# JavaScript 之作用域链查找和原型链查找

JavaScript 中的作用域链查找和原型链查找很相似，有必要详细对比一下他们的行为。

查找分两种：

* RHS(right hand side)：变量在右侧，也就是查询操作时的查找
* LHS(left hand side)：变量在左侧，也就是赋值操作时的查找

## 作用域链

### 1. 作用域链中的 RHS

    var global = 'global'

    function foo () {
      var fooValue = 'foo'

      function bar () {
        var barValue = 'bar'
        console.log(barValue, fooValue, global) // bar foo global
        console.log(something) // Uncaught ReferenceError: something is not defined
      }

      bar()
    }

    foo()

`bar`的作用域链是：

    [
      barContext.VO,
      fooContext.VO,
      globalContext.VO
    ]

`foo`的作用域链是：

    [
      fooContext.VO,
      globalContext.VO
    ]

顺着作用域链查询变量。若最外层作用域的变量对象中也不存在，则抛出`ReferenceError`。

### 2. 作用域链中的 LHS

    var global = 'global'
    var fooValue = 'global'

    function foo () {
      var fooValue = 'foo'
      var fooValue2 = 'foo2'

      function bar () {
        var fooValue = 'foo in bar'
        fooValue2 = 'foo2 in bar'
        fooValue3 = 'foo3 in bar'
        console.log(fooValue, fooValue2) // foo in bar, foo2 in bar
      }

      bar()
      console.log(fooValue, fooValue2) // foo, foo2 in bar 
      console.log(fooValue3) // foo3 in bar
    }

    foo()

若作用域链上存在同名变量：
* 如果用`var`声明，则在当前作用域中声明新的变量，屏蔽外层作用域中的同名变量，赋值操作对外层作用域中的同名变量没有影响。
* 如果不用`var`声明，直接操作最近的外层作用域中的同名变量，赋值操作对外层作用域有影响。

## 原型链

### 1. 原型链中的 RHS

    var foo = {
      name: 'foo'
    }

    var bar = Object.create(foo)

    console.log(bar.name) // foo
    console.log(bar.age) // undefined
    
`bar`的原型链是：

    [
      bar.__proto__,
      bar.__proto__.__proto__, // 即`Object.prototype`
      bar.__proto__.__proto__.__proto__, //即`Object.prototype.__proto__ === null`
    ]

顺着原型链查询变量。若最后的原型也不存在，则返回`undefined`。

### 2. 原型链中的 LHS

    var foo = {
      name: 'foo'
    }

    var bar = Object.create(foo)

    Object.defineProperty(foo, 'age', {
      value: 11,
      writable: false
    })

    Object.defineProperty(foo, 'school', {
      get () {
        return this._school
      },
      set (value) {
        this._school = value
      }
    })
    
    bar.age = 12
    console.log(bar.age) // 11
    
    bar.school = 'UESTC'
    console.log(bar) // {_school: 'UESTC'}
    console.log(bar.school) // 'UESTC'

若原型链上存在同名属性：
* 同名属性是普通数据访问属性，并且没有被标记为只读（writable: false），就直接在当前对象中添加一个同名属性，屏蔽上层同名属性。
* 同名属性被标记为只读（writable: false），那么无法修改已有属性，也不能在当前对象添加同名属性。在严格模式下会抛出错误，在非严格模式下这条语句会被忽略。上面的`bar.age = 12`不生效。
* 同名属性是一个 setter，那么会调用这个 setter，属性不会被添加到当前对象。上面的`bar.school = 'UESTC'`调用了 setter，但`school`没有添加到当前对象中。

## 对比

| **项目** | **作用域链** | **原型链** |
| :--- | :--- | :--- |
| RHS 查找，若链上不存在 | 抛出`ReferenceError` | 返回`undefined` |
| 是否屏蔽外层同名变量或属性 | `var let const` 声明则屏蔽，否则不屏蔽 | 情况复杂，取决于属性是否只读，是否是 setter |

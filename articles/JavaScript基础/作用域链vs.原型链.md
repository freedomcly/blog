# 作用域链 vs. 原型链

* LHS(left hand side)，变量在左侧，也就是赋值
* RHS(right hand side)，变量在右侧，也就是查询

## 作用域链

### 1.作用域链RHS

    var global = 'global'

    function foo () {
      var fooValue = 'foo'

      function bar () {
        var barValue = 'bar'
        console.log(barValue, fooValue, global)
      }

      bar()
    }

    foo()
    
    // bar foo global

`bar`的作用域链是bar => foo => global，顺着作用域链查询变量。若最外层作用域也不存在，则抛出`ReferenceError`。

### 2.作用域链LHS

    var global = 'global'
    var fooValue = 'global'

    function foo () {
      var fooValue = 'foo'
      var fooValue2 = 'foo2'
      var fooValue3 = [1, 2]

      function bar () {
        var fooValue = 'foo in bar'
        fooValue2 = 'foo2 in bar'
        var barValue3 = fooValue3
        barValue3.push(3)
        console.log(fooValue, fooValue2)
      }

      bar()
      console.log(fooValue, fooValue2)
      console.log(fooValue3)
    }

    foo()
    
    // foo in bar, foo2 in bar
    // foo, foo2 in bar 
    // [1, 2, 3]

若作用域链中存在同名变量：
* 如果用var声明，则在当前作用域中声明新的变量，屏蔽外层作用域中的同名变量，赋值操作对外层作用域中的同名变量没有影响。
* 如果不用var声明，直接操作最近的外层作用域中的同名变量，赋值操作有影响。**引用类型更需要注意**。

## 原型链

### 1.原型链RHS

    var foo = {
      name: 'foo'
    }

    var bar = Object.create(foo)

    console.log(bar.name)
    console.log(bar.age)
    
    // foo
    // undefined
    
`bar`的原型链是：

* `bar`
* `foo`即`bar.__proto__`
* `Object.prototype`即`bar.__proto__.__proto__`
* `Object.prototype.__proto__`即`bar.__proto__.__proto__.__proto__`

顺着原型链查询变量。若最外层原型也不存在，则返回`undefined`。

### 2.原型链LHS

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

* 如果在原型链上层存在同名的普通数据访问属性并且没有被标记为只读（writable: false），那就直接在当前对象中添加一个同名属性，屏蔽上层同名属性
* 如果在原型链上层存在同名属性，并且被标记为只读（writable: false），那么无法修改已有属性，也不能在当前对象添加同名属性。在严格模式下回抛出错误，在非严格模式下这条语句会被忽略。例子：`bar.age = 12`
* 如果在原型链上层存在同名属性，并且它是一个setter，那么会调用这个setter。属性不会被添加到当前对象。例子：`bar.school = 'UESTC'`

## 对比

| **项目** | **作用域链** | **原型链** |
| :--- | :--- | :--- |
| RHS查找，链上不存在 | 抛出`ReferenceError` | 返回`undefined` |
| 是否屏蔽外层同名变量/属性 | var/let/const 声明则屏蔽，否则不屏蔽 | 情况复杂，取决于属性是否只读，是否是setter |

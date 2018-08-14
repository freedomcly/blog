# 作用域链 vs. 原型链

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



## 对比

| **项目** | **作用域链** | **原型链** |
| :--- | :--- | :--- |
| 链上不存在 | 抛出`ReferenceError` | 返回`undefined` |
| 是否有arguments变量 | 没有，可以使用rest参数 | 有 |
| 是否能作为构造函数（使用new命令） | 不能 | 能 |
| 是否能作为generator函数（使用yield命令） | 不能 | 能 |

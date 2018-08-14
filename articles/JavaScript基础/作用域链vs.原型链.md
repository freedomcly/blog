# 作用域链 vs. 原型链

## 作用域链

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

`bar`的作用域链是bar => foo => global，顺着作用域链查询
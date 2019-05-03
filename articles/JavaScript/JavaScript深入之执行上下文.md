# JavaScript 深入之执行上下文

## 词法作用域

众所周知，JavaScript 中的作用域是词法作用域。一言以蔽之，JavaScript 中，函数执行依赖的作用域是在函数定义时决定的，而不是函数调用时决定的。

    var value = 1

    function foo() {
      console.log(value)
    }

    function bar() {
      var value = 2
      foo()
    }

    bar() // 1

如果 JavaScript 是词法作用域，那么`bar()`打印的是 1，如果是动态作用域，则应该是 2。

什么语言是动态作用域呢？bash 就是动态作用域：


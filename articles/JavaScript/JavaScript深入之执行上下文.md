# JavaScript 深入之执行上下文

## 词法作用域

众所周知，JavaScript 中的作用域是词法作用域。**一言以蔽之，JavaScript 中，函数执行依赖的作用域是在函数定义时决定的，而不是函数调用时决定的。**

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

    value=1
    function foo () {
      echo $value;
    }
    function bar () {
      local value=2;
      foo;
    }
    bar

打印的结果是 2。

《JavaScript 权威指南》里有两段代码：

    var scope = 'global scope'
    function checkscope() {
      var scope = 'local scope'
      function f() {return scope}
      return f()
    }
    checkscope()
    

    var scope = 'global scope'
    function checkscope() {
      var scope = 'local scope'
      function f() {return scope}
      return f
    }
    checkscope()()

这两段代码分别返回什么？

由于 JavaScript 的作用域是词法作用域，因此两段代码都是返回 local scope。那么，两段代码究竟有什么不同？

## 执行上下文栈

众所周知，JavaScript 引擎并非一行一行地分析执行代码，而是一段一段地分析执行，在每段代码执行前会进行“准备工作”，即建立执行上下文。

正因如此，JavaScript 中才会存在变量提升，变量提升就是这个“准备工作”的结果之一。参考[《JavaScript 深入之变量提升和函数提升》](./JavaScript深入之变量提升和函数提升.md)
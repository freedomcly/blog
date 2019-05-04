# JavaScript 深入之闭包

## 闭包概念

> 有权访问另一个函数作用域中的变量的函数。
——引自《JavaScript 高级程序设计》

> 当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行。
——引自《你不知道的JavaScript上》

实践角度的闭包：
* 即使创建它的上下文已经销毁，它仍然存在（比如，内部函数从父函数中返回）
* 引用了另一个函数作用域中的变量（自由变量）

上一篇中第 2 段代码：

    var scope = 'global scope'
    function checkscope() {
      var scope = 'local scope'
      function f() {return scope}
      return f
    }
    checkscope()()

其中 f 函数满足创建它的上下文 checkscope 已销毁，它仍然存在，也满足引用了另一个函数作用域中的变量 scope，因此 f 函数和声明 f 函数的词法环境是闭包。

## 闭包原理

在上一篇中，详细描述了第 2 段代码的执行过程，可以比较容易理解闭包的原理。

f 函数声明时，内部 [[scope]] 保存了 checkscope 函数的作用域链：

    f.[[scope]] = [
      checkscopeContext.VO,
      globalContext.VO
    ]

f 函数被返回，checkscope 弹出执行上下文栈：

    ECStack = [
      globalContext
    ]

f 函数开始执行，压入执行上下文栈：

    ECStack = [
      fContext,
      globalContext
    ]

f 函数执行上下文初始化：

    fContext = {
      AO: {
        arguments: {length: 0},
      },
      scope: [AO, ...f.[[scope]] ]
    }
    
此时，创建 f 函数的上下文 checkscopeContext 早已经弹出执行上下文栈，然而 f 函数依然可以通过作用域链查找，访问其变量 scope。这就是闭包原理。

其中最重要的，函数声明时函数内部 [[scope]] 会保存其执行上下文的作用域链，这是闭包得以实现的基础。
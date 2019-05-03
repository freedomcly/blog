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

正因如此，JavaScript 中才会存在变量提升，变量提升就是这个“准备工作”的结果之一。参考[《JavaScript 深入之变量提升和函数提升》](./JavaScript深入之变量提升和函数提升.md)。

每段代码怎么划分呢？只有三种：全局代码、函数代码、eval 代码。

也就是说，在全局代码执行前，JavaScript 引擎会建立全局执行上下文，在每个函数执行前，JavaScript 引擎会建立函数执行上下文。

执行上下文怎么管理呢？通过执行上下文栈进行管理。

上面两段代码的执行过程是怎样的呢？

    var scope = 'global scope'
    function checkscope() {
      var scope = 'local scope'
      function f() {return scope}
      return f()
    }
    checkscope()
    
    // 执行上下文栈变化过程
    ECStack.push(globalContext)
    ECStack.push(<checkscope>functionContext)
    ECStack.push(<f>functionContext>
    ECStack.pop()
    ECStack.pop()
    ECStack.pop()

 第 2 段：

    var scope = 'global scope'
    function checkscope() {
      var scope = 'local scope'
      function f() {return scope}
      return f
    }
    checkscope()()

    // 执行上下文栈变化过程
    ECStack.push(globalContext)
    ECStack.push(<checkscope>functionContext)
    ECStack.pop()
    ECStack.push(<f>functionContext)
    ECStack.pop()
    ECStack.pop()

执行上下文中包含哪些内容呢？每个执行上下文包含：

* 变量对象
* 作用域链
* this

下面会一一介绍。

## 执行上下文之变量对象

变量对象（Variable Object）储存了执行上下文中的变量和函数。

在全局上下文中，全局对象即变量对象。在函数上下文中，进入函数时由 arguments 创建活动对象（即当前被激活的变量对象）。

## 执行上下文之作用域链

## 执行上下文之 this


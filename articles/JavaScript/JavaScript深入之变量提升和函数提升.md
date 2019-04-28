# JavaScript 深入之变量提升和函数提升

## 变量提升

    console.log(a) // Uncaught ReferenceError: a is not defined

    console.log(b) // undefined
    var b

    console.log(c) // undefined
    var c = 1

## 函数提升

    foo() // fool

    function foo () {
      console.log('fool')
    }

变量提升和函数提升的区别：函数提升包括函数体，变量提升是 undefined。

## 优先级

    console.log(typeof foo) // function

    var foo = 1
    function foo () {}

**变量和函数同名时，函数提升的优先级更高。**

## 提升原理

为什么 JavaScript 会存在变量提升？

> JavaScript 引擎并非一行一行地分析和执行程序，而是一段一段地分析执行。当执行一段代码的时候，会进行一个“准备工作”，比如第一个例子中的变量提升，和第二个例子中的函数提升。

> 这个“一段一段”中的“段”究竟是怎么划分的呢？

> 其实很简单，就三种，全局代码、函数代码、eval代码。

> 举个例子，当执行到一个函数的时候，就会进行准备工作，这里的“准备工作”，让我们用个更专业一点的说法，就叫做"执行上下文(execution context)"。

> ——来自[《JavaScript 深入之执行上下文栈》](https://github.com/mqyqingfeng/Blog/issues/4)

简单说，JavaScript 引擎在进入执行上下文时，这时候还没执行代码，执行上下文的变量对象会包括函数声明和变量声明。

    function foo(a) {
      var b = 2;
      function c() {}
      var d = function() {};
      
      b = 3;
    }

    foo(1);

在进入执行上下文时，变量对象：

    VO = {
      arguments: {
        0: 1,
        length: 1
      },
      a: 1,
      b: undefined,
      c: reference to function c() {},
      d: undefined
    }

执行完代码之后，变量对象：

    VO = {
      arguments: {
        0: 1,
        length: 1
      },
      a: 1,
      b: 3,
      c: reference to function c() {},
      d: reference to FunctionExpression "d"
    }
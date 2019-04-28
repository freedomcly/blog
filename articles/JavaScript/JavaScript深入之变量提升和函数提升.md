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

变量提升和函数提升的区别：变量提升是 undefined，函数提升包括函数体。

## 优先级

    console.log(typeof foo) // function

    var foo = 1
    function foo () {}

**变量和函数同名时，函数提升的优先级更高。**

## 提升原理

为什么 JavaScript 会存在变量提升？

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
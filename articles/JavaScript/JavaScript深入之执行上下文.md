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

由此可见，两段代码的执行过程不同，执行上下文栈入栈出栈的顺序不同。

## 执行上下文

执行上下文中包含哪些内容呢？每个执行上下文包含：

* 变量对象
* 作用域链
* this

下面会一一介绍。

### 执行上下文之变量对象

变量对象（Variable Object）储存了执行上下文中的变量和函数。

在全局上下文中，全局对象即变量对象。

在函数上下文中，进入函数时根据 arguments 初始化活动对象（即当前被激活的变量对象）。

执行上下文的代码会分成两个阶段进行处理：分析和执行。

变量对象会包括：
* 函数的所有形参。属性值为实参，若实参没有对应值，属性值设为 undefined。
* 函数声明。属性值为函数体，优先级高于变量声明。
* 变量声明。属性值为 undefined。

用上面两段代码举例，checkscope 即将前分析阶段，变量对象均为：

    VO = {
      arguments: {
        length: 0
      },
      scope: undefined,
      f: reference to function f() {return scope}
    }
    
checkscope 执行阶段，变量对象均为：

    VO = {
      arguments: {
        length: 0
      },
      scope: 'local scope',
      f: reference to function f() {return scope}
    }

### 执行上下文之作用域链

函数创建时，函数内部有一个属性 [[scope]]，保存了所有外部执行上下文的变量对象。

为什么 [[scope]] 中不包括函数本身的变量对象呢？函数的变量对象是在执行时才创建，此时还不存在。

    function foo()
      function bar(){...}
      ...
    }

函数创建时，各自的 [[scope]] 为：

    foo.[[scope]] = [
      globalContext.VO
    ]
    
    bar.[[scope]] = [
      fooContext.VO,
      globalContext.VO
    ]

根据上节的内容，函数执行前会建立当前的变量对象（活动对象），把活动对象放在 [[scope]] 前端，形成完整的作用域链。

    scope = [AO].concat([[scope]])

下面用开头的两段代码举例：

    var scope = 'global scope'
    function checkscope() {
      var scope = 'local scope'
      function f() {return scope}
      return f()
    }
    checkscope()

1.checkscope 被创建，保存作用域链到内部属性 [[scope]]

    checkscope.[[scope]] = [
      globalContext.VO
    ]
    
2.执行 checkscope 函数，创建 checkscope 函数执行上下文，checkscope 函数执行上下文被压入执行上下文栈

    ECStack = [
      checkscopeContext,
      globalContext
    ]

3.checkscope 函数并不立即执行，开始准备工作，第一步：复制函数 [[scope]] 属性创建作用域链

    checkscopeContext = {
      scope: checkscope.[[scope]]
    }

4.第二步：用 arguments 创建活动对象，加入形参、函数声明、变量声明

    checkscopeContext = {
      AO: {
        arguments: {length: 0},
        scope: undefined,
        f: reference to function f() {return scope}
      },
      scope: checkscope.[[scope]]
    }
    
5.第三步：将活动对象压入 checkscope 作用域链前端

    checkscopeContext = {
      AO: {
        arguments: {length: 0},
        scope: undefined,
        f: reference to function f() {return scope}
      },
      scope: [AO, ...checkscope.[[scope]]]
    }

6.准备工作完成，开始执行函数，执行过程中修改 AO 的属性值

    checkscopeContext = {
      AO: {
        arguments: {length: 0},
        scope: 'local scope',
        f: reference to function f() {return scope}
      },
      scope: [AO, ...checkscope.[[scope]]]
    }

7.f 函数被创建，保存作用域链到 [[scope]]

    f.[[scope]] = [
      checkscopeContext.VO,
      globalContext.VO
    ]
    
8.执行 f 函数，创建 f 函数执行上下文，被压入执行上下文栈

    ECStack = [
      fContext,
      checkscopeContext,
      globalContext
    ]
    
9.f 函数并不立即执行，开始准备工作

* 复制函数 [[scope]] 属性
* 初始化活动对象，包括形参、函数声明、变量声明
* 把活动对象压入 scope 前端


    fContext = {
      AO: {
        arguments: {length: 0}
      },
      scope: [AO, ...f.[[scope]]]
    }
    
10.执行 f 函数，遇到 scope 变量，进行作用域链 RHS 查找(参考[《JavaScript 深入之作用域链查找和原型链查找》](./JavaScript深入之作用域链查找和原型链查找.md))，在 AO 中没有找到，在`checkscopeContext.VO`中找到，返回 local scope

11.f 函数执行完成，从执行上下文栈中弹出

    ECStack = [
      checkscopeContext,
      globalContext
    ]
    
12.checkscope 函数执行完成，从执行上下文中弹出

    ECStack = [
      globalContext
    ]

第 2 段代码：

    var scope = 'global scope'
    function checkscope() {
      var scope = 'local scope'
      function f() {return scope}
      return f
    }
    checkscope()()

1.checkscope 被创建，保存作用域链到内部属性 [[scope]]

    checkscope.[[scope]] = [
      globalContext.VO
    ]
    
2.执行 checkscope 函数，创建函数执行上下文，入栈

    ECStack = [
      checkscopeContext,
      globalContext
    ]
    
3.checkscope 准备工作，

* 复制函数 [[scope]] 属性
* 初始化活动对象，包括形参、函数声明、变量声明
* 把活动对象压入 scope 前端

    checkscopeContext = {
      AO: {
        arguments: {length: 0},
        scope: undefined,
        f: reference to function f() {return scope}
      },
      scope: [AO, ...checkscope.[[scope]]]
    }
    
4.执行 checkscope，执行过程中修改 AO 的属性值

    checkscopeContext = {
      AO: {
        arguments: {length: 0},
        scope: 'local scope',
        f: reference to function f() {return scope}
      },
      scope: [AO, ...checkscope.[[scope]]]
    }

5.**f 函数创建，保存作用域链到 [[scope]]**

    f.[[scope]] = [
      checkscopeContext.VO,
      globalContext.VO
    ]

6.f 函数被返回，checkscope 函数执行完成，从执行上下文栈中弹出

    ECStack = [
      globalContext
    ]

7.f 函数执行，入栈

    ECStack = [
      fContext,
      globalContext
    ]

8.f 函数准备工作

    fContext = {
      AO: {
        arguments: {length: 0}
      },
      scope: [AO, ...f.[[scope]]]
    }

9.f 函数执行，遇到 scope 变量，进行作用域链查找，在 AO 中没有找到，在`checkscopeContext.VO`中找到，返回 local scope

10.f 函数执行完成，出栈

    ECStack = [
      globalContext
    ]

### 执行上下文之 this


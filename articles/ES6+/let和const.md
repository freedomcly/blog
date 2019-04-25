# let 和 const

var、let 和 const 对比一览

| **项目** | **var** | **let** | **const** |
| :--- | :--- | :--- | :--- |
| 作用域 | 函数作用域 | 块级作用域 | 块级作用域 |
| 变量提升 | 有 | 暂时性死区 | 暂时性死区 |
| 重复声明 | 能 | 不能 | 不能 |
| 重新赋值 | 能 | 能 | 不能 |
| 是否是全局对象（如window）的属性 | 是 | 不是 | 不是 |
| 执行速度 | 慢 | 比var快65% | 比var快65% |

## let 和 const 的特征

**1. 没有变量提升**

什么是变量提升 hoisting ？<br>
在函数作用域或全局作用域使用var声明的变量，无论实际在哪里声明，都会被当成在当前作用域顶部声明。如：

    console.log(value) // undefined
    if (false) {
      var value = 1
    }

let 和 const 没有变量提升：

    console.log(value) // Uncaught ReferenceError: value is not defined
    if (false) {
      let value = 1
    }

**2. 暂时性死区**

在块级作用域起点到 let 或 const 声明的这段区域是暂时性死区，如果在这里访问变量，即使是相对安全的typeof操作符也会引发引用错误。

    console.log(typeof value) // undefined 
    if (true) {
      typeof value // Uncaught ReferenceError: value is not defined
      let value = 1
    }

**3. 不能重复声明**

    let a = 1
    let a = 2 // Uncaught SyntaxError: Identifier 'a' has already been declared

**4. 不是全局对象的属性**

    var a = 1
    let b = 2
    console.log(window.a) // 1
    console.log(window.b) // undefined

**5. const不能重新赋值，但可以为其属性重新赋值**

    const a = {name: 'a'}
    a.name = 'b' // a.name === 'b'
    a = {name: 'b'} // Uncaught TypeError: Assignment to constant variable.

## 一个经典问题

    var consoles = []

    for(var i = 0; i < 6; i++) {
	  consoles[i] = () => {console.log(i)}
    }

    console.log(i) // 6
    consoles[0]() // 6

期望`consoles[i]`能打印`i`，结果都打印了`6`。<br/>
因为`i`存在于全局作用域中，for循环结束后`i`的值为`6`，每个数组项都保存了一个函数：`() => {console.log(i)}`，所以当函数执行时会直接打印当前i的值。

这个问题其中一种解决方法是闭包：

    var consoles = []

    for(var i = 0; i < 6; i++) {
	  consoles[i] = (num => {
		return () => console.log(num)
	  })(i)
    }

    console.log(i)
    consoles[0]()
    
另一种闭包写法：

    var consoles = []

    for(var i = 0; i < 6; i++) {
      (function(num) {
      	consoles[i] = () => console.log(num)
      })(i)
    }

    console.log(i)
    consoles[0]()
    consoles[1]()

闭包能解决这个问题的原因是：闭包中的变量（或参数）能在闭包执行完成之后依然存在于内存中。

let也可以解决这个问题：

    var consoles = []

    for(let i = 0; i < 6; i++) {
	  consoles[i] = () => {console.log(i)}
    }

    consoles[0]() // 6
    
为什么let可以解决呢？这里有两个问题：

* let不能重复声明，但这里每次循环都声明了一次`let i`
* 数组项中保存的函数依然是`() => {console.log(i)}`，取到的是哪里的i值

for中使用var和let，底层有不同的处理方式，详情见http://www.ecma-international.org/ecma-262/6.0/#sec-for-statement-runtime-semantics-labelledevaluation

## 最佳实践

默认使用const，在确实需要改变变量的值时使用let。

## 变量声明的六种方法：

* var
* function
* let (ES6)
* const (ES6)
* import (ES6)
* class (ES6)

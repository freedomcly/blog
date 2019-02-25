#
let和const

| **项目** | **var** | **let** | **const** |
| :--- | :--- | :--- | :--- |
| 作用域 | 函数作用域 | 块级作用域 | 块级作用域 |
| 变量提升 | 有 | 暂时性死区 | 暂时性死区 |
| 重复声明 | 能 | 不能 | 不能 |
| 重新赋值 | 能 | 能 | 不能 |
| 是否是顶层对象（如window）的属性 | 是 | 不是 | 不是 |
| 执行速度 | 慢 | 比var快65% | 比var快65% |

## 名词解释

#### 变量提升hoisting

在函数作用域或全局作用域使用var声明的变量，无论实际在哪里声明，都会被当成在当前作用域顶部声明。

#### 暂时性死区temporal dead zone

let和const声明的变量不会被提升到作用域顶部，如果在声明之前访问这些变量，即使是相对安全的typeof操作符也会引发引用错误。

console.log(typeof value) // undefined
if (condition) {
console.log(typeof value) // 引用错误！
let value = 'blue'
}

## 最佳实践

默认使用const，在确实需要改变变量的值时使用let。

## 变量声明的六种方法：

* var
* function
* let
* const
* import
* class

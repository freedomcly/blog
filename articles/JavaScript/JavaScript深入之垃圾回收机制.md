# JavaScript 深入之垃圾回收机制

JavaScript 自动进行垃圾回收，垃圾收集器周期性地运行。

垃圾回收策略：标记清除和引用计数。

## 标记清除

当变量离开执行上下文时，标记“离开”，以便垃圾收集器进行回收（闭包例外）。

```javascript
function fn(){
  var a = { count: 10 } // 被标记，进入环境 
  var b = { count: 20 } // 被标记，进入环境
}
fn() // 执行完毕之后，被标记，离开环境
```

## 引用计数

当内存空间被引用次数为 0 时，可回收。

```javascript
function fn(){
  var a = { count: 10 } // 资源 { count: 10 } 被引用次数为 1
  a = { count: 20 } // 资源 { count: 20 } 被引用次数为 1，资源 { count: 10 } 被引用次数为 0，等待回收
  // do someThing
}
fn() // 资源 { count: 20 } 被释放
```

这种策略会产生循环引用，容易引发内存泄露。

```javascript
function fn(){
  var a = { count: 10 }
  var b = { count: 20 }
  a.param = b // b 的引用次数为 2
  b.param = a // a 的引用次数为 2
}
fn() // a、b 的引用次数为 1
```

## 最佳实践

对于不再使用的全局变量、闭包中的变量、循环引用的变量，应该及时进行**解除引用**，有利于垃圾回收。

```javascript
variable = null
```
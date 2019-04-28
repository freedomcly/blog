# JavaScript Interview

## 1

    ['1','2','3'].map(parseInt) // [1, NaN, NaN]
    
    ['1','2','3'].map(value => parseInt(value)) // [1, 2, 3]
    
map默认传递三个参数：value，index，array。parseInt接收两个参数：string，radix。

    parseInt('1', 0) // 1
    parseInt('2', 1) // NaN
    parseInt('3', 2) // NaN
    

## 2

    function fn1() {
      for(var i = 0; i < 4; i++) {
        var timer = setTimeout(function(i){
          console.log(i)
          clearTimeout(timer)
        }, 10, i)
      }
    }
    fn1()
    
    // 10ms后，打出0 1 2
    
问题：
* 为什么10ms后一次性打出
* 为什么不是`3 3 3 3`
* 为什么不是`0 1 2 3`


1. 由于setTimeout第一个参数的函数会异步执行，所以是10ms后一次性打出结果。
2. 由于setTimeout第三个参数会记录i的值，所以是`0 1 2...`。
3. 由于timer被不断地重写，最后会保存i为3的setTimeout返回值，10ms后第一次执行i为0时的异步函数，其中`clearTimeout(timer)`会清除i为3的setTimeout，所以是`0 1 2`。


## 3

    function fn2() {
      for (var i = 0; i < 4; i++) {
        var timer = setInterval(function(i, tc) {
          console.log(i)
          clearInterval(tc)
        }, 2000, i, timer)
      }
    }
    fn2()
    
    // 2s后打出 0 1 2 3
    // 之后每隔2s打出3
    
这里不会存在上面那种清除最后一个setTimeout的情况，因为timer被当作setInterval的第四个参数。但第一次传的timer是undefined，第二次传的timer是第一次的setInterval的返回值。

因此，第二次清除了i=0的setInterval，第三次清除了i=1的setInterval，第四次清除了i=2的setInterval，i=3的setInterval一直没被清除，因此会一直执行下去。

## 4

    var a = {n: 1}
    var b = a
    a.x = a = {n: 2}
    console.log(a.x) // undefined
    console.log(b.x) // {n: 2}
    
连等是从右到左。

## 5

```
var promise = new Promise(resolve => {
  console.log(1)
  resolve()
  console.log(2)
})

promise.then(data => {
  console.log(3)

  Promise.resolve().then(() => {
    console.log(4)
  })
})

setTimeout(() => {
  console.log(5)
})

console.log(6)

// 同步
// 1
// 2
// 6

// 异步microtask队列
// 3
// 4

// 异步macrotask队列
// 5

```

## 6

`let`暂时性死区。

```
let a = 1

{
  console.log(a) // ReferenceError
  let a = 2
}
```

```
let a = 1

(function() {
  console.log(a) // TypeError
  let a = 2
})()

```

## 7

实现`instanceof`。

```
function myInstanceof (obj, cla) {
  if (!obj.__proto__) return false
  if (obj.__proto__.constructor === cla) {
    return true
  } else {
    if (obj.__proto__.__proto__) {
      return myInstanceof(obj.__proto__, cla)
    }
  }
}

console.log(myInstanceof([], Array))
console.log(myInstanceof([], Object))
```
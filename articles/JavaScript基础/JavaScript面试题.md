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



# JavaScript异步原理和异步解决方案

## 异步、并发、并行

* 异步：*现在*传入的函数*未来*才会执行。
* 并发：两个或多个事件链随时间发展*交替执行*，以至于从更高的层次来看，就像是同时在运行（尽管在任意时刻只处理一个事件）。
* 并行：同时执行。

## EventLoop

JavaScript是单线程语言，只能做到异步并发，不能真正实现并行。JavaScript的异步原理是基于EventLoop模型：

    while (queue.waitForMessage()) {
      queue.processNextMessage()
    }
    
当主线程空闲时，EventLoop机制会从消息队列中取出一个消息执行。当这个消息执行完毕后主线程又变为空闲状态，继续取出下一个消息，循环下去。

一个最简单的栗子：

    console.log(1)
    setTimeout(() => {
      console.log(3)
    }, 0)
    console.log(2)

* 执行console.log(1)
* 0秒后把() => {console.log(3)}放入消息队列中
* 执行console.log(2)
* 主线程空闲，取出消息队列中第一个消息
* 执行console.log(3)

因此，JavaScript确实只有一个解释执行的线程，而通过EventLoop模型和消息队列实现了异步并发，也就是*交替执行*。

## 更复杂的情况：macro-task和micro-task

JavaScript任务可以分为两种类型：

* macro-task（宏任务）：script整体代码、setTimeout、setInterval、setImmediate、I/O、UI rendering
* micro-task（微任务）：process.nextTick、Promises（浏览器实现的原生Promise）、Object.observe、MutationObserver

[vue中的nextTick实现原理](https://github.com/freedomcly/awesome-vue-source/blob/master/docs/nextTick.md)

一个栗子：

    setImmediate(function(){
      console.log(8);
    },0)

    setTimeout(function(){
      console.log(7);
    },0)

    new Promise(function(resolve){
      console.log(1);
      resolve();
      console.log(2);
    })
      .then(function(){
        console.log(6)
      })

    console.log(3);

    process.nextTick(function(){
      console.log(5);
    })

    console.log(4);
    
    // 1 2 3 4 5 6 7 8
    // 优先级 process.nextTick > Promise > setTimeout > setImmediate
    // macro queue: setTimeout / setImmediate
    // micro queue: process.nextTick / Promise

宏任务可以认为是一个个大型的任务，setTimeout等是把函数任务放在下一个宏任务中执行。微任务是宏任务之间的小型任务队列，Promise把任务放在下一个微任务队列，可以在下一个宏任务之前执行。

可以理解为：
* （宏任务（微任务））（宏任务（微任务））……
* （宏任务）（微任务）（宏任务）（微任务）……

## 异步解决方案

### 1.回调函数

    // get是一个异步任务
    function get(func) {
      setTimeout(() => {
        func()
      }, 2000)
    }

    get(function() {
      console.log('get1')
      get(function() {
          console.log('get2')
          get(function() {
            console.log('get3')
          })
      })
    })

名副其实的回调地狱。

缺点：

* 没有可读性，不符合人脑思维
* 信任问题。可能不是异步；调用回调过早；调用回调过晚；调用回调次数过多或过少……
        
### 2.Promise

    function get() {
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          resolve(1)
        }, 2000)
      })
    }

    get().then(() => {
      console.log('get1')
      return get()
    }).then(() => {
      console.log('get2')
      return get()
    }).then(() => {
      console.log('get3')
    })
    
Promise有两个优势：
* 拉平“洋葱结构”，增强可读性
* 解耦异步本身的逻辑和响应逻辑。在例子中，get对应异步本身的逻辑，then()中的函数对应响应逻辑。而回调函数方式，func会耦合在get中。

### 3.Async/Await

    async function get(param) {
      return await new Promise((resolve, reject) => {
        setTimeout(() => {
          console.log(param)
          resolve(1)
        }, 2000)
      })
    }

    // 串行
    get().then(() => {
      console.log(1)
      return get()
    }).then(() => {
      console.log(2)
      return get()
    }).then(() => {
      console.log(3)
    })

    // 并发
    get(1)
    get(2)
    get(3)

Async/Await模式可以把“洋葱结构”拍平，就像上面的例子中的并发。Await等待的对象必须是Promise对象。

## 参考文章

* [Promises/A+](https://promisesaplus.com/)
* [【翻译】Promises/A+规范](http://www.ituring.com.cn/article/66566)
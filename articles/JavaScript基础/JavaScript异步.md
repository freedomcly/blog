# JavaScript异步

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

## macro-task和micro-task

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

## setTimeout和requestAnimationFrame

* setTimeout把代码放在异步队列执行，不适合处理渲染相关的操作，会有掉帧现象（某一帧没有渲染，下一帧把两次的结果一起渲染了）
* requestAnimationFrame在渲染前执行，严格遵守“执行一次渲染一次”，不会掉帧

感觉requestAnimationFrame和Vue中的Vue.nextTick的思路有点像，都是在渲染前执行，但实现原理不同。

## 异步解决方案

* 回调函数
* 事件发布订阅模式
* Promise模式
* Async Await
* 其他流程控制库
* Generator

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

由于回调函数的执行处在另一个函数中，不能确定回调函数是否是异步，是否只调用一次等情况。

缺点：

* 缺乏可读性，不符合人脑思维
* 信任问题。可能不是异步；调用回调过早；调用回调过晚；调用回调次数过多或过少……

### 2.发布订阅模式

发布订阅模式实现异步的例子：

    var event = {
      clientList: [],
      listen: function(key, fn) {
        if (!this.clientList[key]) {
          this.clientList[key] = []
        }
        this.clientList[key].push(fn)
      },
      trigger: function() {
        var key = Array.prototype.shift.call(arguments)
        var fns = this.clientList[key]

        if(!fns || fns.length === 0) {
          return false
        }

        for(var i = 0, fn; fn = fns[i++]; ) {
          fn.apply(this, arguments)
        }
      }
    }

    var installEvent = function(obj) {
      for(var i in event) {
        obj[i] = event[i]
      }
    }

    // test
    var obj = {}
    installEvent(obj)

    obj.listen('foo', data => {
      console.log(data)
    })

    setTimeout(() => {
      obj.trigger('foo', 'bar')
    }, 3000)


### 3.Promise

* [Promises/A+](https://promisesaplus.com/)
* [https://github.com/then/promise](https://github.com/then/promise)


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
    
Promise所在函数只管返回状态，不需要关注谁在侦听，也不需要关注之后怎么执行。

Promise有两个优势：
* 拉平“洋葱结构”，增强可读性
* 解耦异步本身的逻辑和响应逻辑。在例子中，get对应异步本身的逻辑，then()中的函数对应响应逻辑。而回调函数方式，func会耦合在get中。

#### 实现一个简单的Promise

换了一个名字：Promisey。

简单来说，先执行异步的`fn`，在异步`fn`还没完成时，主线程执行`.then`函数，把异步`fn`完成后应该执行的函数推到一个数组中`deferreds`，当`fn`完成时调用`resolve`，递归执行`deferreds`中的函数们。

Promisey还应该添加一些功能：

* 链式调用
* 强制异步
* 引入状态
* 串行（`then`返回一个新的`Promise`）
* 失败处理
* 异常处理

```
export function Promisey (fn) {
    var state = 'pending'
    var value = null
    var deferreds = []

    this.then = function (onFulfilled, onRejected) {
        return new Promisey(function(resolve, reject) {
            handle({
                onFulfilled: onFulfilled || null,
                onRejected: onRejected || null,
                resolve: resolve,
                reject: reject
            })
        })
    }

    function handle (deferred) {
        if (state === 'pending') {
            deferreds.push(deferred)
            return
        }

        var cb = state === 'fulfilled' ? deferred.onFulfilled : deferred.onRejected
        var ret

        if (cb === null) {
            cb = state === 'fulfilled' ? deferred.resolve : deferred.reject
            cb(value)
            return
        }

        try {
            ret = cb(value)
            deferred.resolve(ret)
        } catch (e) {
            deferred.reject(e)
        }
    }

    function resolve (newValue) {
        if (newValue && (typeof newValue === 'object' || typeof newValue === 'function')) {
            var then = newValue.then
            if (typeof then === 'function') {
                then.call(newValue, resolve)
                return
            }
        }

        value = newValue
        state = 'fulfilled'
        finale()
    }

    function reject (reason) {
        state = 'rejected'
        value = reason
        finale()
    }

    function finale() {
        setTimeout(function () {
            deferreds.forEach(function(deferred) {
                handle(deferred)
            })
        }, 0)
    }

    fn(resolve, reject)
}
```


### 4.Async/Await

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

### 5.其他流程控制库

* [async](https://github.com/caolan/async)
* step
* wind

## 参考资料

* [剖析 Promise 之基础篇](https://tech.meituan.com/promise_insight.html)
# JavaScript 深入之异步原理

众所周知，JavaScript 是单线程、无阻塞、异步并发的。

比如：发送 ajax 请求后，不会阻塞之后代码的运行，当执行栈中代码运行结束且 ajax 请求返回成功，之前的回调函数才会执行。

    $.ajax({url: 'APIURL'}).done(function(){console.log('done')})
    
    console.log('pending')

    // pending
    // done

有几个名词需要解释一下：

* 异步：**现在**传入的函数**未来**才会执行
* 并发：两个或多个事件随时间发展**交替执行**，以至于从更高的层次来看，就像是同时在运行（尽管在任意时刻只处理一个事件）
* 并行：同时执行

那么，JavaScript 中异步是如何实现的呢？

在浏览器和 Node 中，异步的实现方式并不相同。

## 浏览器中 JavaScript 异步原理

浏览器中异步的实现需要以下几部分通力配合：

* call stack
* callback queue
* event loop

### 1.call stack

即之前在[《JavaScript 深入之执行上下文》](./JavaScript深入之执行上下文.md)中提到的执行上下文栈。

代码执行从全局上下文入栈开始，一直到代码结尾执行栈清空。

### 2.callback queue

异步事件执行完成后，callback 会被放入 callback queue，当 call stack 被清空，也就是 JavaScript 线程空闲时，**callback queue 中的第一个 callback 会进入 call stack 开始执行**（浏览器中一个一个执行，NodeJS 中一次执行完毕），之后依次类推。

举个栗子，

    setTimeout(function(){
      console.log('async')
    }, 0)

    const timer = Date.now()
    while(Date.now() - timer < 3000) {}

发现至少 3 秒后才会打印 async。与以下代码无太大差别：

    setTimeout(function(){
      console.log('async')
    }, 3000)

    const timer = Date.now()
    while(Date.now() - timer < 3000) {}

setTimeout 的含义是：n 秒后，把回调函数放入 callback queue 中。上面两段代码，由于执行栈中本来就有 3 秒的任务要执行，0 秒后放入和 3 秒后放入最终表现差不多。

### 3.event loop

当执行栈被清空时，event loop 会查看 callback queue 中是否有 callback 等待执行，若有则一个一个地按照先进先出的顺序执行，若无则隔一小段时间再查看一次。

    while (queue.waitForMessage()) {
      queue.processNextMessage();
    }

event loop 流程图：

![](/assets/eventloop.jpeg)

浏览器中有两个任务队列
* macro-task（宏任务）：script 整体代码、setTimeout、setInterval、I/O、UI rendering
* micro-task（微任务）：Promises（浏览器实现的原生Promise）、MutationObserver

可以理解为：（宏任务）（微任务）（宏任务）（微任务）……

以上为异步实现原理，说了这么多，举个简单的例子：

    function foo() {
      const timer = Date.now()
      while(Date.now() - timer < 3000) {}
    }

    function bar() {
      console.log('bar')
    }

    setTimeout(() => {
      console.log('async')
    }, 0)

    foo()
    bar()

这段代码我们都知道它的打印结果。它的执行过程是：

1.全局执行上下文入栈

    ECStack = [
      globalContext
    ]

2.setTimeout 函数入栈

    ECStack = [
      setTimeoutContext,
      globalContext
    ]

3.setTimeout 函数出栈

    ECStack = [
      globalContext
    ]

4.foo 函数入栈

    ECStack = [
      fooContext,
      globalContext
    ]

3.0 秒（理论上是 0 秒，实际最低 4 毫秒）后，匿名函数放入 callback queue。

    CallbackQueue = [
      anonymous
    ]

4.foo 函数 3 秒后出栈。**此时不会引发 EventLoop，因为执行栈中还有 globalContext**。

    ECStack = [
      globalContext
    ]

5.bar 函数入栈，console.log 函数入栈

6.console.log 函数出栈，bar 函数出栈，全局上下文出栈

7.开始 EventLoop，把 CallbackQueue 中的第一个函数放入执行栈执行，其中 console.log 入栈

    CallbackQueue = []
    
    ECStack = [
      console.log,
      anonymous
    ]

8.console.log 出栈，anonymous 出栈。结束。

**即使异步 callback 早已进入 callback queue，异步 callback 也不会插入到同步代码的多个函数执行间隙去执行。原因是若全局执行上下文一直在执行栈中，执行栈没有清空，不会触发 event loop。**

可以使用这个工具动态观察异步和执行栈的情况：http://latentflip.com/loupe/。

## Node 中异步

Node 中的异步和浏览器中的异步真的不同吗？

```javascript
setTimeout(() => {
  console.log('setTimeout1')
  Promise.resolve().then(() => {
    console.log('Promise1')
    Promise.resolve().then(() => {
      console.log('Promise5')
    })
  })
  Promise.resolve().then(() => {
    console.log('Promise2')
    Promise.resolve().then(() => {
      console.log('Promise6')
    })
  })
})

setTimeout(() => {
  console.log('setTimeout2')
  Promise.resolve().then(() => {
    console.log('Promise3')
    Promise.resolve().then(() => {
      console.log('Promise7')
    })
  })
  Promise.resolve().then(() => {
    console.log('Promise4')
    Promise.resolve().then(() => {
      console.log('Promise8')
    })
  })
})
```

以上代码在浏览器中打印

```
setTimeout1
Promise1
Promise2
Promise5
Promise6
setTimeout2
Promise3
Promise4
Promise7
Promise8
```

然而在 NodeJS（v8.9.4）有时打印与浏览器一样，有时打印如下：

```
setTimeout1
setTimeout2
Promise1
Promise2
Promise3
Promise4
Promise5
Promise6
Promise7
Promise8
```

例子中的不同之处在于，**浏览器 event loop 的 Macrotask queue 在每次循环中只会读取一个任务到执行栈，Microtask queue 是一次性读取。NodeJS 中 Macrotask queue 和 Microtask queue 中的任务都会一次性读取执行完**，在新版本中也有可能像浏览器中那样执行。

Node 中宏任务和微任务：

* macro-task（宏任务）：setTimeout、setInterval、setImmediate、I/O
* micro-task（微任务）：Promises（浏览器实现的原生Promise）、process.nextTick



另外，Node 中 event loop 的宏任务执行过程分为六个阶段：

* timers：执行 setTimeout() 和 setInterval() 中到期的 callback
* I/O callbacks：上一轮循环中有少数的 I/O callback 会被延迟到这一轮的这一阶段执行
* idle, prepare：仅内部使用
* poll：最为重要的阶段，执行I/O callback，在适当的条件下会阻塞在这个阶段
* check：执行 setImmediate 的 callback
* close callbacks：执行 close 事件的 callback，例如socket.on("close", func)

如图：

       ┌───────────────────────┐
    ┌─>│        timers         │
    │  └──────────┬────────────┘
    │  ┌──────────┴────────────┐
    │  │     I/O callbacks     │
    │  └──────────┬────────────┘
    │  ┌──────────┴────────────┐
    │  │     idle, prepare     │
    │  └──────────┬────────────┘      ┌───────────────┐
    │  ┌──────────┴────────────┐      │   incoming:   │
    │  │         poll          │<─────┤  connections, │
    │  └──────────┬────────────┘      │   data, etc.  │
    │  ┌──────────┴────────────┐      └───────────────┘
    │  │        check          │
    │  └──────────┬────────────┘
    │  ┌──────────┴────────────┐
    └──┤    close callbacks    │
       └───────────────────────┘

## 参考

* https://vimeo.com/96425312
* https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop
* https://cnodejs.org/topic/5a9108d78d6e16e56bb80882
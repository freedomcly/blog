# JavaScript 深入之异步原理

众所周知，JavaScript 是单线程、无阻塞、异步并发的。

比如：发送 ajax 请求后，不会阻塞之后代码的运行，当执行栈中代码运行结束且 ajax 请求返回成功，之前的回调函数才会执行。

    &.ajax({url: 'APIURL'}).done(function(){console.log('done')})
    
    console.log('pending')

    // pending
    // done

有几个名词需要解释一下：

* 异步：**现在**传入的函数**未来**才会执行
* 并发：两个或多个事件随时间发展**交替执行**，以至于从更高的层次来看，就像是同时在运行（尽管在任意时刻只处理一个事件）
* 并行：同时执行

那么，JavaScript 中异步是如何实现的呢？

## 异步原理

异步的实现需要以下几部分通力配合：

* call stack
* callback queue
* event loop

### 1.call stack

即之前在[《JavaScript 深入之执行上下文》](./JavaScript深入之执行上下文.md)中提到的执行上下文栈。

代码执行从全局上下文入栈开始，一直到代码结尾执行栈清空。

### 2.callback queue

异步事件执行完成后，callback 会被放入 callback queue，当 call stack 被清空，也就是 JavaScript 线程空闲时，callback queue 中的第一个 callback 会进入 call stack 开始执行，之后依次类推。

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

![](/assets/eventloop.png)




## 参考

* https://vimeo.com/96425312
* http://latentflip.com/loupe/
* https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop
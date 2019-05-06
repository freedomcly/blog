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

即之前在[《JavaScript 深入之执行上下文》](./JavaScript深入之执行上下文.md)中提到的 JavaScript 的执行上下文栈。



### 2.callback queue

异步事件执行完成后，callback 会被放入 callback queue，当 call stack 被清空，也就是 JavaScript 线程空闲时，callback queue 中的第一个 callback 会进入 call stack 开始执行，之后依次类推。

### 3.event loop

当执行栈被清空时，event loop 会查看 callback queue 中是否有 callback 等待执行，若有则一个一个地按照先进先出的顺序执行，若无则隔一小段时间再查看一次。

    while (queue.waitForMessage()) {
      queue.processNextMessage();
    }

![](/assets/eventloop.png)




## 参考

* https://vimeo.com/96425312
* http://latentflip.com/loupe/
* https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop
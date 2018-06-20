# JavaScript异步原理

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
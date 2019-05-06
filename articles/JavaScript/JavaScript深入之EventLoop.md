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

## 异步原理



![](/assets/eventloop.png)

之前在[《JavaScript 深入之执行上下文》](./JavaScript深入之执行上下文.md)中有提到 JavaScript 的执行上下文栈。


## 参考

* https://vimeo.com/96425312
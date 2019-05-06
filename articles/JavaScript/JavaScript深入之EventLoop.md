# JavaScript 深入之 EventLoop

众所周知，JavaScript 是单线程、无阻塞、异步并发的。

比如：发送 ajax 请求后，不会阻塞之后代码的运行，当执行栈中代码运行结束且 ajax 请求返回成功，之前的回调函数才会执行。

    &.ajax({url: 'APIURL'})
      .done(function(){console.log('done')})
    
    console.log('pending')

![](/assets/eventloop.png)

之前在[《JavaScript 深入之执行上下文》](./JavaScript深入之执行上下文.md)中有提到 JavaScript 的执行上下文栈。
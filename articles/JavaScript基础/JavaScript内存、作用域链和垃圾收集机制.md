# JavaScript内存和垃圾收集机制

## 内存

基本数据类型储存在数据结构栈，变量中保存的是值本身。
引用数据类型储存在数据结构堆，变量中保存的是指向堆的指针。

复制基本数据类型时，如图，直接复制值本身。

![](/assets/basic-data-type.png)

复制引用数据类型时，如图，只复制变量保存的指针。

![](/assets/reference-data-type.png)

为什么基本数据类型保存在栈，引用类型保存在堆？

**因为基本数据类型数据占用大小能预期，引用类型数据大小不能确定。**

## 参数传递

函数的参数按值传递。

    // example 1
    var num = 0;
    function plus (num) {
      num++;
    }
    plus(num);
    console.log(num); // 0
    
    // example 2
    var obj = {name: 'maomao'};
    function resetName (obj) {
      obj.name = '';
    }
    resetName(obj);
    console.log(obj.name); // ''
    
从两个例子可以看到，JavaScript传参就是复制的过程。如果参数是基本数据类型，复制值本身，如果参数是引用数据类型，复制指针。

因此example1中函数外部的基本数据类型num没有改变，而example2中函数外部的引用数据类型obj的属性name被改变。

**在函数内部不要直接操作引用类型的参数，应该操作它的一份深拷贝。**

## 垃圾收集

* JavaScript自动进行垃圾回收。
* 垃圾收集器会周期性地执行垃圾回收。
* 离开作用域时，该作用域中的值被标记为可回收。
* 标记清除是主流的垃圾回收算法；引用计数是另一种垃圾回收算法，但会导致循环引用的性能问题。
* 应该及时进行**变量引用的解除**，全局对象、全局对象的属性、循环引用变量，即`foo = null`。

## JavaScript中数据结构

* 栈：基本数据类型；环境栈
* 堆：引用数据类型
* 队列：异步消息队列
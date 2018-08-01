# JavaScript内存、作用域链和垃圾收集机制

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

## 作用域链

    var globalName = 'global';
    console.log(globalName);
    function contextA(params) {
      var contextAName = 'contextA';
      console.log(contextAName, globalName);
      function contextB(params) {
        var contextBName = 'contextB';
        console.log(contextBName, contextAName, globalName);
      }
      contextB();
    }
    contextA();
    
    // global
    // contextA global
    // contextB contextA global

* ES6之前，JavaScript中只有全局作用域和函数作用域。
* 在最初作用域链为global。
* 调用`contextA()`，进入函数contextA中，作用域链为contextA => global。
* 进入函数contextB中，作用域链为contextB => contextA => global。
* 退出函数contextB，作用域链为contextA => global。
* 退出函数contextA，作用域链为global。
* 变量标识符沿作用域链一级一级搜索，从作用域链前端向全局作用域的方向，直到找到变量为止。
* 作用域链和执行环境有助于确定何时释放内存。就像上面的栗子，当函数退出contextB，作用域链变为contextB => global时，就表示执行环境contextB可以被释放。

更进一步的解释：
* 当函数第一次被调用时，会创建一个执行环境（execution context）及相应的作用域链，并把作用域链赋值给特殊的内部属性[[scope]]。
* 然后，使用this、arguments和其他命名参数来初始化函数的活动对象。
* 函数外层（函数声明的外层，而不是函数调用的外层）每个作用域都有一个保持变量的对象——变量对象。最外层是全局变量对象。
* 函数中变量的寻找沿着作用域链（scope chain）。

![](/assets/scope-chain.jpg)

## 闭包

闭包与上面所说的作用域链紧密相关。

* 闭包保存着闭包自身的活动对象，以及闭包包含函数的整条作用域链。
* 闭包包含函数执行完成后其活动对象不会被销毁，还存在于内存中。因为它还被闭包的作用域链所引用。
* 只有当闭包被设置为null，闭包包含函数的活动对象以及整条作用域链才会被销毁。 

![](/assets/scope-chain-closure.png)

## 垃圾收集

* JavaScript自动进行垃圾回收。
* 垃圾收集器会周期性地执行垃圾回收。
* 离开作用域时，该作用域中的值被标记为可回收。
* 标记清除是主流的垃圾回收算法；引用计数是另一种垃圾回收算法，但会导致循环引用的性能问题。
* 应该及时进行**变量引用的解除**，全局对象、全局对象的属性、循环引用变量，即`foo = null`。

## JavaScript中的数据结构

* 栈：基本数据类型；环境栈
* 堆：引用数据类型
* 队列：异步消息队列
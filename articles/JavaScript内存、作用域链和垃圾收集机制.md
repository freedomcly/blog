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

* ES5和ES5之前，JavaScript中只有全局作用域和函数作用域。
* 在最初作用域链为global。
* 进入函数contextA中，作用域链为contextA => global。
* 进入函数contextB中，作用域链为contextB => contextA => global。
* 退出函数contextB，作用域链为contextA => global。
* 退出函数contextA，作用域链为global。
* 变量标识符沿作用域链一级一级搜索，从作用域链前端向全局作用域的方向，直到找到变量为止。
* 作用域链和执行环境有助于确定何时释放内存。就像上面的栗子，当函数退出contextB，作用域链变为contextB => global时，就表示执行环境contextB可以被释放。

## 垃圾收集

* JavaScript自动进行垃圾回收。
* 垃圾收集器会周期性地执行垃圾回收。
* 离开作用域时，该作用域中的值被标记为可回收。
* 标记清除是主流的垃圾回收算法；引用计数是另一种垃圾回收算法，但会导致循环引用的性能问题。
* 应该及时进行**变量引用的解除**，全局对象、全局对象的属性、循环引用变量，即`foo = null`。

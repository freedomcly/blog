# JavaScript闭包

闭包概念：
* **有权访问另一个函数作用域中的变量的函数**（《JavaScript高级程序设计》）
* **当函数可以记住并访问所在的词法作用域时，就产生了闭包，即使函数是在当前词法作用域之外执行**（《你不知道的JavaScript上》）

常见闭包形式，在一个函数内部声明另一个函数。

可以参考：[闭包原理](./JavaScript内存、作用域链和垃圾收集机制.md#闭包)

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
* 当函数第一次被调用时，会创建一个执行环境（execution context）及相应的作用域链，并把作用域链赋值给执行环境特殊的内部属性[[scope]]。
* 然后，使用this、arguments和其他命名参数来初始化函数的活动对象。
* 函数外层（**函数声明的外层，而不是函数调用的外层**）每个作用域都有一个保存变量的对象——变量对象。最外层是全局变量对象。
* 函数中变量的寻找沿着作用域链（scope chain）。

![](/assets/scope-chain.jpg)

## 闭包

闭包与上面所说的作用域链紧密相关。

* 闭包保存着闭包自身的活动对象，以及闭包包含函数的整条作用域链。
* 若闭包存在于内存中（被return），闭包包含函数执行完成后其活动对象不会被销毁，还存在于内存中。因为它还被闭包的作用域链所引用。
* 只有当闭包被设置为null，闭包包含函数的活动对象以及整条作用域链才会被销毁。 

![](/assets/scope-chain-closure.png)

## bind polyfill

    Function.prototype.bind = function(context) {
      const that = this;
      const argus1 = Array.prototype.slice.call(arguments, 1);

      return function() {
        const argus2 = Array.prototype.slice.apply(arguments);
        return that.apply(context, argus1.concat(argus2));
      }
    }
    
    // test
    function getName (){
      console.log(arguments);
      return this.name;
    }
    
    var realGetName = getName.bind({name: 233}, 11, 22);
    realGetName(33, 44, 55);


- [x] 是否需要`return self.apply(...)`？return更好一些，能拿到`return this.name`

## curry polyfill

    Function.prototype.curry = function () {
      const that = this
      const args = Array.prototype.slice.call(arguments)
      return function () {
        const innerArgs = Array.prototype.slice.call(arguments)
        const finalArgs = args.concat(innerArgs)
        return that.apply(null, finalArgs)
      }
    }

    // test
    function test(a, b, c, d, e){console.log(a,b,c,d,e)}
    var testA = test.curry(1)
    var testB = testA.curry(2, 3)
    testB(4, 5)

- [ ] 为什么要curry

## repeat

使用JS实现一个repeat方法：

    function repeat (func, times, wait) {}
    const repeatFunc = repeat(alert, 4, 3000)

调用这个repeatedFunc("hello world")，会alert4次hello world，每次间隔3秒。

    function repeat (func, times, wait) {
      let time = times;
      let self = this;
      let repeatFunc = function() {
        if(time <= 0) return;
        let argus = Array.prototype.slice.apply(arguments);
        setTimeout(() => {
          func.apply(self, argus);
          time--;
          repeatFunc.apply(self, argus);
        }, wait);
      };
      return repeatFunc;
    }
    
    // test
    const repeatFunc = repeat(console.log, 3, 3000);
    repeatFunc('hello world');
    
- [x] 如何实现连续调用？在repeat函数中定义具名函数repeatFunc，在repeatFunc中调用自己。
- [x] 注意参数的传递。`'hello world'`继续传递给后续的repeatFunc。

## throttle

    function throttle(func, delay) {
      let self = this;
      let timer;
      let currentArgus;
      return function() {
        // 更新为最后一次调用的参数
        currentArgus = Array.prototype.slice.apply(arguments);
        if(!timer) {
          timer = setTimeout(() => {
            func.apply(self, currentArgus);
            timer = null;
          }, delay);
        }
      }
    }

    // test
    let realFunc = throttle(console.log, 3000);
    realFunc('hello');
    realFunc('hello');
    realFunc('hello world');

注意和debounce的区别，这个[demo](https://codepen.io/llh911001/pen/XmGYKV?editors=1010)很不错。

* 记录`currentArgus`
* 调用后置空`timer`

## debounce

    function debounce(func, delay) {
      let self = this;
      let timer;
      return function() {
        // 更新为最后一次调用的参数
        const currentArgus = Array.prototype.slice.apply(arguments);
        if(timer) {
          clearTimeout(timer);
        }
        timer = setTimeout(() => {
          func.apply(self, currentArgus);
        }, delay);
      }
    }
    
    // test
    let realFunc = debounce(console.log, 3000);
    realFunc('hello');
    realFunc('hello');
    realFunc('hello world');

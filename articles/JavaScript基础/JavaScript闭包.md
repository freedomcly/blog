# 关于JavaScript闭包

闭包概念：**有权访问另一个函数作用域中的变量的函数**。常见闭包形式，在一个函数内部创建另一个函数。

可以参考：[闭包原理](./JavaScript内存、作用域链和垃圾收集机制.md#闭包)

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

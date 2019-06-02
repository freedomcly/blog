# ES6 之模块化

## 模块化历史

* 无模块，通过文件拆分或立即执行函数实现简陋的模块化。
* CommonJS，同步模块加载。
* AMD，异步模块加载。
* CMD，异步模块加载。按需加载，依赖就近，不需要在最初就加载执行所有依赖模块。
* ES6 modules。

## CommonJS 和 ES6 modules

区别一：CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。

也就是说 CommonJS 输出值不会改变，ES6 有可能会改变。

区别二：CommonJS 模块是运行时加载，ES6 模块是编译时输出接口。

区别三：循环加载。[demos](https://github.com/freedomcly/learn-module)

CommonJS 一旦出现某个模块被“循环加载”，就只输出已经执行的部分，还未执行的部分不会输出。

ES6 模块是动态引用，如果使用`import`从一个模块加载变量（即`import foo from 'foo'`），那些变量不会被缓存，而是成为一个指向被加载模块的引用，需要开发者自己保证，真正取值的时候能够取到值。

## 参考资料

* https://www.cnblogs.com/lvdabao/p/js-modules-develop.html
* http://es6.ruanyifeng.com/#docs/module-loader
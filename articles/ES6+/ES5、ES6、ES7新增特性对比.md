# ES5、ES6、ES7新增特性对比

## ES5

| **项目** | **详情** |
| :--- | :--- |
| 严格模式 |  |
| JSON |  |
| 新增Object方法属性 | `Object.getPrototypeOf`<br>`Object.getOwnPropertyDescriptor`<br>`Object.getOwnPropertyNames`<br>`Object.create`<br>`Object.defineProperty`<br>`Object.defineProperties`<br>`Object.seal`<br>`Object.freeze`<br>`Object.preventExtensions`<br>`Object.isSealed`<br>`Object.isFrozen`<br>`Object.isExtensible`<br>`Object.keys` |
| 新增Array方法属性 | `Array.prototype.indexOf`<br>`Array.prototype.lastIndexOf`<br>`Array.prototype.every`<br>`Array.prototype.some`<br>`Array.prototype.forEach`<br>`Array.prototype.map`<br>`Array.prototype.filter`<br>`Array.prototype.reduce`<br>`Array.prototype.reduceRight` |
| `Function.prototype.bind` |  |
| `String.prototype.trim` |  |
| `Date.now() / Date().toJSON()` |  |


## ES6

| **项目** | **详情** |
| :--- | :--- |
| `let / const` | [详情](./let和const.md) |
| 字符串模板 | `hello ${name}` |
| 解构赋值 |  |
| 数组新特性 | `Array.from`<br>`Array.of`<br>`Array.prototype.copyWithin`<br>`Array.prototype.fill`<br>`Array.prototype.find`<br>`Array.prototype.findIndex` |
| 函数参数 | 默认参数；rest参数 |
| 箭头函数 | [详情](./箭头函数.md) |
| 增强对象 | 对象简写<br>返回变量或对象作为属性名<br>对象方法属性声明简写 |
| 类 | [详情](./class和继承.md) |
| 模块module`import / export` |  |
| 循环与迭代器Iterator |  |
| 生成器Generator |  |
| set map weakset weakmap | 集合 |
| promise | [详情](../JavaScript基础/JavaScript异步.md) |
| 数据类型symbol | 作为对象名 |
| proxy | 对象劫持，类似于`Object.defineProperty`中的`getter / setter` |

## ES7

| **项目** | **详情** |
| :--- | :--- |
| 幂指数操作符`**` | `x**y`等同于`Math.pow(x, y)` |
| `Array.prototype.includes` | `array.includes(item) // true or false` |
| 异步函数`async / await` |  |
| SIMD | 单指令多数据流 |

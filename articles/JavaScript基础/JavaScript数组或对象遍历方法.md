# JavaScript数组或对象遍历方法

* for
* while(do...while)
* for...of
* for...in
* forEach
* map
* every
* some
* reduce
* Object.keys
* Iterator
* Generator

## 拓展运算符`...`

拓展运算符可以用于数组和对象的展开和复制等。

    let arr = [1, 2, 3]
    let arr2 = [...arr, 4, 5, 6] // [1,2,3,4,5,6]

    let obj = {a: 1, b: 2}
    let obj2 = {...obj, c: 3} // {a:1, b:2, c:3}
    
## `for...in`和`for...of`

* `for...of`是ES6新引入的遍历方式
* `for...of`用于补充`for...in`遍历数组的不足，因为`for...in`会遍历出数组的属性
* `for...of`可以遍历实现`[Symbol.iterator]`接口的对象，如`NodeList.prototype[Symbol.iterator] = Array.prototype[Symbol.iterator]`
* `for...in`默认情况下会遍历原型链上的可枚举属性，可以用`obj.hasOwnProperty`来过滤
* `for...of`遍历的是value，`for...in`遍历的是key
* `for...of`推荐用来遍历数组，`for...in`推荐用来遍历对象

    var obj = {name: 'obj', age: 1}
    var arr = [1, 2, 3]

    for(let i in obj) {
      console.log(i) // name age
    }

    for(let i in arr) {
      console.log(i) // 0 1 2
    }

    for(let i of obj) {
      console.log(i) // Uncaught TypeError: obj is not iterable
    }

    for(let i of arr) {
      console.log(i) // 1 2 3
    }
    
## `forEach`/`map`/`every`/`some`

* `forEach`和`map`只能遍历数组，遍历value
* `forEach`和`map`无法退出循环
* `forEach`和`map`的不同是，`map`会返回一个新数组
* 某些需要退出循环的情况，可以用`every`或`some`代替

## `Object.keys`

* `Object.keys`可以拿到对象的key组成的数组，可以用来遍历对象
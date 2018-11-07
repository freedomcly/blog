# JavaScript数组或对象遍历方法

* for
* while(do...while)
* forEach
* map
* reduce
* for...of
* for...in
* Iterator
* Generator

## 拓展运算符`...`

拓展运算符可以用于数组和对象的展开和复制等。

    let arr = [1, 2, 3]
    let arr2 = [...arr, 4, 5, 6] // [1,2,3,4,5,6]

    let obj = {a: 1, b: 2}
    let obj2 = {...obj, c: 3} // {a:1, b:2, c:3}
    
## `for...in`和`for...of`

* `for...in`是
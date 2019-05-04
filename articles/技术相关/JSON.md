# JSON

## JSON 值

JSON 支持哪些值？

* 简单值，包括字符串、数字、布尔、null，不支持 undefined
* 键值对对象
* 数组

例如：

    JSON.parse('{"a": undefined}') // 报错，由于 JSON 中值不能为 undefined

字符串须要用双引号。

    JSON.parse("{\"a\": 'undefined'}") // 报错

属性名须要用双引号。

    JSON.parse('{a: 1}') // 报错
    JSON.parse("{'a': 1}") // 报错
    JSON.parse('{"a": 1}') // 正确

## `JSON.stringify()`

1.值为 undefined 的属性都会被跳过

    JSON.stringify({a: undefined}) // {}

2.第二个参数用于过滤，第三个参数用于缩进

    JSON.stringify({address: 'shenzhen', children: [{name: 'Tracy', age: 11}, {name: 'Rose', age: 12}]}, null, 2)

3.`JSON.stringify()`的策略：

1. 调用 toJSON 方法，若有
2. 第二个参数过滤
3. 序列化
4. 缩进格式化

若有 toJSON 方法，则调用它：

    var obj = {address: 'shenzhen', children: [{name: 'Tracy', age: 11}, {name: 'Rose', age: 12}]}

    console.log(Object.prototype.toJSON) // undefined

    Object.prototype.toJSON = function() {return 1}

    JSON.stringify(obj) // 1

## `JSON.parse()`

将 JSON 字符串转换为 JSON 对象。


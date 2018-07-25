# JSON

## JSON值

* 简单值——字符串、数字、布尔、null。不支持undefined
* 键值对对象
* 数组

字符串只能用双引号。属性名须要用双引号。

## JSON序列化

1.`JSON.stringify()`

* 值为undefined的属性都会被跳过
* 第二个参数用于过滤
* 第三个参数用于缩进
* `JSON.stringify()`策略：1.toJSON方法；2.第二个参数过滤；3.序列化；4.缩进格式化

2.`JSON.parse()`




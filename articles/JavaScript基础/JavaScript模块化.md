# JavaScript模块化

| **项目** | **AMD** | **CMD** | **CommonJS** | **import/export** |
| :--- | :--- | :--- | :--- | :--- |
| 名称 | Asynchronous Module Definition | Common Module Definition | CommonJS | import/export |
| 代表 | requireJS | seaJS | nodeJS | ES6 |
| 载入方式 | 异步 |  | 同步 |  |
| 模块规范 |  |  | 模块引用`require()`<br>模块定义`module.exports`<br>模块标识 |  |

## 兼容多种模块规范


    ;(function (name, definition) {
      // 检测上下文环境是否为AMD或CMD
      var hasDefine = typeof define === 'function'
      // 检测上下文环境是否为Node
      var hasExports = typeof module !== 'undefined' && module.exports

      if (hasDefine) {
        // AMD环境或CMD环境
        define(definition)
      } else if (hasExports) {
        // 定义为普通Node模块
        module.exports = definition()
      } else {
        // 将模块的执行结果挂在window变量中
        this[name] = definition()
      }
    })('hello', function () {
      var hello = function () {console.log('hello')}
      return hello
    })
    


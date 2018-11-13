# exports和module.exports

    exports.a = {...}
    module.exports = {...}
    
结论：
* 最终导出的是module.exports
* 不能使用`exports = {...}`，只能使用`exports.a = {...}`，因为`exports`是对`module.exports`的引用，如果重新对`exports`赋值，会打破引用关系。
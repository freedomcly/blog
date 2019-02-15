# Webpack优化

## 基本概念

* entry——从入口开始，递归查找所有依赖的模块
* module——一切皆模块，一个模块对应一个文件
* chunk——一个chunk由多个module组成，用于代码合并和分割
* loader——模块转换器
* plugin——插件，特定的时机做对应的事
* output——输出结果

## 输出分析工具

* ~~官方输出分析工具，web应用webpack analyse~~
* webpack-bundle-analyzer

## 优化策略

优化可以分为：优化开发体验、优化输出质量

### 1.优化开发体验

优化构建速度：
* **缩小文件的搜索范围（见下方）**
* 使用`webpack.DllPlugin`（动态链接库思想，动态链接库的代码只需要编译一次，复用时直接使用，不需要再次编译）
* 使用`happypack`（发挥多核CPU优势，利用多进程执行loader转换）
* 使用`webpack-parallel-uglify-plugin`（发挥多核CPU优势，利用多个子进程压缩文件）

另附，缩小文件的搜索范围策略：
* 优化loader配置，根据test、include、exclude更精准地匹配
* 优化resolve.modules配置，配置为`node_modules`，减少向上一层层查找
* 优化resolve.mainFields配置，配置第三方模块入口文件
* 优化resolve.alias配置，别名映射的文件，也可以配置第三方模块入口文件
* 优化resolve.extensions配置，文件后缀，尽可能少
* 优化module.noParse配置，对非模块化文件，忽略递归解析处理

优化使用体验：
* **使用自动刷新（文件监听，自动刷新浏览器）**
* **开启模块热替换**

### 2.优化输出质量

减少首屏加载时间：
* 区分环境
* **压缩代码（ES6: `uglifyjs-webpack-plugin`/ CSS: `optimize-css-assets-webpack-plugin`）**
* CDN
* **使用Tree Shaking**
* **提取公共代码（`optimization.splitChunks`）**
* **生成HTML文件（`html-webpack-plugin`）**
* **按需加载（路由异步加载）**

提升流畅度：
* 使用Prepack（修改代码实现优化，`prepack-webpack-plugin`）
* 开启Scope hoisting（作用域提升，减少代码量，`webpack/lib/optimize/ModuleConcatenationPlugin`）

## webpack运行流程

* 初始化参数。从配置文件和shell语句中读取与合并参数，得到最终的参数。
* 开始编译。用参数初始化Compiler对象，加载配置的插件，通过执行对象的run方法开始执行编译。
* 确定入口。根据配置的entry找出所有入口文件。
* 编译模块。从入口文件出发，调用对应的loader对模块进行翻译，再找出该模块的依赖模块，递归本步骤，直到所有依赖模块都经过本步骤的处理。
* 完成模块编译。经过第4步，得到每个模块的翻译结果和他们的依赖关系。
* 输出资源。根据入口和模块之间的依赖关系，组装成一个个包含多个模块的chunk，再将每个chunk转换成一个单独的文件加入输出列表。
* 输出完成。确定好输出内容后，根据配置的路径和文件名，将文件内容写入文件系统中。

简化一下：
* 初始化
* 编译
* 输出

## 输出文件

问题：

* 为什么webpack中可以import/require
* webpack怎么处理按需加载

输出的文件是一个立即执行函数，参数为存放所有模块的数组或对象。

    (function(modules) {
      // 模拟require
      function __webpack_require__(moduleId) {
      }

      // 执行入口模块
      return __webpack_require__(__webpack_require__.s = '/* moduleId */');
    })({/* 存放所有模块 */})

按需加载的原理是JSONP，在主文件中写入`window['webpackJsonp']`函数，异步文件中调用`webpackJson(/* 异步模块内容 */)`安装模块。`requireEnsure`用于异步加载。

## 编写Loader

每个Loader是一个nodeJS模块。最简单的loader：

    module.exports = function (source) {
      return source
    }
    
## 编写Plugin

    class BasicPlugin{
      constructor(options){}

      apply(compiler) {
        compiler.plugin('compilation', function(compilation){})
      }
    }

    module.exports = BasicPlugin

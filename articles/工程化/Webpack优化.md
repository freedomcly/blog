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
* **压缩代码（JS: `webpack/lib/optimize/UglifyJsPlugin`/ ES6: `uglifyjs-webpack-plugin`/ CSS: `css-loader?minimize`）**
* CDN
* **使用Tree Shaking（`--optimize-minimize`）**
* **提取公共代码（chunk思想，`webpack/lib/optimize/CommonsChunkPlugin`，提取CSS文件`extract-text-webpack-plugin`）**
* **生成HTML文件（`html-webpack-plugin`）**
* **分割代码以按需加载（chunk思想）**

提升流畅度：
* 使用Prepack（修改代码实现优化，`prepack-webpack-plugin`）
* 开启Scope hoisting（作用域提升，减少代码量，`webpack/lib/optimize/ModuleConcatenationPlugin`）
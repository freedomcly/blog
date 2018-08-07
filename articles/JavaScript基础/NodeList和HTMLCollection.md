# NodeList和HTMLCollection

## 相同点

* 都有length属性
* 都是类数组对象
* 都有getter方法，用item()传入索引值访问

## 不同点

| **项目** | **NodeList** | **HTMLCollection** |
| :--- | :--- | :--- |
| 包含项 | node（包含element node1，text node3，comment node8等） | element node |
| namedItem方法，通过元素name或id过滤 | 部分浏览器支持 | 支持 |
| 最初设计 | DOM core | DOM html |
| 如何获得 | `childNodes`属性，`document.querySelectorAll()` | `children`属性，`document.getElementsByTagName()` |

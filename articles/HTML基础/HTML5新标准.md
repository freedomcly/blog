# HTML5新标准

| **类别** | **具体规范** |
| :--- | :--- |
| 离线（offline） | Application Cache, **Local Storage**, Indexed DB, **在线/离线事件** |
| 存储（storage）| Application Cache, **Local Storage**, Indexed DB |
| 连接（connectivity） | Web Socket, Server-sent事件 |
| 文件访问（file access） | File API, File System, File Writer, Progress Events |
| 语义（semantics） | 各种新元素，包括Media，structural，国际化，Link relation，属性，form类型，microdata等 |
| 音频和视频（audio/video） | HTML5 Videa, Web Audio, WebRTC, Video track |
| - [ ] 3D和图形（3D/graphics） | Canvas 2D/3D, CSS3 transform, WebGL, SVG |
| 展示（presentation） | CSS3 2D/3D, transition, Webfont |
| 性能（performance） | Web Worker, HTTP caching |
| 其他（nuts and bolts） | 触控和鼠标, Shadow DOM, CSS masking |

## 语义化

HTML5新增语义化标签：

| **Element** | **Description** |
| :--- | :--- |
| &lt;section> | 定义文档中的一个章节 |
| &lt;article> | 定义可以独立于内容其余部分的完整独立内容块 |
| &lt;nav> | 定义包含导航链接的部分 |
| &lt;header> | 定义页面头部，经常包含logo、标题和导航 |
| &lt;footer> | 定义页面尾部，经常包含版权信息、法律信息、反馈意见 |
| &lt;main> | 定义文档中主要或重要的部分 |
| &lt;aside> | 定义和页面内容关联度较低的内容——如果被删除，剩下的内容仍然合理 |
| &lt;figure> | 定义图例，包含图片和说明（figcaption） |
| &lt;figcaption> | 定义图例的说明 |

传统语义化标签：

| **Element** | **Description** |
| :--- | :--- |
| &lt;h1>&lt;h2>&lt;h3>&lt;h4>&lt;h5>&lt;h6> | 定义标题 |
| &lt;li>&lt;ul>&lt;ol> | 定义列表 |

语义化的目的：

* 无CSS时，页面能呈现出很好的内容（无CSS，指浏览器不支持CSS或CSS文件加载失败）
* 有利于SEO
* 增强可访问性，方便盲人阅读器、移动设备呈现页面
* 增强可读性和可维护性，方便团队开发和维护

## 参考资料

* [MDN HTML5标签列表](https://developer.mozilla.org/zh-CN/docs/Web/Guide/HTML/HTML5/HTML5_element_list)
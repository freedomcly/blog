# Block-formatting-context

# 什么是BFC？

Block formatting context即块级格式化上下文，用于容纳一个个块级元素。在CSS 2.1中，定位模式有三种：
* normal flow
* floats
* absolute positioning

BFC属于normal flow（正常流），一个普通的html即是一个BFC。

# 如何产生新的BFC？

* The value of `float` is not `none`
* The value of `position` is neither `static` nor `relative`
* The value of `display` is `table-cell`, `table-caption`, `inline-block`, `flex`, `inline-flex`
* The value of `overflow` is not `visible`

前两个很好理解，float和absolute position都不是normal flow，它们新建了块级格式化上下文。

# BFC应用

## 1.避免margin塌陷

由于margin塌陷只存在于同一个BFC中，可以用新建BFC来避免margin塌陷。

## 2.容纳float元素

float元素无法撑起它的容纳块，如果要撑起容纳块，可以新建BFC。功效等价于清除浮动。

## 3.避免文字围绕

在同一个BFC中，文字会围绕在float元素周围，如果想避免这种情况，可以新建BFC来包含文字。

# 参考资料

* [Understanding Block Formatting Contexts In CSS](https://www.sitepoint.com/understanding-block-formatting-contexts-in-css/)
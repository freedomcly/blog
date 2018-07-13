# FastClick原理

## 浏览器300ms延时产生的原因

浏览器在等待确认是否是双击

## FastClick核心代码

    FastClick.prototype.onTouchEnd = function(event){
      if (!this.needsClick(targetElement)) {
        event.preventDefault(); 
        this.sendClick(targetElement, event);
      }
    }

    FastClick.prototype.sendClick = function(targetElement, event) {
      // 创建一个鼠标事件
      clickEvent = document.createEvent('MouseEvents');
      // 初始化鼠标事件为click事件 
      clickEvent.initMouseEvent(this.determineEventType(targetElement), true, true, window, 1, touch.screenX, touch.screenY, touch.clientX, touch.clientY, false, false, false, false, 0, null);
      clickEvent.forwardedTouchEvent = true;
      // 在目标元素上触发该鼠标事件
      targetElement.dispatchEvent(clickEvent);
    }
    
## FastClick原理

    FastClick.attach(document.body)

**FastClick会在`document.body`上绑定`touchstart`和`touchend`事件，用于事件委托/代理。`touchstart`事件保存targetElement，`touchend`事件在targetElement上触发click事件。**

**也就是说，FastClick只是为根元素（document.body）绑定了一些事件，没有为每个target element绑定事件，根元素的touchend事件触发时，在target element上dispatch一个手动构造的click事件，从而绕过了300ms延时。**

如何触发click事件呢？涉及到一些关于event的API：
* document.createEvent('MouseEvents')
* clickEvent.initMouseEvent(...)
* targetElement.dispatchEvent(clickEvent)




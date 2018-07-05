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
    
`onTouchStart`保存targetElement，`onTouchEnd`在targetElement上触发click事件。

如何触发click事件呢？涉及到一些关于event的API：
* document.createEvent('MouseEvents')
* clickEvent.initMouseEvent(...)
* targetElement.dispatchEvent(clickEvent)

## 总结

因此，简单说FastClick是把click改写成touch事件。也就是在touchstart触发时获取targetElement，touchend立即dispatchEvent，绕过了click的300ms延时。



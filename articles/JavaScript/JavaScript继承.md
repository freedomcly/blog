# JavaScript 深入之继承

继承在维基百科里的解释：

> 继承可以使得子类具有父类别的各种属性和方法，而不需要再次编写相同的代码。在令子类别继承父类别的同时，可以重新定义某些属性，并重写某些方法，即覆盖父类别的原有属性和方法，使其获得与父类别不同的功能。

其中比较常见的继承有两种：寄生组合继承和行为委托继承。

## 原型链继承

* 每个函数（包括构造函数）有`prototype`属性，指向函数对应的原型对象
* 每个实例内部有指针`[[proto]]`（在chrome浏览器里可见，即`__proto__`），指向对应构造函数的原型对象

原型链继承原理：**实例中的内部指针`[[proto]]`指向构造函数的原型对象，因此实例能获取构造函数原型对象中的属性和方法。如果构造函数的原型对象是超类构造函数的实例，它也能获取该超类构造函数的原型对象。这样递进下去，就能形成一条原型链。**

关于`constructor`：

* 每个原型对象中有`constructor`属性指向构造函数
* 每个实例通过原型对象也能拿到`constructor`

## 几种继承方法对比

| **方法** | **劣势** | **优势** |
| :--- | :--- | :--- |
| 原型链继承 | 超类实例属性若为引用类型，被所有实例共享；子类复用超类构造函数时不能传参 | 实例方法复用 |
| 借用构造函数 | 无法复用实例方法 | 可以解决原型链继承的劣势 |
| 组合继承 | 两次调用超类 | 既解决了原型链继承的劣势，又可以复用实例方法 |
| 寄生组合继承 | 无 | 减少一次超类调用 |
| `Object.create()` | 没有引入类的概念，只有对象，不能识别对象类型 | 简单 |

## 寄生组合式继承

较优解。来自YAHOO.lang.extend()。

    function Person(name, age) {
      this.name = name;
      this.age = age;
      this.colors = ['yellow', 'white', 'black'];
    }

    Person.prototype.getName = function () {
      return this.name;
    }

    function Child(school, name, age) {
      // 借用构造函数
      Person.call(this, name, age);
      this.school = school;
    }
    
    // 继承
    Child.prototype = Object.create(Person.prototype); // 不是Child.prototype = new Person()
    Child.prototype.constructor = Child;

    let child1 = new Child('tieyi', 'rongrong', 15);
    let child2 = new Child('kunshan', 'maomao', 11);

## Object.create()

由Douglas Crockford（《JavaScript语言精粹》作者）提出，基于已有对象构造新对象。

    function create(o) {
      function F(){}
      F.prototype = o;
      return new F();
    }

同ES5中的Object.create()。

## ES6 class继承

class继承是寄生组合继承的语法糖。参考[class和继承](../ES6+/class和继承.md)。

## 行为委托继承

JavaScript 的继承简单说就是，一个对象可以访问另一个对象的属性或方法，从而实现代码复用。

最优解。更清晰，更简单，避免了丑陋的`prototype`和`call`。

    var Person = {
      init (name, age) {
        this.name = name
        this.age = age
      },
      getName () {
        return this.name
      }
    }

    var Child = Object.create(Person)

    Child.make = function (name, age, school) {
      this.init(name, age)
      this.school = school
    }
    
    var child1 = Object.create(Child)
    child1.make('rongrong', 15, 'tieyi')
    var child2 = Object.create(Child)
    child2.make('maomao', 11, 'kunshan')
    
## 名词解释

来自其他面向对象语言的概念：
* 私有属性（方法）——只能在类中使用，例如在构造函数中声明的变量和函数
* 静态属性（方法）——通过类名来访问，例如`Person.className`
* 实例属性（方法）——通过实例来访问，例如`person.name`或`person.getName`

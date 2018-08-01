# 关于JavaScript继承

## 原型链继承

* 每个函数（包括构造函数）有prototype属性，指向函数对应的原型对象
* 每个实例内部有指针`[[proto]]（在chrome浏览器里可见，即__proto__）`，指向对应构造函数的原型对象

原型链继承原理：**实例中的内部指针`[[proto]]`指向构造函数的原型对象，因此实例能获取构造函数原型对象中的属性和方法。如果构造函数的原型对象是超类构造函数的实例，它也能获取该超类构造函数的原型对象。这样递进下去，就能形成一条原型链。**

## 几种继承方法对比

| **方法** | **劣势** | **优势** |
| :--- | :--- | :--- |
| 原型链继承 | 超类实例属性若为引用类型，被所有实例共享；子类复用超类构造函数时不能传参 | 实例方法复用 |
| 借用构造函数 | 无法复用实例方法 | 可以解决原型链继承的劣势 |
| 组合继承 | 两次调用超类 | 既解决了原型链继承的劣势，又可以复用实例方法 |
| 寄生组合继承 | 无 | 减少一次超类调用 |

## 寄生组合式继承

最优解。来自YAHOO.lang.extend()。

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
    Child.prototype = Object.create(Person.prototype);
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
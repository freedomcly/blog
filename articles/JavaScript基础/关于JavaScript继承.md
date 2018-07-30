# 关于JavaScript继承

## 原型链继承

原型链继承原理：**实例中有一个内部指针`[[proto]]`指向构造函数的原型对象，因此实例能获取构造函数原型对象中的属性和方法。如果构造函数的原型对象是另一个构造函数的实例，它也能获取该构造函数的原型对象。这样递进下去，就能形成一条原型链。**


    function Person(name, age) {
      this.name = name;
      this.age = age;
    }

    Person.prototype.getName = function () {
      return this.name;
    }

    function Child() {}
    function LittleChild() {}

    // 实现原型链继承
    Child.prototype = new Person();
    LittleChild.prototype = new Child();

    let child = new Child();
    let littleChild = new LittleChild();
    child.name = 'child';
    littleChild.name = 'little child';
    child.getName();
    littleChild.getName();

如图：
![](/assets/inherit.png)

1.为什么littleChild能调用getName函数？
因为LittleChild.prototype是Child构造函数的实例，能获取Child.prototype；Child.prototype是Person构造函数的实例，能获取Person.prototype。
littleChild调用getName时，
* 首先在littleChild实例中找；
* 如果没有，就在LittleChild.prototype中找；
* 如果没有，就在Child.prototype中找；
* 如果没有，就在Person.prototype中找，getName确实在Person.prototype中能找到。

2.prototype和[[proto]]有什么区别？
prototype存在于函数中，[[proto]]存在于对象实例中。
图中三个构造函数Person、Child、LittleChild中都有prototype指针，指向对应的原型对象。
图中三个构造函数的实例Child.prototype、LittleChild.prototype、littleChild中都有[[proto]]，在chrome浏览器中是__proto__，指向其构造函数的原型对象。

    littleChild.__proto__ === LittleChild.prototype;
    littleChild.__proto__.__proto__ === Child.prototype;
    littleChild.__proto__.__proto__.__proto__ === Person.prototype;

3.什么是原型链？
也就是箭头A、B、C形成的链条。

4.如何识别实例和原型的关系？

    littleChild instanceof LittleChild; // true
    littleChild instanceof Child; // true
    littleChild instanceof Person; // true
    littleChild instanceof Object; // true

    LittleChild.prototype.isPrototypeOf(littleChild); // true
    Child.prototype.isPrototypeOf(littleChild); // true
    Person.prototype.isPrototypeOf(littleChild); // true
    Object.prototype.isPrototypeOf(littleChild); // true

5.constructor的问题？
因为`Child.prototype = new Person()`改写了Child的原型对象，LittleChild也是类似的情况，所以它们的constructor都指向Person。

    LittleChild.prototype.constructor; // Person
    Child.prototype.constructor; // Person
    Person.prototype.constructor; // Person
    
6.原型链继承的局限性
* 包含引用类型值时，如数组，会被所有实例共享
* 创建子类型的实例时，不能向超类型构造函数传递参数。也就是创建child实例时，不能向Person构造函数传递name和age。


## 组合继承

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
    
    // 原型链继承
    Child.prototype = new Person();
    
    let child1 = new Child('tieyi', 'rongrong', 15);
    let child2 = new Child('kunshan', 'maomao', 11);

1.colors不会被所有实例共享。

    child1.colors.push('red');
    child1.colors; // ['yellow', 'white', 'black', 'red'];
    child2.colors; // ['yellow', 'white', 'black'];
    
2.可以向超类型构造函数传递参数。

3.组合继承的局限性：
* 超类构造函数被调用了两次

## 寄生组合式继承

最优解：

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
    
    // 原型链继承
    inheritPrototype(Child, Person);

    function inheritPrototype(subType, superType) {
      var prototype = Object.create(superType.prototype);
      prototype.constructor = subType;
      subType.prototype = prototype
    }

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
# JavaScript 深入之继承

继承在维基百科里的解释：

> 继承可以使得子类具有父类别的各种属性和方法，而不需要再次编写相同的代码。在令子类别继承父类别的同时，可以重新定义某些属性，并重写某些方法，即覆盖父类别的原有属性和方法，使其获得与父类别不同的功能。

JavaScript 中比较常见的继承方式有：

* 原型链继承
* 借用构造函数继承
* 组合继承
* 原型式继承
* 寄生式继承
* **寄生组合继承**
* **行为委托继承**

## 原型链继承

可参考上一节 [JavaScript 深入之原型链](./JavaScript深入之原型链.md)，借助原型链实现继承：

    function Person (name) {
      this.name = name
      this.colors = ['black', 'yellow', 'white']
    }

    Person.prototype.address = 'earth'

    Person.prototype.getName = function() {
      return this.name
    }

    function Student (name, school) {
      this.name = name
      this.school = school
    }

    Student.prototype = new Person()
    Student.prototype.constructor = Student

    var s1 = new Student('maomao', 'kunshan')
    var s2 = new Student('rongrong', 'UESTC')
    s1.getName() // maomao
    s1.address // earth
    
    s1.colors.push('red')
    s2.colors // black, yellow, white, red
    
原型链继承有两个问题：

* 子类新建实例时，无法向父类传递参数。`new Student('maomao', 'kunshan')`中的 name 无法直接复用父类中的`this.name = name`。
* 父类中的引用类型属性被子类的实例共享。colors 被实例 s1 和 s2 共享，这个问题是`Student.prototype = new Person()`遗留的，改为`Student.prototype = Object.create(Person.prototype)`就没有这个问题。

## 借用构造函数继承

    function Person (name) {
      this.name = name
    }

    function Student (name, school) {
      Person.call(this, name)
      this.school = school
    }

    var s1 = new Student('maomao', 'kunshan')
    var s2 = new Student('rongrong', 'UESTC')

子类新建实例时，可以向父类传递参数，因为子类中有`Person.call(this, args)`。

借用构造函数继承的问题是

* 无从得知继承关系。
* 无法复用实例方法。

## 组合继承

    function Person (name) {
      this.name = name
      this.colors = ['black', 'yellow', 'white']
    }

    Person.prototype.getName = function() {
      return this.name
    }

    function Student (name, school) {
      Person.call(this, name)
      this.school = school
    }

    Student.prototype = new Person()
    Student.prototype.constructor = Student

    var s1 = new Student('maomao', 'kunshan')
    var s2 = new Student('rongrong', 'UESTC')
    
    s1.colors.push('red')
    s2.colors // black, yellow, white

组合继承组合了原型链继承和借用构造函数继承，避免了两者的缺点。既可以向父类传递参数，父类中的引用类型属性不被共享，可以获取继承关系，也可以复用实例方法。

但也有缺点，父类调用了两次，一次是`Student.prototype = new Person()`，一次是`Person.call(this, ...args)`。

## 原型式继承

由 Douglas Crockford（《JavaScript语言精粹》作者）提出，基于已有对象构造新对象。

    function create(o) {
      function F(){}
      F.prototype = o;
      return new F();
    }

同 ES5 中的`Object.create()`。

## 寄生式继承

    function createStudent(person) {
      var tempStudent = Object.create(person)
      tempStudent.school = 'UESTC'
      tempStudent.getSchool = function(){return this.school}

      return tempStudent
    }

    var person = {
      name: 'maomao',
      getName: function(){return this.name}
    }

    var student = createStudent(person)
    student.name // maomao
    student.getName() // maomao
    student.getSchool() // UESTC

寄生式继承基于原型式继承。

## 寄生组合式继承

    function Person(name, age) {
      this.name = name
      this.age = age
      this.colors = ['yellow', 'white', 'black']
    }

    Person.prototype.getName = function () {
      return this.name
    }

    function Student(school, name, age) {
      // 借用构造函数
      Person.call(this, name, age)
      this.school = school
    }

    // 继承
    Student.prototype = Object.create(Person.prototype)
    Student.prototype.constructor = Student

    var s1 = new Student('tieyi', 'rongrong', 15)
    var s2 = new Student('kunshan', 'maomao', 11)

寄生式组合继承借鉴了寄生式继承，原来的组合继承中父类需要调用两次，寄生组合继承只需要调用一次。

## 行为委托继承

JavaScript 的继承简单说就是，一个对象可以访问另一个对象的属性或方法，从而实现代码复用。

行为委托继承更清晰，更简单，避免了丑陋的 prototype 和 call。缺点是无从得知继承关系，constructor 都指向 Object。

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

## 几种继承方法对比一览

| **方法** | **劣势** | **优势** |
| :--- | :--- | :--- |
| 原型链继承 | 子类新建实例时，无法向父类传递参数；<br/>父类中的引用类型属性被子类的实例共享 | 可以获取继承关系；实例方法复用 |
| 借用构造函数继承 | 无法复用实例方法 | 可以解决原型链继承的劣势 |
| 组合继承 | 两次调用父类 | 既解决了原型链继承的劣势，又具有原型链继承的优势 |
| 原型式继承 | 不能识别对象类型 | 简单 |
| 寄生式继承 | 不能识别对象类型；无法复用实例方法 | 简单，某些场景适用 |
| 寄生组合继承 | 无 | 对比组合继承，减少一次父类调用 |
| 行为委托继承 | 不能识别对象类型，无法获取继承关系 | 接近 JavaScript 中继承的本质 |

## 名词解释

来自其他面向对象语言的概念：
* 私有属性（方法）——只能在类中使用，例如在构造函数中声明的变量和函数
* 静态属性（方法）——通过类名来访问，例如`Person.className`
* 实例属性（方法）——通过实例来访问，例如`person.name`或`person.getName`

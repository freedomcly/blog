# JavaScript 之原型链

## 函数的`prototype`属性

    function func () {}
    console.log(func.prototype) // {constructor: ƒ}
    
每个普通函数都有`prototype`属性，指向原型对象。

原型对象是什么呢？

当函数作为构造函数使用，构造函数的实例都会与其原型对象产生联系，可以获取原型对象的属性。

    function Person () {}

    Person.prototype.name = 'Person'

    var person1 = new Person()
    var person2 = new Person()

    person1.name // Person
    person2.name // Person

构造函数的实例对象怎么与构造函数的`prototype`属性指向的原型对象产生联系呢？与对象内部的`[[proto]]`有关。

## 对象的`__proto__`属性

    var obj = {}
    obj.__proto__ // {...}

每个普通对象都有`__proto__`属性，指向其原型对象。

通过对象表达式创建的对象是`Object`的实例，因此：

    obj.__proto__ === Object.prototype // true

`__proto__`从哪里来呢？

`__proto__`并不存在于实例和实例的原型对象，而是`Object.prototype`中的`getter`。我们访问对象的`__proto__`时，会进行原型链查找，一直追溯到`Object.prototype`中根据`getter`函数来返回。

## 原型对象的`constructor`属性

构造函数和其实例有什么联系呢？换句话说，怎么获取一个对象的构造函数？

可以通过`constructor`属性：

    var obj = {}
    obj.constructor // Object
   
    function Person () {}
    var person = new Person()
    person.constructor // Person
   
实例对象`person`是空的，`person`的`constructor`从哪里获取的呢？

原来`person`的原型对象中有`constructor`属性，指向其构造函数。

    person.__proto__.constructor // Person
    Person.prototype.constructor // Person

由此，得到了构造函数、原型对象、实例之间的关系：

![](/assets/prototype1.jpg)

由于任何对象都继承自`Object`，因此关系图可以拓展为：

![](/assets/prototype2.png)

## 原型链

有一个新的类 Student 继承 Person：

    function Student () {Person.call(this)}
    Student.prototype = Object.create(Person.prototype)
    Student.prototype.constructor = Student
    
关系图拓展为：

![](/assets/prototype3.png)

蓝色线条即原型链，图中有两条，从 student 开始到 null 结束的蓝色线条，以及从 person 开始 到 null 结束的蓝色线条。
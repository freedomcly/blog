# class和继承

## 静态属性

静态属性的特点：

* 通过类名访问
* 可被继承（是否应该被继承？）

方式一：

    class Person {}
    Person.total = 100
    
方式二：

    let num = 100
    class Person {
      static get total () {
        return num
      }
      static set total (value) {
        num = value
      }
    }
    
    // test
  
    Person.total // 100
    
由于static命令只能修饰class的方法，因此可以通过get和set函数来定义静态属性。

## 静态方法

静态方法的特点：

* 通过类名访问
* 可被继承


    class Person {
      static getCountry () {
        return 'China'
      }
    }
    
    // test
    
    Person.getCountry()
  
## 私有属性和私有方法

构造函数内部定义的变量和函数。

## 继承

    class Person {
      constructor(name, age) {
        this.name = name
        this.get = age
      }
      getName () {
        return this.name
      }
    }
    
    class Child extends Person {
      constructor(name, age, kindergarten) {
        super(name, age)
        this.kindergarten = kindergarten
      }
      getKindergarten () {
        return this.kindergarten
      }
    }

相当于ES5

    function Person (name, age) {
      this.name = name
      this.age = age
    }
    
    Person.prototype.getName = function () {
      return this.name
    }
    
    function Child (name, age, kindergarten) {
      Person.call(this, name, age)
      this.kindergarten = kindergarten
    }
    
    Child.prototype = new Person()
    
    Child.prototype.getKindergarten = function () {
      return this.kindergarten
    }
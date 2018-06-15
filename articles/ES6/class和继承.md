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

可以通过命名方式来约定。

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
        return kindergarten
      }
    }

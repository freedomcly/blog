# ES6 之 class 和继承

ES6 继承：

    class Person {
      constructor (name) {
        this.name = name
      }

      // 静态属性，可被子类继承
      static get className () {
        return 'Person'
      }

      sayName () {
        return this.name
      }
    }

    class Child extends Person {
      constructor (name, school) {
        super(name)
        this.school = school
      }

      sayName () {
        // 重写父类的sayName方法，可以引用super.sayName
        return `Child: ${super.sayName()}`
      }

      learn (content) {
        console.log(`I am learning ${content}`)
      }
    }

    // test
    console.log(Person.className) // Person
    console.log(Child.className) // Person
    var child1 = new Child('maomao', 'UESTC')
    console.log(child1.sayName()) // Child: maomao
    child1.learn('math') // I am learning math
    
    
ES5 寄生组合继承：


    function Person (name) {
      this.name = name
    }

    Person.prototype.sayName = function() {
      return this.name
    }

    // 静态属性不能继承
    Person.className = 'Person'

    function Child (name, school) {
      Person.call(this, name)
      this.school = school
    }

    Child.prototype = Object.create(Person.prototype)

    Child.prototype.sayName = function () {
      // 这里不能直接调用超类的实例方法
      return `Child: ${this.name}`
    }

    Child.prototype.learn = function (content) {
      console.log(`I am learning ${content}`)
    }

    // test
    console.log(Person.className) // Person
    console.log(Child.className) // undefined
    var child1 = new Child('maomao', 'UESTC')
    console.log(child1.sayName()) // Child: maomao
    child1.learn('math') // I am learning math

## class 的语法

**1. super **

`super()` 作为函数使用，代表父类构造函数。相当于 ES5 中的`Person.call(this, name)`。

```javascript
class Child extends Person {
  constructor (name, school) {
    super(name)
    this.school = school
  }
}
```

super 作为对象使用，代表父类原型对象：

```javascript
class Child extends Person {
  sayName () {
    return `Child: ${super.sayName()}`
  }
}
```

**2. 两条原型链**

```javascript
Child.__proto__ === Person
child.__proto__.__proto__ === Person.prototype
```

## class 写法的优势

类相关代码可以写在同一个地方。更清晰、更像面向对象编程的语法。
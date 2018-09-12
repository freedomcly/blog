# class和继承

ES6继承：

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
    console.log(child1.sayName()) // maomao
    child1.learn('math') // I am learning math
    
    
ES5寄生组合继承：


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
    
## class的优势

类相关代码可以写在一个地方。
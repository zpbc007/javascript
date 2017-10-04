# 继承

继承有两种方式：接口继承和实现继承。接口继承只继承方法签名，而实现继承则继承实际的方法。由于js中没有函数签名，无法实现接口继承。

## 1. 原型链

实现原型链的基本方式

```
function SuperType () {
    this.superProperty = true
}

SuperType.prototype.getSuperValue = function () {
    return this.superProperty
}

function SubType () {
    this.subProperty = false
}

SubType.prototype = new SuperType()

SubType.prototype.getSubValue = function () {
    return this.subProperty
}

var instance = new SubType()
console.log(instance.getSuperValue())
```

注意： **instance中的constructor指向的是SuperType**

### 1.1 别忘记默认的原型

所有对象最终都继承与Object

### 1.2 确定原型和实例的关系

instanceof 运算符用来检测 constructor.prototype 是否存在于参数 object 的原型链上。(不是检测constructor是否在原型链上被引用)

```
console.log(instance instanceof Object)     //true
console.log(instance instanceof SuperType)  // true
console.log(instance instanceof SubType)    // true
```

使用isPropertyOf()方法(与instanceof 其实是一样的)

```
console.log(Object.prototype.isPrototypeOf(instance))   // true
console.log(SuperType.prototype.isPrototypeOf(instance))    // true
console.log(SubType.prototype.isPrototypeOf(instance))  //true
```

### 1.3 谨慎的定义方法

子类型有时候需要覆盖超类中的某个方法，或者添加超类中不存在的方法。**给原型添加方法的代码一定要放在替换原型语句之后**

在通过原型链实现继承的时候，不能使用字面量对象创建原型方法，这样做会重写原型。

### 1.4 原型链问题

引用类型的值会被继承，被所有实例共享

```
function SuperType () {
    this.colors = ['red', 'black']
}

function SubType () {}

SubType.prototype = new SuperType()

var instance1 = new SubType()
instance1.colors.push('green')

var instance2 = new SubType()

console.log(instance1.colors)   //["red", "black", "green"]
console.log(instance2.colors)   //["red", "black", "green"]
```

创建子类型的实例时，不能向超类的构造函数中传递参数。

## 2. 借用构造函数

```
function SuperType () {
    this.colors = ['red', 'black']
}

function SubType () {
    // 继承了SuperType
    SuperType.call(this)
}

var instance1 = new SubType()
instance1.colors.push('green')

var instance2 = new SubType()

console.log(instance1.colors)
console.log(instance2.colors)
```

### 2.1 传递参数

```
function SuperType (name) {
    this.name = name
}

function SubType () {
    // 向超类传递参数
    SuperType.call(this, 'zp')

    // 实例属性
    this.age = 29
}

var instance = new SubType()
console.log(instance.name)  //zp
console.log(instance.age)   //29
```

### 2.2 问题

方法都定义在构造函数中，因此函数无法复用。而且超类的原型中定义的方法对子类型不可见，结果所有类型都只能使用构造函数模式。

**为了确保SuperType构造函数不会重写子类的属性，应该在调用超类的构造函数后，再添加应该在子类型中定义的属性**

## 3. 组合继承(伪经典继承)

将原型链和借用构造函数方式组合在一起。使用原型链实现对原型属性和方法的继承，通过构造函数来实现对实例属性的继承。

```
function SuperType (name) {
    this.name = name
    this.colors = ['red', 'black']
}

SuperType.prototype.sayName = function () {
    console.log(this.name)
}

function SubType (name, age) {
    // 继承属性
    SuperType.call(this, name)

    this.age = age
}

// 继承方法
SubType.prototype = new SuperType()
SubType.prototype.constructor = SubType
SubType.prototype.sayAge = function () {
    console.log(this.age)
}

var instance1 = new SubType('zp', 29)
instance1.colors.push('green')
console.log(instance1.colors)
instance1.sayAge()
instance1.sayName()

var instance2 = new SubType('lmy', 25)
console.log(instance2.colors)
instance2.sayAge()
instance2.sayName()
```

## 4. 原型式继承

**s使用Object.create()方法**

```
var person = {
    name: 'zp',
    friends: ['zhao', 'qian']
}

var anotherPerson = Object.create(person)
anotherPerson.name = 'lmy'
anotherPerson.friends.push('sun')

var yetAnotherPerson = Object.create(person)
yetAnotherPerson.name = 'cui'
yetAnotherPerson.friends.push('li')

console.log(person.friends) // ["zhao", "qian", "sun", "li"]
```

## 5. 寄生式继承

创建一个仅用于封装继承过程的函数，该函数在内部以某种方式来增强对象，最后返回对象。

```
function object (o) {
    function f () {}
    f.prototype = o
    return new f()
}

function createAnother (original) {
    var clone = object(original)

    clone.sayHi = function () {
        console.log('hi')
    }

    return clone
}

var person = {
    name: 'zp',
    friends: ['zhao', 'qian']
} 

var anotherPerson = createAnother(person)
anotherPerson.sayHi()
```

## 6. 寄生组合式继承

组合继承的问题: 无论在什么情况下，都会调用两次超类的构造函数，一次是在创建子类型原型的时候(继承方法时)，另一次是子类型构造函数内部(创建实例时)。

寄生组合式继承: 借用构造函数来继承属性，通过原型链的混成形式来继承方法。不必为了指定子类型的原型而调用父类的构造函数，我们需要的只是超类原型的一个副本。

```
function inheritPrototype (subType, superType) {
    var prototype = object(subType.prototype)   // 创建对象
    prototype.constructor = subType             // 增强对象
    subType.prototype = prototype               // 指定对象
}
```

```
function object(o) {
    function F () {}
    F.prototype = o
    return new F()
}

function inheritPrototype (subType, superType) {
    var prototype = object(superType.prototype)
    prototype.constructor = subType
    subType.prototype = prototype
}

function SuperType (name) {
    this.name = name
    this.colors = ['red', 'black']
}

SuperType.prototype.sayName = function () {
    console.log(this.name)
}

function SubType (name, age) {
    // 继承属性
    SuperType.call(this, name)

    this.age = age
}

inheritPrototype(SubType, SuperType)
SubType.prototype.sayAge = function () {
    console.log(this.age)
}
```


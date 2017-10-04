# 创建对象   

虽然Object构造函数或对象字面量都可以用来创建单个对象，但有一个缺点：使用同一个接口创建很多对象，会产生大量的重复代码。
    
##  1. 工厂模式

工厂模式抽象了创建具体对象的过程

```
function createPerson (name, age, job) {
    var o = new Object()
    o.name = name
    o.age = age
    o.job = job
    o.sayName = function () {
        console.log(this.name)
    }
    return o
}

var person1 = createPerson('zp', 25, 'coder')
var person2 = createPerson('lmy', 25, 'gf' )
```

工厂模式解决了多个相似对象的问题，但却没有解决对象识别的问题。

## 2. 构造函数模式

```
function Person (name, age, job) {
    this.name = name
    this.age = age
    this.job = job
    this.sayName = function () {
        console.log(this.name)
    }
}

var person1 = new Person ('zp', 25, 'coder')
var person2 = new Person ('lmy', 25, 'gf' )

```

与工厂模式不同处：

- 没有显式地创建对象
- 直接将属性和方法给了this对象
- 没有return语句

创建一个新的对象经历了以下4个步骤：

1. 创建一个新对象
2. 将构造函数的作用域赋给新的对象(此时this指向新的对象)
3. 执行构造函数中的代码(为这个对象添加属性)
4. 返回新的对象

person1和person2保存着Person的不同实例。两个对象都有一个constructor属性，该属性指向Person。
可以通过**instanceof**操作符判断对象类型

```
person1 instanceof Person // true
person2 instanceof Person // true
person1 instanceof Object // true
perosn2 instanceof Object // true
```

### 2.1 将构造函数当做函数

使用new操作符就为构造函数，不使用就是普通函数。不使用new就会将属性与方法添加到global对象上（window）

### 2.2 构造函数的问题

每个方法都要在每个实例上重新创建一遍。

### 2.3 原型模式

```
function Person () {}

Person.prototype.name = 'zhaopeng'
Person.prototype.age = 25
Person.prototype.job = 'coder'
Person.prototype.sayName = () => {
    console.log(this.name)
}

var person1 = new Person()
person1.sayName() // zhaopeng

var person2 = new Person()
person2.sayName() // zhaopeng

Person.prototype.isPrototypeOf(person1) // true
```

#### 2.3.1 理解原型对象

在创建新的函数时，会自动为该函数创建一个prototype属性，这个属性指向函数的原型对象。默认情况系下，所有原型对象都会自动获得一个constructor属性，指向prototype所在的函数。
方法**Object.getPrototypeOf()**返回对象prototype的值。
对象实例可以通过原型访问保存在原型中的值，但是却不能在对象实例中重写原型中的值。如过在对象实例中添加了一个与原型中相同的属性，对象实例中的属性会屏蔽原型中的属性，但不会重写。

```
function Person () {}

Person.prototype.name = 'zhaopeng'
Person.prototype.age = 25
Person.prototype.job = 'coder'
Person.prototype.sayName = () => {
    console.log(this.name)
}

var person1 = new Person()
var person2 = new Person()

person1.name = 'greg'

console.log(person1.name)    // greg -- 来自实例
console.log(person2.name)    // zhaopeng -- 来自原型
console.log(person1.hasOwnProperty('name'))    // true

delete person1.name
console.log(person1.name)     // zhaopeng -- 来自原型
console.log(person1.hasOwnProperty('name'))    // false
```

**hasOwnProperty()**方法可以用来检测一个属性是存在于实例中还是原型中

#### 2.3.2 原型与in操作符

有两种方式使用in操作符：单独使用和在for-in中使用。
单独使用时如果属性在对象中能够访问到返回true无论是在原型中还是实例中。
使用for-in循环时，循环的时可枚举属性。
要取得对象上所有可枚举的实例属性，可以使用**Object.keys()**方法。
**Object.getOwnProptertyNames()**取得所有实例属性，无论是否可枚举。

#### 2.3.3 更简单的原型语法

```
function Person () {}

Person.prototype = {
    name: 'zhaopeng',
    age: '25',
    job: 'coder'
    sayName: function () {
        console.log(this.name)
    }
}
```

_此时prototype的constructor属性不再指向Person，因为我们创建了一个新的字面量对象覆盖了原来的prototype，此时的constructor属性指向Object_。因此可以指定constructor属性

```
function Person () {}

Person.prototype = {
    constructor: Person,
    name: 'zhaopeng',
    age: '25',
    job: 'coder'
    sayName: function () {
        console.log(this.name)
    }
}
```

但是此时的constructor属性的[[Enumerable]]特性为true，而原生的constructor属性是false的。

```
function Person () {}

Person.prototype = {
    name: 'zhaopeng',
    age: '25',
    job: 'coder'
    sayName: function () {
        console.log(this.name)
    }
}

Object.defineProperty(Person.prototype, 'constructor', {
    enumerable: false,
    value: Person
})
```

#### 2.3.4 原型的动态性

对原型对象所做的任何修改都能够立刻从实例上反映出来

```
var friend = new Person()

Person.prototype.sayHi = function () {
    console.log('hi')
}

friend.sayHi()    // hi
```

但是如果重写了整个原型对象，实例中访问的还是重写之前的原型，新建的实例才能够访问到重写的原型。

```
function Person () {}

var friend = new Person()

Person.prototype = {
    constructor: Person,
    name: 'zp',
    age: '25',
    job: 'coder',
    sayName: function () {
        console.log(this.name)
    }
}

friend.sayName()    // error

var newFriend = new Person()
newFriend.sayName()     // zp
```

#### 2.3.5 原型对象的问题

1. 忽略了为构造函数传递参数的环节，所有实例默认情况下都是相同的
2. 原型上所有的属性都是共享的，属性为函数或者基本值还好，但是如果属性保存的时引用值，所有实例对它的更改都会影响到其他实例

```
function Person () {}

Person.prototype.friends = ['zhao', 'qian', 'sun']

var person1 = new Person()
var person2 = new Person()

person1.friends.push('li')
console.log(person2.friends)    // ['zhao', 'qian', 'sun', 'li']
```

### 2.4 组合使用构造函数模式和原型模式

构造函数模式用于定义实例属性，而原型模式用于定义方法和共享的属性。结果，每个实例都会有自己的一份实例属性的副本，但同时又共享着对方法的引用，最大限度地节省了内存。

```
function Person (name, age, job) {
    this.name = name
    this.age = age
    this.job = job
    this.friends = ['zhao', 'qian']
}

Person.prototype = {
    constructor: Person,
    sayName: function () {
        console.log(this.name)
    }
}

var person1 = new Person('zp', 25, 'coder')
var person2 = new Person('lmy', 25, 'lover')

person1.friends.push('li')

console.log(person1.friends)    // ['zhao', 'qian', 'li']
console.log(person2.friends)    // ['zhao', 'qian']

console.log(person1.friends === person2.friends)    //false
console.log(person1.sayName === person2.sayName)    // true
```

### 2.5 动态原型模式

之前构造函数与原型是独立开来的，动态原型把所有的信息封装在构造函数中，在构造函数中初始化原型

```
function Person (name, age, job) {
    // 属性
    this.name = name
    this.age = age
    this.job = job
    
    // 方法 只会在构造函数第一次调用时执行 不能使用对象字面量重写原型
    if (typeof this.sayName != 'function') {
        Person.prototype.sayName = function () {
            console.log(this.name)
        }    
    }
}

var person = new Person('zp', 25, 'coder')
person.sayName()    // zp
```

### 2.6 寄生构造模式

创建一个函数，该函数的作用仅仅是封装创建对象的代码，然后再返回创建的新对象

```
function Person (name, age, job) {
    var o = new Object()
    
    o.name = name
    o.age = age
    o.job = job
    
    o.sayName = function () {
        console.log(this.name)
    }
    
    return o
}

var person = new Person('zp', 25, 'coder')
person.sayName()    // zp
```              

这个模式可以在特殊的情况下用来为对象创建构造函数。
假设需要创建一个具有额外方法的特殊数组。由于不能直接修改Array构造函数，因此可以使用这个模式

```
function SpecialArray() {
    // 创建数组
    var values = new Array()
    
    // 添加值 使用apply是因为arguments为一个数组,也可以用...代替
    values.push.apply(values, arguments)
    
    // 添加方法
    values.toPipedString = function () {
        return this.join("|")
    }
    
    return values
}
var colors = new SpecialArray('red', 'blue')
console.log(colors)
```  

注意：**返回的对象与构造函数或者与构造函数的原型属性之间没有关系，与在构造函数外部创建的对象是一样的，因此不能通过instanceof判断对象类型**

### 2.7 稳妥构造函数模式

**稳妥对象**: 没有公共属性，而且其方法也不引用this对象。

```
function Person (name, age, job) {
    // 创建要返回的对象
    var o = new Object()

    // 添加私有变量和函数

    // 添加方法
    o.sayName = function () {
        console.log(name)
    }

    return o
}

var friend = Person('zp', 25, 'coder')
friend.sayName()    // zp
```

**除了使用sayName()方法之外没有其他办法访问name的值**


                            
                                
                                    
                                        
                                            
                                                
                                                    
                                                        
                                                            
                                                                    
    

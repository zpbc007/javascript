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

console.log(person1.name)    // greg
console.log(person2.name)    // zhaopeng
```



        
            
                
                    
                        
                            
                                
                                    
                                        
                                            
                                                
                                                    
                                                        
                                                            
                                                                    
    

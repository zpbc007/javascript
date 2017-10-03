# 创建对象   
    虽然Object构造函数或对象字面量都可以用来创建单个对象，但有一个缺点：使用同一个接口创建很多对象，会产生大量的重复代码。
    
##  1. 工厂模式

    工厂模式抽象了创建具体对象的过程。
    
    
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

var person1 = new createPerson('zp', 25, 'coder')
var person2 = new createPerson('lmy', 25, 'gf' )
```

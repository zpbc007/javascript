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
## 1.属性类型

1. 数据属性

 - [[Configurable]]: 表示能否通过delete删除属性从而重新定义属性，能否修改属 性的特性，或者能否把属性修改为访问器属性。直接在对象上定义的属性默认为true。如果定义为false，严格下调用delete报错，非严格忽略delete。且在configurable设为false后除writable外其他属性都不可更改。
 - [[Enumerable]]: 表示能否通过for-in循环返回属性。直接在对象上定义的属性默认为true。
 - [[Writable]]: 表示能否修改属性的值。直接在对象上定义的属性默认为true。修改writable为false的属性严格模式报错，非严格忽略赋值操作
 - [[Value]]: 包含这个属性的数据值。默认为undefined
    
 要修改属性的默认特性，必须使用**Object.defineProperty()**方法
    
    ```
    var person = {}
    Object.defineProperty(person, 'name', {
        witable: false,
        value: 'zp'
    })
    ```
    
2. 访问器属性

访问器属性不包含数据值，它包含一对getter，setter函数（都不是必须的）。访问器属性有以下4个特性。
    
- [[Configurable]]
- [[Enumerable]]
- [[Get]]: 读取属性时调用的函数，默认为undefined。只定义get属性，意味着属性不能写。严格报错，非严格忽略。
- [[Set]]: 写入属性时调用的函数，默认为undefined。只定义set属性，意味着属性不能读。严格报错，非严格忽略。
    
```
var book = {
    _year: 2004,
    edition: 1
}

Object.defineProperty(book, 'year', {
    get: function () {
        return this._year
    },
    set: function (newValue) {
        if (newValue > 2004) {
            this._year = newValue
            this.edition += newValue -2004
        }
    }
})

book.year = 2005
console.log(book.edition)
```
    
3. 定义多个属性

**Object.defineProperties**
    
```
var book = {}

Object.defineProperties(book, {
    _year: {
        writable: true,
        value: 2004
    },
    edition: {
        writable: false,
        value: 2
    },
    year: {
        get: function () {
            return this._year
        }
    }
})
```
    
4. 读取属性的特性

**Object.getOwnPropertyDescriptor()**,返回一个对象，如果为数据属性，返回数据属性的4个值，如果为访问器属性，返回访问器的4个值
    
```
var book = {}
Object.defineProperties(book, {
    _year: {
        value: 2004
    },
    edition: {
        value: 1
    },
    year: {
        get: function () {
            return this._year
        },
        set: function (newValue) {
            if (newValue > 2004) {
                this._year = newValue
                this.edtion += newValue -2004                
            }
        }
    }
})

var descriptor = Object.getOwnPropertyDescriptor(book, '_year')
console.log(descriptor)
```
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
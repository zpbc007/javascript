递归函数是在一个函数通过名字调用自身的情况下构成的：

```
function factorial (num) {
    if (num <= 1) {
        return 1
    } else {
        return num * factorial(num -1)
    }
}
```

上面的代码在执行下面的操作后会报错

```
var anotherFactorial = factorial
factorial = null
console.log(anotherFactorial(4))    // 报错
```

错误的原因是在函数体中指定了函数名factorial，使用arguments.callee可以解决此问题

```
function factorial (num) {
    if (num <= 1) {
        return 1
    } else {
        return num * arguments.callee(num - 1)
    }
}
```

因此在写递归函数时，arguments.callee总比使用函数名更安全。但在严格模式中，不能访问arguments.callee这个属性，访问会报错。此时使用命名函数表达式：

```
var factorial = (function f (num) {
    if (num <= 1) {
        return 1
    } else {
        return num * f(num - 1)
    }
})
```
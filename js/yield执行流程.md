#yield 执行流程

```javascript
var g = function* () {
    console.log('before yield');
    yield console.log('yield');
    console.log('after yield');
};

var i = g();
console.log('before next');
i.next();
console.log('after next');
```
上面的执行流程将会如何输出呢？
```
"before next"
"before yield"
"yield"
"after next"
```
说明了，generator的执行流程为 g() 实际不会执行函数，next()的话，会从函数之前断开的yeild**左表达式**或者**从函数最开始位置**即上文中的before yeild，开始执行，直到执行到 **yield 右表达式**，然后右表达式执行完成后，程序从**这个函数这里断开**，并把右表达式的结果返回，赋给next()执行结果，然后从next()往下执行。

所以 上面的流程可以总结为
1. next()调用， 程序返回对应的generator函数并执行
2. 会从generator函数的 上一个断开的yield 左表达式开始执行，包括把yield的返回值赋值给 yield 左侧的变量，或者从函数的最初开始执行, 直到执行到下一个yield右表达式，并把右表达式的结果返回，程序从这里断开
3. next()返回的对象的value值就是 yield右表达式的的结果值

```javascript
var g = function* () {

    yield console.log('first yielding');
    console.log('after first yield');
    yield console.log('secord yielding');
    console.log('after yield');
};

var i = g();
console.log('before first next');
i.next();
console.log('after first next');
i.next();
```
利用上面结论，非常容易就得到下面的结果了
```
"before first next"
"first yielding"
"after first next"
"after first yield"
"secord yielding"
```
关键点是 第一个next执行后，抛出第一个first yielding, 然后又返回next, 执行after first next
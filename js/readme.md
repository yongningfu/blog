## js难点
### 1. 函数声明和变量声明的提升
```javascript
var a = 100;
function fn() {
    console.log(a);
    var a = 200;
    console.log(a);
}
fn();
console.log(a);
var a;
console.log(a);
var a = 300;
console.log(a);
```
实际被翻译成
```javascript
var a;
function fn() {
    var a;
    console.log(a);
    a = 200;
    console.log(a);
}
var a;
var a;
a = 100;
fn();
console.log(a);
var a;
console.log(a);
var a = 300;
console.log(a);
```
这样就很容易得到结果   
undefined 200 100 100 300

### 2. call apply
很多人都知道 call 和 apply用于改变函数的this,而且call后面跟的是
不确定参数，apply后面跟的是数组参数
### 3. bind
bind一般用于绑定函数的this对象, 但是bind的玩法还有绑定参数  
bind(this, arg1, arg2, arg3....)
  1. bind第一个参数一定要为this对象, 但是如果为null的话 代表不绑定this对象(或原来的this对象不改变)
  2. 后面的参数代表绑定函数参数
    ```javascript
    var name = "win"
    var obj  = {name: "obj"}
    function f(arg1, arg2, arg3) {
      console.log(this.name)
      console.log(arg1);
      console.log(arg2);
      console.log(arg3)
    }

    var f2 = f.bind(obj, 1); //绑定obj为this对象 1为第一个参数
    var f3 = f2.bind(null, 2)//null表示不改变this 由于f2的第一个参数已经固定 2为固定第二个参数
    f3(3, 2)                //这里的话 f3的前2个参数都固定了 那么3只能传递给第三个参数 2无参数传递
    ```
    
    结果obj 1 2 3

### 4. typeof和instanceof的正确使用  
- typeof返回的是javascript的基本数据类型
- instanceof返回的是布尔值(表示一个数据是否是另外一个的实例)
- javascript的基本数据类型有Undefined Null Number String Object function
- 注意typeof null 返回的是Object 这是javascript的一个bug
    ```javascript
    console.log(typeof undefined) //undefined 
    console.log(typeof null)   //注意这个返回object
    console.log(typeof 3)      //number
    console.log(typeof 'aa')   //string
    console.log(typeof [1, 2]) //object
    console.log(typeof {})     //object
    console.log(typeof true)   //boolean
    console.log(typeof function() {}) //function

    //注意
    var s = new String('aaa')
    console.log(typeof s === 'object') ///true 这里是因为 new String返回的是一个对象
    ```
- instanceof 返回的是对象实例(注意区分3 和 new Number(3))
    ```javascript
    var three = 3; 
    console.log(three instanceof Number)    //false
    var threeObj = new Number(3);        
    console.log(threeObj instanceof Number)  //true
    console.log(threeObj instanceof Object)  //true
    
    var s = '3';
    console.log(s instanceof Number)        //false
    var sObj = new String('3');
    console.log(sObj instanceof String)     //true
    console.log(sObj instanceof Object)     //true
    ```
    可以看到,instanceof用于对象实例的判断, 可以用于继承链上子实例的判断,
    比如String继承与Object
- 实例 如何判断是否为string类型
    ```javascript
    //分析 string类型可能是javascript基本的数据类型string也可能是
    // new String()得来的对象
    //则判断方法为
    function isString(str) {
        return typeof str === 'string' || str instanceof String
    }
    ```
### 5 js数组删除(常用splice)
```javascript
var arr = [5, 6, 7]
//删除6
var ret = arr.splice(6, 1)
console.log(ret) //[6]
console.log(arr) //[5, 7]
```


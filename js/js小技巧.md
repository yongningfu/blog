# js小技巧

### 1.把变量值转为对象属性
```javascript
var IO = "socket";
var obj = {[IO]: "value"}
console.log(obj);  //{socket: "value"}
```
在对象的key位置上面， 我们可以使用 [] 将变量套住，那么变量值就变key了
### 2. !!的使用
在看一下源码中，经常看到有人用!!, 觉得奇怪，其实这个是非常有用的一种方式，用于把值转换成 boolean类型
> 这样有什么用呢？ 其实用处是非常大的，很多时候我们需要类型的转化，比如我们从服务器传递数据过来，可能用0 和 1做 false 和 true的标记，那么就意味着，在你的业务代码中，很可能也直接使用0 或者 1, 到时这是一种非常不好的方式，很好是用true 或者 false来进行 bool类型的判断， 而!!恰好提供了非常好的转换方式

```javascript
!!0  //false
!!1  //true
```
### 3.逻辑技巧
逻辑技巧是一个非常有意思的事情，下面会总结子在开发中常用的逻辑技巧。
1. 如果存在xx的话，就返回什么
```javascript
var ret =  num && num * 2 //如果num存在的话，就返回num *2的值
```
2. x存在返回x, x 不存在返回y, y不存在返回z....
```javascript
var ret = x || y || z
```
3. 要么x不存在，要么返回x的某个属性值
```javascript
var ret = !x || x[props]
```
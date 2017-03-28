# js小技巧

### 1.把变量值转为对象属性
```javascript
var IO = "socket";
var obj = {[IO]: "value"}
console.log(obj);  //{socket: "value"}
```
在对象的key位置上面， 我们可以使用 [] 将变量套住，那么变量值就变key了
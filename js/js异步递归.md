递归，一般是我们理解的同步递归，即重复的执行某个函数，js里面的异步递归其实是非常好玩的，我们可以使用setInterval重复执行某个函数, 但是setInterval可能会造成问题，比如忘记clear掉，一般来说，我们也可以使用setTimeout来重复的执行某个函数，而且控制的更加优雅

基本形式为
```js
function  loop() {
  // 一秒后 重复执行 
  var id = setTimeout(loop, 1000);
  // do something
  // 符合条件   那么清楚掉函数 不在执行
  if () {
     clearTimeout(id)
     id = null;
  }
}
```

具体例子(计时器):

```js
var count = 0;
function timer() {
  var id = setTimeout(timer, 1000);
  console.log(count++);
  if (count === 11) {
     clearTimeout(id);
  }
}
```
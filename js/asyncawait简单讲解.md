# async await简单讲解

es7的特性，平时用的不多，后面应该全面使用
**async**: await的执行环境， 声明这个是一个异步调用的函数
**await**: 后面一般跟一个promise(当然还有其他情况，不过一般不提倡),  当遇到await的时候，会使用协程的机制，函数暂时从断开，去执行其他的函数，当await后面的promise对象为确实状态的时候（即resolve  rejected）， 协程重新从断开的位置调用这个函数，await返回值是promise的对象携带的的数据
下面是一个比较有意思的例子

```javascript
//一个延迟delay时间才 确实状态的promise
//如果放在await后面的话，函数会断开delay时间，才重新回调这个函数，所以起到sleep的作用
var sleep = (delay) => new Promise((resolve) => setTimeout(() => resolve('promise 数据'), delay));

(async () => {
  for (var i = 0; i < 5; i++) {
    //函数从这里断开1s, 直到sleep变成 resolve状态的时候才重新调用
    let data = await sleep(1000);
    console.log(data); //promise 数据
    console.log(new Date, i);
  }
  await sleep();
  console.log(new Date, i);
})();
```
原文的代码不太友好
```javascript
var sleep = delay => new Promise(resolve => setTimeout(resolve, delay));
(async () => {
  for (var i = 0; i <= 5; i++) {
    console.log(new Date, i);
    await sleep(1000);
  }
})();
```
# uuid的获取
如何才能获取一个uuid，有很多算法，比如md5哈希等等，在阅读redux-saga源码的过程中，发现一个十分简单的方法，利用闭包的原理，来获取uuid
```javascript
function autoInc(seed = 0) {
  return () => ++seed
}
const uid = autoInc()
uuid() //1
uuid() //2
```
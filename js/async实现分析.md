# async实现解读
解读前我们要首先关注几个问题: 
1. 如何确保async返回一个promise, 而且状态必须等里面全部await后面的promise确定完后才确定
2. 如何确保所有的async里面直接抛出的异常能被捕获
3. 如何确保await后面的promise为reject的时候，async立即停止执行，而且返回promise为reject

上面的三点是处理async执行流中最关键的三点。
2 和 3 如何解决呢？ 想一下，生成器在迭代的时候，上面时候会抛出异常呢？ 是不是在生成器执行 next函数期间，如果中途遇到异常的话，是不是会外抛出？ 所以我们只需要在生成器执行next处捕获异常就行了
如何在 await 后面的promise为 reject的时候，抛出异常呢？我们可以获取yield后面的promise对象，如果执行完后为 reject， 那么直接使用 generator的 .throw api，往生成器内部抛异常，如果内部无法捕获的话，那么它会在对应的next出往外抛，即又和2一样了，我们同样可以在next处 来捕获 await后面的 promise 为reject异常，只是这个异常是我们自己抛的而已。

```javascript
function spawn(genF) {
  //直接返回一个promise 解决1
  return new Promise(function(resolve, reject) {
    var gen = genF();
    function step(nextF) {
      // 这里捕获两种异常，一种为 generator内部可能直接抛的异常
      // 一种为我们跑到await后面的promise为reject时候，我们主动抛的异常
      try {
        var next = nextF();
      } catch(e) {
        return reject(e);
      }
      //生成器执行完成后 才resovle 解决1
      if(next.done) {
        return resolve(next.value);
      }
      // 为什么使用resolve呢？ 兼容的原因，如果next.value可以为promise，也可以为
      //其他普通值
      Promise.resolve(next.value).then(function(v) {
        step(function() { return gen.next(v); });
      }, function(e) {
        step(function() { return gen.throw(e); });
      });
    }
    step(function() { return gen.next(undefined); });
  });
}
```
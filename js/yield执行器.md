##  一个generator自动执行器

最近看 redux-saga的源代码， 作者写的yield执行器代码非常不错，详细的分析一下

1.  支持异常处理
2.  支持thunk
3.  支持promose
4.  支持常量值
 
** 利用的根本: **
1. 如果一个promise (假设A)的resolve函数传递的是一个promise(假设B)对象，那么这   个promiseA对象是无法立即确定状态为Onfulfilled的，必须先等等promiseB的状态确定后，然后将promiseB的内部value，传递给promiseA的resove. 即再执行一遍promiseA的resolve，只是这时候的参数值不为promise对象，而是一个具体值, 所以此时promiseA的状态获取,value为从promiseB传递过来 
2. 扩展: 每个then里面的onfulfilled函数的返回值，都直接作为.then这个函数本身返回的promise对象的resolve参数，其中如果返回值为promise的话，那么按照1的规则

```javascript
function runGenerator(generatorFUN, initialValue) {
  const generator = generatorFUN(initialValue);
  iterate(generator);

  function iterate(generator) {
    step();
    // step传递的值为  上一个yield右表达式的"求出的值"，isError标记上一个yield是否发生异常
    //利用arg 赋值给上一个yield的左表达式值，并返回下一个yield右表达式的值
    function step(arg, isError) {

      //如果上一个next执行完后发送错误了，那么就抛出一个异常
      //由于从上一个yield左边表达式开始执行，所以恰好处理异常的话，就是在上一个yield那里捕获异常
                            
      //这里的arg的值可能有几种情况获取
      /**
       * 
       * 一种是 yield new Promise()
       * 一种是 yield () => {return value}
       * 一种是 yield () => {return new Promise}
       * 一种是 yield value 那么arg就直接是value
       * 
       * 而且arg的值, 不能直接是promise， 而是promise执行完成后，确实的内部的value(即传递给then注册的值)
       * 
       * 如何做到这一点呢？ 下面再说
       */
      const {value: express, done} = isError ? generator.throw(arg) : generator.next(arg); 

      let response;
      if (!done) {  //一定要有 不然无限递归
        //由迭代器的知识 我们知道 express 是yield右侧的值
        if (typeof express === 'function') {
          response = express();  //response 可能为 promise  或者value
        } else {
          response = express;    //这里response可能为promise 或者value
        }

        //要求，如果为promise， 那么arg的值为 promise确实状态后的value值，如果为value
        // 那么就直接设置为value----你想到什么了么？
        //再看看我上面说的根本，promise的巧妙之处

        //如果一个promise A的resolve传递的是 promise B对象，那么这个promise A对象并不一定就
        //里面是onfulfilled状态，而是根据 promise B对象的状态决定，如果promiseB 对象为reject，
        // 那么promise A对象也是reject，resovle也同理，而且会把promiseB 对象的value传递给 promiseA
        //的 resolve (promise B状态确定后，重新执行一遍 promiseA的promise resolve 只是参数value不为promise了)

        //这句话是非常巧妙的，respone可能为 value 或者 promise对象
        //Promise.resolve 不一定为 Onfulfilled 状态的，得看 response
        //而且response执行完成后的值 可以直接传递给 Promise.resolve 的resolve, 即then 里面的next参数
        //如果respone为 reject的话 执行后面的 err => next, true

        Promise.resolve(response).then(step, err => step(err, true));
      }
    }
  }
}
```

//删除注释后
```javascript
function runGenerator(generatorFUN, initialValue) {
  const generator = generatorFUN(initialValue);
  iterate(generator);

  function iterate(generator) {
    step();
    function step(arg, isError) {
      const {value: express, done} = isError ? generator.throw(arg) : generator.next(arg); 
      let response;
      if (!done) {
        if (typeof express === 'function') {
          response = express();  
        } else {
          response = express;    
        }
        Promise.resolve(response).then(step, err => step(err, true));
      }
    }
  }
}
```



测试一下
```javascript
function* gen1() {
  yield console.log(1);
  yield console.log(2);
  yield console.log(3);
}
runGenerator(gen1);
```
```javascript
function* gen2() {
  var value1 = yield Promise.resolve('promise');  //直接返回promise
  console.log(value1);

  var value2 = yield () => Promise.resolve('thunk prommise')  //thunk里面返回promise
  console.log(value2);

  var value3 = yield "string type";
  console.log(value3);

  var value4 = yield () => "thunk string type"
  console.log(value4);
}

 runGenerator(gen2);
```
异常情况 可以在看看上面的异常处理流程 是由谁抛的 为啥我们能接收到 根据什么理由抛的异常

```javascript
function* gen3() {
  try {
    var value1 = yield new Promise((resolve, reject) => setTimeout(reject, 0, 'reject error'));
  } catch (error) {
    console.log(error);
  }
  var value2 = yield 3;
  console.log(value2);
}

runGenerator(gen3);
```
##  美团实现的promise源码分析

原文写的有点繁琐，下面是它的代码加上直接看时候的理解分析 [原文地址](http://tech.meituan.com/promise-insight.html)
promise有几个要点要注意:

1. 每个promise对象都有一个value值，它就是 传递给.then注册函数的的参数值
2. then函数返回一个新的promise对象
3. then的参数为两个函数 onFulfilled,  onrejected  以onFulfilled举例，如果onFulfilled这个函数返回的是一个普通的值时， 那么这个值就直接赋予.then本身返回的这个promise对象的value值，如果**onFulfilled返回一个promise对象的时候**,  这个是最麻烦的处理情况，(假设onFullfilled返回的promise为promiseB, 对应的.then返回的promise为promiseC)， 那么必须保证，promiseB的状态确定后，才执行promiseC的resolve(即确定promiseC的状态)， 而且promiseB的value要设置为promiseC的value
4. 回调是异步执行的

理解好上面几点，就可以看下面的分析了

```javascript
function Promise(fn) {
    var state = 'pending',
        //这个value记录的是promise本身值 即用于它.then(onFulfilled)的参数值
        value = null,
        //记录它对应的异步回调对象，当resolve执行的时候，会异步执行这些函数
        deferreds = [];

    //then会将一下符合条件的回调加入 deferred    
    

    //须知
    /*  new Promise_A_(resolve => resolve()).then(onFulfilled_B_)_C_ 

    最麻烦的是 onFulfilled 返回也返回一个 promise对象 下面讨论这种情况

    * 假设第一个 new Promise 为 Promise A
    * onFulfilled 返回的promise 为 Promise B
    * then 本身返回的Promise 为 Promise C
    * 必须是 A 状态确定即执行resolve  然后在 B 状态确定  然后再是 C状态确定
    * 那么如何才能确保上面的流程执行？
    * 由于执行resolve状态才能确定， 那么上面执行情况一定是
    * A 中的 resolve 中 调用 B 的 resolve  B的resolve中 调用C的resolve
    * 参数如何传递？ 利用好promise 里面的value就行
    */


    this.then = function (onFulfilled, onRejected) {

        //每个then自动返回一个promise,
        return new Promise(function (resolve, reject) {

        //这里可以获取 这个then创建的promise的resolve函数 因为它里面会调用fn(resolve)
        //获取这个resolve函数可以做流程控制 控制这个resolve什么时候执行 也就是promise C 
        //什么时候状态确定
        //promiseC 什么时候状态确定呢？它必须在 onFulfilled 返回的promiseB状态确定后才能确定
        //由于PromiseB 在PromiseA的 resolve函数里面执行 所以我们暂时把 onFulfilled 和 这个resolve
        //保存起来
        //promise中的fn是 立即执行的 不用担心handle获取不到数据
            handle({
                onFulfilled: onFulfilled || null,
                onRejected: onRejected || null,
                resolve: resolve,
                reject: reject
            });
        });
    };


    //这个handler是非常重要的，异步里面当前promise状态确定了以后也直接调用它
    //之前说了 promsieA 状态确定 才执行promiseB的then C也同理
    function handle(deferred) {

        //当当前的promise对象为pendding时候 直接加入到异步回调中
        if (state === 'pending') {
            deferreds.push(deferred);
            return;
        }

        //当前promise状态已经确定了话 就直接执行then参数函数，当然如果存在话
        var cb = state === 'fulfilled' ? deferred.onFulfilled : deferred.onRejected,
            ret;
        //这里是非常巧妙的 如果then参数不处理的话，自动交给then返回的promise对象处理
        //起到冒泡的作用
        if (cb === null) {
            cb = state === 'fulfilled' ? deferred.resolve : deferred.reject;
            //如果then参数不出来 那么之前把promiseA的value复制给promiseC的resolve
            cb(value);
            return;
        }
        
        //如果then的参数处理了 比如onFulfilled
        //那么把onFuillfilled的返回值 当成value传递给promiseC
        //这个返回值 分为两种情况 一种为 promsie 一种为 普通值
        //如果为promise的话 那么必须等待这个promiseB状态确定后 promiseC才执行它的resolve
        ret = cb(value);
        deferred.resolve(ret);
    }
     //执行resolve即能确定自己的状态
    function resolve(newValue) {
         //如果它的参数是promise的话
        if (newValue && (typeof newValue === 'object' || typeof newValue === 'function')) {
            var then = newValue.then;
            if (typeof then === 'function') {
                //如果resolve参数为promise对象的话，那么先确定参数的promise状态，然
                //后在确定自己本身的promise状态，
               // 这里就做到了流程控制，而且把自己本身的resolve，reject传递在      
               //newValue的 then函数，恰好还能接受newValue这个promse的内置value，
               //这样就既做到了流程控制，又十分巧妙的获取了promsie的value

                then.call(newValue, resolve, reject);
                return;
            }
        }

        //如果不是promise对象的话, 直接调用异步回调 而且设置promise对象的value
        state = 'fulfilled';
        value = newValue;
        finale();
    }

    function reject(reason) {
        state = 'rejected';
        value = reason;
        finale();
    }

    function finale() {

        // resolve后执行的回调 必须异步的
        setTimeout(function () {
            deferreds.forEach(function (deferred) {
                handle(deferred);
            });
        }, 0);
    }

    fn(resolve, reject);
}
```
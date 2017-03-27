#  js异常处理总结

###  先看最基础的情况
```javascript
function children() {
    throw new Error("子报错");
}

function parent() {
    children(); //有异常抛出 函数中断执行
}
parent();
console.log("cccccccc");
```

try catch 单层嵌套
```javascript
function children() {
    throw new Error("子报错");
}
function parent() {
    //可以在上一层函数捕获下层函数的异常
    try {
        children(); //有异常抛出 函数中断执行
    } catch(error) {
        console.log(error);
    }
}
parent();
```

多级嵌套，捕获下面的异常
```javascript
function children() {
    throw new Error("子报错");
}

function parent() {
    children(); //有异常抛出 函数中断执行
}

//多级嵌套也是没问题的, 异常回层层往上抛
try {
    parent();
} catch (error) {
    console.log(error);
}
```

1. 预期异常：参数不合法，前提条件不符合，通常直接throw
2. 非预期异常: js运行时异常，来着依赖库异常
3. 可以直接在异常上面提供一下附加属性来提供上下文

```javascript
function children() {
    var err =  new Error("子报错");
    err.statusCode = 404;  //附加的属性 提供上下文
    throw err;
}

function parent() {
    children(); //有异常抛出 函数中断执行
}

try {
    parent();
} catch (error) {
    console.log(error.statusCode); //404
    console.log(error);
}
```


### 异步回调异常处理

```javascript
function asyncCallbackError(callback) {
    
    setTimeout(() => {
        throw new Error("异步出现了异常");
        callback();
    }, 0);
}

function callAsync() {
    asyncCallbackError(function() {
    });
}

//能不能在callAsync外面嵌套一个 try catch 处理异常呢？

try {
    callAsync();
} catch(error) {
    console.log(error);
}
```

上面的代码是不行的，执行栈里面的try catch 无法捕获异步队列中抛出的异常
> 为啥 执行栈中的try catch无法捕获到异步队列的异常？
执行栈都执行完了，异步队列才开始执行，所以执行栈无法捕获异步
函数抛出的异常

```
所以对于异步函数，我们对异常的处理原则为, 异步函数里面使用自己的try catch 
自处理异常，然后通过它的回调函数的参数 callback(error, value) 在上一层的调用
中判断是否异步出现了异常，如果出现的话， error即第一个参数不为空
```

```javascript
function asyncCallbackError(callback) {
    
    setTimeout(() => {

        //异步函数必须自己处理自己的异常
        try {

            //if (发送了异常) {
            throw new Error("异步出现了异常");
            //}
            //else  没有异常 {
            //callback(null, value);  无异常的话 第一个参数设置为null 第二个自己的值
            //}
        } catch(error) {
            callback(error);
        }
    }, 0);
}

function callAsync() {

    //callback 判断获取异步异常值
    asyncCallbackError((error, value) => {
        if (error) { //如果异步出现异常
            console.log(error);
        } else {
        }
    });
}

callAsync();
```

### promise 异常

promise本身只是一个 基于事件分发的状态管理器
它不为我们管理异常，所以promise里面的异常必须我们自己try catch
当 发生异常的时候 就设置当前 promise的状态为 reject

十分注意一点是: 如果一个 reject状态的promise没有进行处理，那么它

```javascript
var p = new Promise((resolve, reject) => {
    throw new Error("异常发生了");
    resolve();
});

console.log(p);
```
上面的代码 会直接抛出一个异常，程序无法执行，因为promsie不会为我们自动处理异常

```javascript
var p = new Promise((resolve, reject) => {
    try {
        throw new Error("异常发生了");
    } catch(error) {

        //不让它的状态立即改变，防止reject进入异步队列，这样可以让then注册的回调不是立即执行
        //tips: 一个promise状态确定后，通过then注册的回调 会立即执行
        setTimeout(() => {
            reject(error); //内部直接主动管理异常
        }, 0);
    }
});

p.then(() => {console.log("resolve")}, (error) => { console.log(error)});
```
promise内部发生异常，统一我们自己在内部try catch 处理，并设置它的状态为reject
然后通过then 注册reject回调处理，即promise异常处理通过reject状态处理, 而且这里
再次强调一下，**如果一个promise的reject状态没得到处理的话，会抛出一个异常**

```javascript
var p = new Promise(function (resolve, reject) {
    setTimeout(reject, 0);
  });

p.then(() => {});
```
上面的代码报异常没有捕获，promise状态为reject的话，需要处理reject情况
改成 ```p.then(()=>{}).catch(()=> {});```就可以了
> 注意: 目前node里面Promise的reject没有处理，抛的异常不会阻止程序的执行，但是
未来这种情况会中断node的执行

### generator的异常

如果在一个generator 函数体内抛出一个异常 它会怎么样呢？
 
var g = function* () {
    throw new Error("异常发生了");
    yield console.log('yielding');
};

var i = g();
console.log(i.next());


结果程序遇到异常 也直接不执行了，所以generator也不会帮我们处理异常
我们需要自己手动处理

```javascript
var g = function* () {
    try {
        throw new Error("异常发生了");
    } catch(error) {
        yield console.log('yielding');
    }

};

var i = g();
console.log(i.next());
```

generator 函数体内抛出的异常 还能在函数体外捕获
但是注意捕获的时间，哪个next 执行会抛出异常，就在哪次上门捕获
当然也可以 try catch 包含多个next
```javascript
var g = function* () {
    throw new Error("异常发生了");
    yield console.log('yielding');
};

var i = g();
try {
    console.log(i.next());
} catch(error) {
    console.log(error);
}
```

嵌套多个next 可能抛出的异常 异常

```javascript
var g = function* () {
    yield console.log('yielding');
    throw new Error("异常发生了");
};

var i = g();
try {
    i.next();
    i.next(); //第二个才会抛出异常
} catch(error) {
    console.log(error);
}
```
**yield 自带异常api  Generator.prototype.throw()**

Generator函数返回的遍历器对象，都有一个throw方法，
可以在函数体外抛出错误，然后在Generator函数体内捕获。

应该在 函数体里面的哪里捕获呢？ 想想 函数体外利用throw抛异常，
如何函数体内可以接受这个异常的话，那是不是说明现在的函数执行流程应该有
跑回到 generator函数里面，而generator会从上一个的yeild 左表达式开始执行，
所以，为了捕获异常，我们应该 try catch 前一个yield

当然 它也是可以直接try catch 多个yeild 如果不确定那个抛出的话，只要在对应的
抛出异常的外传yeild就可以了

```javascript
var g = function* () {
    try {
        yield console.log('yielding'); //从这里断开的 需要从左表达式处接受异常
    } catch(error) {
        console.log(error);
    }
};

var i = g();
i.next();
i.throw(new Error("外部异常")); //throw 传递的参数可以传递到generator里面
```

如果连续利用多个 generator.throw 那么如果异常无法在generator函数体内进行捕获，
那么它就会在函数generator体外抛出这个异常 我们可以在体外处理

注意: throw的话 相当于 一个next 并且 同时throw Error 所以它会对生成器函数内部迭代一次

```javascript
var g = function* () {
    try {
        yield console.log('yielding'); //从这里断开的 需要从左表达式处接受异常
    } catch(error) {
        console.log(error);
    }
};

var i = g();
i.next();
i.throw(new Error("外部异常")); //throw 传递的参数可以传递到generator里面
i.throw(new Error("外部异常2"));  //这个异常无法在generator函数里面捕获，所以它往外面抛 

//所以改写为
var i = g();
i.next();
i.throw(new Error("外部异常")); //throw 传递的参数可以传递到generator里面
try {
    i.throw(new Error("外部异常2"));  //这个异常无法在generator函数里面捕获，所以它往外面抛 
} catch (error) {
    console.log(error);
}

```
所以对于生成器函数来说 我们使用 它的 .throw函数抛出异常 在生成器函数里面使用 try catch 捕获 yield异常，
内部无法捕获的话，使用 try catch在对应的next处捕获

### await async异常

先直接在async里面抛出异常看看
```javascript
async function f() {
  throw new Error('出错了');
}

f().then(
  v => console.log(v),
  e => console.log(e)
)
```

**上面并不会报错, 不会终止程序的执行**，因为async函数是一个自执行的generator，它里面会捕获异常，
然后返回一个reject的promise, 所以我们可以在async里面把异常转为promise的reject

所以async无论如何都会返回一个promise，如果内部有异常，那么返回一个reject的promise,
value为异常error, 如果返回一个值，那么返回一个resolve的promise,value为这个值，如果
返回一个promise，那么async就直接返回这个promise


只要有一个await后面的promise是reject， 那么async就会中断，并且返回一个reject的promise, 
(正如之前说过promise为reject的话，而且未处理，那么它会抛出一个异常)

那 如果我想 即使 await后面是一个reject的promise，我如何还能让它往下执行呢？
可以用一个 try catch 将对应的await 包裹住，这样的话，它就可以捕获promise未处理抛出的异常
或者把这个promise给处理了


**async 处理promise reject异常的方法**
try catch 方法
```javascript
async function f() {
  try {
    await Promise.reject('出错了');
  } catch(e) {
  }
  return await Promise.resolve('hello world');
}

f()
.then(v => console.log(v))
// hello world
```
把未处理的 promise给处理了 即利用.catch 这样的话 返回一个resolve的promise
```javascript
async function f() {
  await Promise.reject('出错了')
    .catch(e => console.log(e));
  return await Promise.resolve('hello world');
}

f()
.then(v => console.log(v))
// 出错了
// hello world
```

> 为何async内部会帮我们处理异常？ 如何帮的？

```javascript
//代码来自阮一峰es6


function spawn(genF) {
  return new Promise(function(resolve, reject) {
    var gen = genF();
    function step(nextF) {

      //可以看到 每次迭代都将nextF用try catch 包裹起来, 
      //当生成器执行过程中抛出异常时，就可以在外部捕获异常，并且立即返回 reject(e)      
      try {  
        var next = nextF();
      } catch(e) {
        return reject(e); //有异常的话 直接返回一个reject promise
      }
      //需要理解的是 为啥在这里捕获异常？？？ 前面我们已经说过，生成器是一个迭代器，按照next的流程执行，
      // 只有next执行的时候，generator才会开始执行，执行才可能发生异常，而且生成   
      // 器在next执行期间，内部发生的
      //异常没有被捕获的话，可以往外抛，即再对应的next函数处，我们可以捕获异常
      // nextF()的执行 恰好就是执行generator的next函数

      if(next.done) {
        return resolve(next.value);  //async的状态直到 generator全部执行完 才确定
      }
      Promise.resolve(next.value).then(function(v) {
        step(function() { return gen.next(v); });
      }, function(e) {
        //发生错误，往生成器里面抛异常，如果这个异常在生成器内部没有被捕获，那么
       //它可以往外抛
        step(function() { return gen.throw(e); });
      });
    }
    step(function() { return gen.next(undefined); });
  });
}
```
# Promise
**Promise 简述**  
**基础版本**  
1.默认有 3 个状态 进行中，成功，失败  
2.用户传入执行器 executor,executor 接受两个参数，分别是 resolve 和 reject  
3.resolve 和 reject 会改变默认的状态，并存储成功或者失败的值  
4.promise 必须有一个 then 方法，then 接收两个参数，分别是 promise 成功的回调 onFulfilled, 和 promise 失败的回调 onRejected

- 如果调用 then 时，promise 已经成功，则执行 onFulfilled，参数是 promise 的 value；
- 如果调用 then 时，promise 已经失败，那么执行 onRejected, 参数是 promise 的 reason；
- 如果 then 中抛出了异常，那么就会把这个异常作为参数，传递给下一个 then 的失败的回调 onRejected；
- executor 执行的是异步，此时 then 调用时，仍然处于 pending.
- 我们需要先将成功和失败的回调分别存放起来，在 executor()的异步任务被执行时，触发 resolve 或 reject，依次调用成功或失败的回调

```javascript
const PENDING = 'pending'
const REJECT = 'reject'
const FULFILLED = 'fulfilled'

class Promise(){
    constructor(executor){
        this.status = PEDNING;
        this.value = undefined;//保存成功状态的值
        this.reason = undefined;//保存失败状态的值
        this.onResolveCallbacks = []
        this.onRejectedCallbacks = []

        const resolve=(value)=>{
            if(this.status=PEDNING){
                this.status = FULFILLED
                this.value = value
                //此处执行
                this.onResolveCallbacks.forEach(fn=>fn())
            }
        }

        const reject = (reason)=>{
             if(this.status = PENNING){
                 this.status = REJECT
                 this.reason = reason
                 this.onRejectedCallbacks.forEach(fn=>fn())
             }
        }

        try{
            executor(resolve,reject)
        }catch(e){
            reject(e)
        }
    }

    then(onFulfilled,onRejected){
        if(this.status === REJECT){
           onFulfilled(this.value)
        }
        if(this.status === FULFILLED){
            onRejected(this.reason)
        }
        if(this.status === PENDING){
            //如果此时状态是进行中，则下一步成功失败的回调存储起来
            this.onResolveCallbacks.push(()=>{
                onFulfilled(this.value)
            })
            this.onRejectedCallbacks.push(()=>{
                onRejected(this.reason)
            })
        }
    }

}
```

**链式调用版本（在基础版本上新增）**  
1.then 的参数可以缺省，如果 onFulfilled 或者 onRejected 不是函数，则将其忽略，且依旧可以在下面的 then 中获取到之前返回的值  
2.promise 可以 then 很多次，因为每次 then 都返回一个新的 promise  
3.我们将 then 返回值定义为 x,如果 x 是普通值，我们认为是成功的回调  
4.如果 then 中抛出异常，我们认为走失败的回调  
5.x 是 promise，那么会等这个 promise 执行完，成功走成功回调，失败走失败回调

```javascript
const PENDING = "pending";
const REJECT = "reject";
const FULFILLED = "fulfilled";
//x为then中的传递的成功或者失败的返回值
//x的类型决定promise2成功还是失败
function resolvePromise(promise2, x, resolve, reject) {
  //对于文档2.3.1 自己抛出自己
  if (x === promise2) {
    reject(new TypeError("异常情况"));
  }
  //如何判断是promise（对象，有then方法）
  if ((typeof x === "object" && x !== null) || typeof x === "function") {
    let called = false; // 保证成功和失败的回调只会执行一次
    try {
      let then = x.then; //有then 且then 是函数
      if (typeof then === "function") {
        //2.3.3.3此时认为他就是promise
        then.call(
          x,
          (y) => {
            //不用x.then 是因为上面取过，避免不必要的错误
            if (called) {
              return;
            }
            called = true;
            resolvePromise(promise2, y, resolve, reject);
          },
          (r) => {
            if (called) {
              return;
            }
            called = true;
            reject(r);
          }
        );
      } else {
        resolve(x);
      }
    } catch (e) {
      reject(e);
    }
  } else {
    resolve(x);
  }
}

class Promise {
  constructor(executor) {
    //2。用户传入executor
    this.status = PENDING;
    this.value = undefined;
    this.reason = undefined;
    this.onResolvedCallbacks = []; //存放成功的回调
    this.onRejectedCallbacks = [];

    const resolve = (value) => {
      if (this.status === PENDING) {
        this.status = FULFILLED;
        this.value = value;
        this.onResolvedCallbacks.forEach((fn) => fn());
      }
    };

    const reject = (reason) => {
      if (this.status === PENDING) {
        this.status = REJECT;
        this.reason = reason;
        this.onRejectedCallbacks.forEach((fn) => fn());
      }
    };
    try {
      executor(resolve, reject); //3。代码可能抛出错误
    } catch (e) {
      reject(e);
    }
  }

  then(onFulfilled, onRejected) {
    //4。then的时候判断成功失败
    //1处理不是函数的情况
    onFulfilled = typeof onFulfilled === "function" ? onFulfilled : (v) => v;
    onRejected =
      typeof onRejected === "function"
        ? onRejected
        : (reason) => {
            throw reason;
          };
    //不停的创建新的promise，来实现链式调用
    let promise2 = new Promise((resolve, reject) => {
      if (this.status === FULFILLED) {
        try {
          let x = onFulfilled(this.value);
          resolvePromise(promise2, x, resolve, reject);
        } catch (e) {
          reject(e);
        }
      }
      if (this.status === REJECT) {
        try {
          let x = onFulfilled(this.value);
          resolvePromise(promise2, x, resolve, reject);
        } catch (e) {
          reject(e);
        }
      }
      //5。异步的情况，既不成功也不失败
      if (this.status === PENDING) {
        this.onResolvedCallbacks.push(() => {
          try {
            let x = onFulfilled(this.value);
            resolvePromise(promise2, x, resolve, reject);
          } catch (e) {
            reject(e);
          }
        });
        this.onRejectedCallbacks.push(() => {
          try {
            let x = onFulfilled(this.value);
            resolvePromise(promise2, x, resolve, reject);
          } catch (e) {
            reject(e);
          }
          //promise 只要返回是普通值，下一次都走resolve
        });
      }
    });
    return promise2;
  }
}
```

**Promise 的 API**  
**Promsie.resolve()**  
Promise.resolve(value) 用结果 value 创建一个 resolved 的 promise。

```javascript
Promise.resolve(Promise.reject(1))
  .then(
    (v) => {
      console.log(0);
    },
    (e) => {
      console.log(e);
    }
  )
  .catch((e) => {
    console.log(2, e);
  });
Promise.resolve("123213");
//如果是接受一个promise对象，则执行他的then方法
//如果接受一个其他值或者空值，则返回一个promise的成功回调
Promise.resolve = function (val) {
  return new Promise((resolve, reject) => {
    if (val instanceof Promise) {
      val.then(resolve, reject);
    } else {
      return resolve(val);
    }
  });
};
```

**Promise.reject()**  
Promise.reject(error) 用 error 创建一个 rejected 的 promise。

```javascript
Promise.reject(reason){
    return new Promsie((resovle,reject)=>{
          reject(reason)
    })
}
```

**Promsie.all()**  
当所有给定的 promise 都 resolve 时，新的 promise 才会 resolve，并且其结果数组将成为新 promise 的结果。  
如果任意一个 promise 被 reject,那么 Promise 就会立即 reject

```javascript
//写法一
Promsise.all = function(promiseArr){
    return new Promsise((resolve,reject)=>{
        let arr = []
        let time = 0
        promsieArr.forEach((item,index)=>{
            if(! item instanceof Promsie){
                arr[index] = item
                time ++
            }
            item.then(value=>{
                arr[index] = value
                time++
                if(time === promiseArr.length){
                    resolve(srr)
                }

            },reason=>{
                reject(reason)
            })
        })
    })
}
//写法二
Promise.all = function(promiseArr){
    return new Promise(resolve,reject){
        let arr = [],time = 0;
        for(let i = 0;i<promiseArr.length;i++){
            Promise.resovle(promiseArr[i]).then(value=>{
                  arr[i]= value
                  if(i === promsieArr.length){
                      return resolve(arr)
                  }
            }),reject)
        }
    }
}
```

**Promsie.allSettled()**  
来获取 **所有** 给定的 promise 的结果，即使其中一些被 reject

```javascript
Promsie.allSettled = funtion(promiseArr){
    return new Promise((resolve,reject)=>{
       let arr= [],time = 0;
       promiseArr.forEach((item.index)=>{
           Promise.resolve(item).then(val=>{
               time += 1
               arr[index]={
                   status:'fulfilled',
                   value:val
               }
           },reason =>{
               time +=1
               arr[index]={
                   status:'rejected',
                   reason:reason
               }
           }).finally(()=>{
               if(time === promiseAre.length){
                   return resolve(arr)
               }
           })
       })
    })
}
```

**Promise.race()**  
（成功或失败不关心结果）只等待第一个 settled 的 promise 并获取其结果（或 error）。第一个 settled 的 promise “赢得了比赛”之后，所有进一步的 result/error 都会被忽略。

```javascript
Promise.race = function(PromiseArr){
    return new Promise((resolve,reject)=>{
        PromiseArr.forEach((item)=>{
            Promsie.resolve(item).then(val=>{
                resolve(val)
            },reason=>{
                reject(reason)
            })
          }
        })
    })
}

```

**Promise.any()**  
 Promise.any 只等待第一个 fulfilled 的 promise，并将这个 fulfilled 的 promise 返回  
使用场景：从最快的服务器检索资源 来自世界各地的用户访问网站，如果你有多台服务器，则尽量使用响应速度最快的服务器，在这种情况下，可以使用 Promise.any() 方法从最快的服务器接收响应

```javascript
Promise.any = function (promiseArr) {
  return new Promsie((resolve, reject) => {
    let arr = [],
      time = 0;
    promiseArr.forEach((item, index) => {
      Promsie.resovle(item).then(
        (val) => {
          resolve(item);
        },
        (reason) => {
          time++;
          arr.push(item);
          if (time === promiseArr.length) {
            reject(arr);
          }
        }
      );
    });
  });
};
```

**Promise.prototype.catch()**  
**Promise.prototype.finally()**  
无论成功或者失败都会执行

```javascript
Promise.prototype.catch = function(callback){
    return this.then(null,callback)
}

Promise.prorotype.fianlly = funtion(callback){
    return this.then(value=>{
        return Promise.resolve(callback()).then(()=>value)
    },reason=>{
        return Promise.resolve(callback().then(()=>{
            throw reason
        }))
    })
}
```

**练习题**

```javascript
new Promise((resolve, reject) => {
  console.log("外部promise");
  resolve();
})
  .then(() => {
    console.log("外部第一个then");
    return new Promise((resolve, reject) => {
      console.log("内部promise");
      resolve();
    })
      .then(() => {
        console.log("内部第一个then");
      })
      .then(() => {
        console.log("内部第二个then");
      });
  })
  .then(() => {
    console.log("外部第二个then");
  });

//外部promise
//外部第一个then
//内部promise
//内部第一个then
//外部第二个then
//外部第二个then
```

```javascript
new Promise((resolve, reject) => {
  console.log("外部promise");
  resolve();
})
  .then(() => {
    console.log("外部第一个then");
    new Promise((resolve, reject) => {
      console.log("内部promise");
      resolve();
    })
      .then(() => {
        console.log("内部第一个then");
      })
      .then(() => {
        console.log("内部第二个then");
      });
  })
  .then(() => {
    console.log("外部第二个then");
  });
//外部promise
//外部第一个then
//内部promise
//内部第一个then
//外部第二个then
//内部第二个then
```

```javascript
new Promise((resolve, reject) => {
  console.log("外部promise");
  resolve();
})
  .then(() => {
    console.log("外部第一个then");
    let p = new Promise((resolve, reject) => {
      console.log("内部promise");
      resolve();
    });
    p.then(() => {
      console.log("内部第一个then");
    });
    p.then(() => {
      console.log("内部第二个then");
    });
  })
  .then(() => {
    console.log("外部第二个then");
  });
//外部promise
//外部第一个then
//内部promise
//内部第一个then
//内部第二个then
//外部第二个then
```

```javascript
let p = new Promise((resolve, reject) => {
  console.log("外部promise");
  resolve();
});
p.then(() => {
  console.log("外部第一个then");
  new Promise((resolve, reject) => {
    console.log("内部promise");
    resolve();
  })
    .then(() => {
      console.log("内部第一个then");
    })
    .then(() => {
      console.log("内部第二个then");
    });
});
p.then(() => {
  console.log("外部第二个then");
});
//外部promise
//外部第一个then
//内部promise
//外部第二个then
//内部第一个then
//内部第二个then
```

```javascript
new Promise((resolve, reject) => {
  console.log("外部promise");
  resolve();
})
  .then(() => {
    console.log("外部第一个then");
    new Promise((resolve, reject) => {
      console.log("内部promise");
      resolve();
    })
      .then(() => {
        console.log("内部第一个then");
      })
      .then(() => {
        console.log("内部第二个then");
      });
    return new Promise((resolve, reject) => {
      console.log("内部promise2");
      resolve();
    })
      .then(() => {
        console.log("内部第一个then2");
      })
      .then(() => {
        console.log("内部第二个then2");
      });
  })
  .then(() => {
    console.log("外部第二个then");
  });
//外部promise
//外部第一个then
//内部promise
//内部promise2
//内部第一个then
//内部第一个then2
//内部第二个then
//内部第二个then2
//外部第二个then
```

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/dwvarg/ltxtw0
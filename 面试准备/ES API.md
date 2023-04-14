# ES API
# bind/call/apply

改变 this 方向  
apply 可以接受一个数组，bind 返回的是一个函数，调用才能执行

```javascript
let person = {
  name: "tom",
  sayHi(to, ...args) {
    console.log(
      `Hi,${to}, my name is ${this.name}。${args && args.toString()}`
    );
  },
};
person.sayHi();
// Hi,undefined, my name is tom。
let person2 = {
  name: "Bob",
};
person.sayHi.call(person2, "Mary");
person.sayHi.apply(person2, ["Mary"]);
// Hi,Mary, my name is Bob。
person.sayHi.bind(person2, "Mary", "Good morning!")();
// Hi,Mary, my name is Bob。Good morning!
```

```javascript
Function.prototype._call(obj,...args){
	obj.fn = this
  const  r = obj.fn(...args)
  delete obj.fn
  return r
}

Function.prototype._bind(obj,...args){
  obj.fn = this
  return function(...args){
    const r = obj.fn(...args)
    delete obj.fn
    return  r
  }
}

Function.prototype._apply(obj,...args){
	obj.fn = this
  const r = obj.fn(...arr)
  delete obj.p
  return r
}
```

# softbind

软绑定

```javascript
Function.prototype._softbind(obj,...args){
	return (...res) => this.call(obj,...args,...res)
}
```

# Array.isArray

```javascript
const isArray = Array.isArray || list => ({}).toString.call(list) === '[object Array]'
```

# sleep/delay

sleep 让函数短暂睡觉几秒

```javascript
function sleep(ms) {
  return new Promise((resolve) => setTimeout(resolve, ms));
}

sleep(1000).then(() => {
  console.log("已经睡眠了 1 秒");
});
```

实现以恶搞 delay 函数。在 N 毫秒之后执行函数，并以函数结果作为返回值

```javascript
function delay(func, seconds, ...args) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      Promise.resolve(func(...args))
        .then(resolve)
        .catch(reject);
    }, seconds);
  });
}

// 在 3s 之后返回 hello, world
await delay((str) => str, 3000, "hello, world");

// 在 3s 之后返回 hello, world，第一个函数可返回 promise
await delay((str) => Promise.resolve(str), 3000, "hello, world");
```

# Promise.all

当所有给定的 promise 都 resolve 时，新的 promise 才会 resolve，并且其结果数组将成为新 promise 的结果。  
如果任意一个 promise 被 reject,那么 Promise 就会立即 reject

```javascript
Promsise.all = function(promiseArr){
    return new Promsise((resolve,reject)=>{
        let resArr = []
        let time = 0
        promiseArr.forEach((item,index) => {
          if(! item instanceof Promsie){
            resArr[index] = time
            time++
          }
          item.then(value=>{
            resArr[index] = value
            time++
            if(time === promiseArr.length){
              resolve(resArr)
            }
          },reason=>{
             reject(reason)
          }
        })
    })
}
```

# Promise.allSettled

```javascript
function allSettled(promiseArr){
  return new Prioise((resolve,reject)=> {
  	let res = []
    let time = 0
    promiseArr.forEach((item,index)=> {
      item.then(res=>{
        res[index]={
          status: 'fulfilled'
          value: res
        }
        time++
      },reason=> {
        res[index]={
          status: 'rejected',
          reason: reason
        }
        time++
      }.finally(()=>{
        if(time === promiseArr.length){
        	return resolve(arr)
        }
      })
    })
  })
}
```

# Array.prototype.flat

```javascript
function flat(arr,depth = 1) {
	if(depth === 0) return arr
  return arr.reduce((a,b)=> {
    a.concat(Array.isArray(b) ? flat(b,depth-1):b),[]
  }）
}
```

# Array.prototype.reduce

对元素进行迭代  
用于将数组减少为一个单一的值。它的第一个参数是一个回调函数，第二个参数是可选的初始值。

```javascript
const reduce = (arr,callback,init) => {
	let next = init !=== undefiend?init:arr[0]
  for(let i = init !=== undefiend? 0 : 1;i<arr.length;i++)  {
    	next = callback(next,arr[i],i)
  }
  return next
}
```

# String.prototype.trim

```javascript
str.replace(/^\s+|\s+$/g, "");
```

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/dwvarg/tobqgcyuvlvz1c39
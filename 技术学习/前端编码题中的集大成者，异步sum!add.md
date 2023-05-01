# 前端编码题中的集大成者，异步sum/add
废话不多，我们直接上题目  
请实现一个 sum 函数，接收一个数组 arr 进行累加，并且只能使用 add 异步方法  
add 函数已实现，模拟异步请求后端返回一个相加后的值

```javascript
/*
  请实现一个 sum 函数，接收一个数组 arr 进行累加，并且只能使用add异步方法
  
  add 函数已实现，模拟异步请求后端返回一个相加后的值
*/
function add(a, b) {
  return Promise.resolve(a + b);
}

function sum(arr) {}
```

## 这道题在问什么？

add 函数是返回一个 promise, 异步输出 a+b 的值，  
我们使用 add 模拟接口请求，a,b 是给接口的参数，a+b 是接口返回的值。  
sum 函数输入一个数组例如[1,2,3,4,5]，进行累加，使用 add 异步方法。  
转换成我们业务中的场景就是，

- 调用接口执行 1+2, 接口返回 3，
- 调用接口再执行 3+3, 接口返回 6
- 调用接口再执行 6+4，接口返回 10
- 调用接口再执行 10+5，接口返回 15

## 简单实现

不用多考虑，遍历执行累加就好

- 借助数组方法 reduce 方法实现累加

```javascript
function add(a, b) {
  return Promise.resolve(a + b);
}

function sum(arr) {
  if (arr.length === 1) return arr[0];
  return arr.reduce((x, y) => Promise.resolve(x).then((x) => add(x, y)));
}
```

- 借助 async/await 实现，原理一样，代码更加清晰

```javascript
function add(a, b) {
  return Promise.resolve(a + b);
}

async function sum(arr) {
  let s = arr[0];
  for (let i = 1; i < arr.length; i++) {
    s = await add(s, arr[i]);
  }
  return s;
}
```

### 问题

如果 add 函数调用接口，假设接口执行完毕返回需要 1s，那么一个长度为 n 的数组，需要执行 n-1 次累加，需要的时间为 n-1 s  
**面试官继续问了，有没有什么优化空间呢？**

## 优化实现

既然是异步操作，还是累加操作，也就是说，只要输入的数组是确定的，返回的累加值也就是确定的。  
之前从前往后累加，一个一个加。  
我现在可以借助 Promise.all() 改成并行请求，数组两两一组，进行累加，然后再把和累加。  
比如输入[1,2,3,4,5]  
第一次请求[1,2] [3,4]，拿到接口返回 3，7  
第二次请求[3,7]，拿到接口返回 10  
第三次请求[10,5], 拿到接口返回 15

```javascript
function add(a, b) {
  return Promise.resolve(a + b);
}
// chunk 函数把输入数组两两分组
// 输入[1,2,3,4,5]
// 返回[[1,2],[3,4],5]
function chunk(arr) {
  const res = [];
  for (let i = 0; i < arr.length; i++) {
    const index = Math.floor(i / 2);
    res[index] ??= [];
    res[index].push(arr[i]);
  }
  return res;
}

async function sum(arr) {
  if (arr.length === 1) return arr[0];
  const promises = chunk(arr).map(([x, y]) =>
    // 注意此时单数的情况
    y === undefined ? x : add(x, y)
  );
  return Promise.all(promises).then((list) => sum(list));
}
```

有点类似归并思想，把数组分成两两一组，最后一项如果是单数，直接输出就好，直到数组长度为 1.  
时间复杂度也降低了 logN

### 问题

这种是代码 code 的思路，但是实践生产能写这样的代码？  
promise.all 中可以写 100 个，1000 个元素，一起发起请求，但是浏览器起能同时发起 100 个 1000 个请求吗？  
于是面试官继续问了， **比如有 1000 个数据，那第一次就会发送 500 个请求，网络拥堵了，我想控制成只能同时发送 10 个请求怎么办？**

## 再次优化

要控制**成只能同时发送 10 个请求**，就要对 promise.all 进行修改。

```javascript
function add(a, b) {
  return Promise.resolve(a + b);
}

function chunk(arr) {
  const res = [];
  for (let i = 0; i < arr.length; i++) {
    const index = Math.floor(i / 2);
    res[index] ??= [];
    res[index].push(arr[i]);
  }
  return res;
}

async function pMap(list, mapper, concurrency = Infinity) {
  const results = [];

  const batches = chunk(list, Math.min(concurrency, list.length));

  await Promise.all(
    batches.map(async (batch) => {
      const batchResults = await Promise.all(batch.map(mapper));
      results.push(...batchResults);
    })
  );

  return results;
}

async function sum(arr, concurrency) {
  if (arr.length === 1) return arr[0];

  const mapped = await pMap(
    chunk(arr, 2),
    ([x, y]) => (y === undefined ? x : add(x, y)),
    concurrency
  );

  return sum(mapped, concurrency);
}
```

pMap 函数中，先调用 chunk 函数将原数组按照指定大小切割成多个子数组（每个子数组大小不超过并发数），然后使用 Promise.all 将每个子数组中的数据并发地传递给 mapper 函数进行处理，最后将每个子数组的处理结果拼接成一个新数组返回。

sum 函数则使用了 pMap 函数，将原数组切割成两两一组的子数组，对每个子数组调用 add 函数求和，最终递归地将求和后的结果作为新数组再次传入 pMap 函数中继续处理。  
这样实现可以提高代码的并发性能，加速数据处理的速度。

以下是一个使用例子，测试用例将一个长度为 10 的数组按照每个元素加 1 的方式进行异步处理，并发数为 3：

```javascript
const arr = [1, 2, 3, 4, 5];

sum(arr, 2)
  .then((result) => console.log(result))
  .catch((error) => console.error(error));
```

## 总结

上述一个代码题考查了哪些部分

- promise 串行,并行
- 二分
- 并发控制

考察技术深度广度都有了，但是大部分人题目都看不懂，其实我也是，我连答案也没明白。

参考链接[https://github.com/shfshanyue/Daily-Question/issues/662](https://github.com/shfshanyue/Daily-Question/issues/662)

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/xqahba7rwsi1v2xc
# lodash API
# 防抖（debounce）/节流（throttle）

防抖，多次点击，1s 后执行，多次点击，重新计算时间 =>等电梯  
场景：按钮多次点击发送请求，浏览器调整窗口，文本编辑实时保存，无更改操作后 1s 保存

```javascript
function debounce(fn, wait) {
  let timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => {
      fn(...args);
    }, wait);
  };
}
```

节流，控制事件发生频率，1s 内只执行一次 =>红绿灯  
场景：浏览器播放时间，每 1sj 计算一次进度，scroll 事件，每隔一秒计算一次位置信息

```javascript
function throttle(fn, wait) {
  let timer;
  return (...args) => {
    if (timer) return;
    timer = setTimeout(() => {
      fn(...args);
      timer = null;
    }, wait);
  };
}
```

# 深拷贝

deepClone

```javascript
const obj = {
  re: /hello/,
  f() {},
  date: new Date(),
  map: new Map(),
  list: [1, 2, 3],
  a: 3,
  b: 4,
};
function deepClone(target, map = new Map()) {
  if (target === null) return null;
  if (Object.prototype.toString.call(target) === "[object RegExp]") {
    return new RegExp(target);
  }
  if (Object.prototype.toString.call(target) === "[object Date]") {
    return new Date(target);
  }
  if (typeof target === "object") {
    let cloneTarget = Array.isArray(target) ? [] : {};
    if (map.get(target)) {
      return map.get(target);
    }
    map.set(target, cloneTarget);
    for (const key in target) {
      cloneTarget[key] = deepClone(target[key], map);
    }
    return cloneTarget;
  } else {
    return target;
  }
}
```

# 深比较 deepEqual

```javascript
function isEqual(x, y) {
  if (x === y) return true;
  if (
    typeof x === "object" &&
    x !== null &&
    typeof y === "object" &&
    y !== null
  ) {
    const keysX = Object.keys(x);
    const keysY = Object.keys(y);
    if (keysX.length !== keysY.length) return false;
    for (const key of keysX) {
      if (!isEqual(x[key], y[key])) {
        return fasle;
      }
    }
    return true;
  } else {
    return false;
  }
}
```

# loash.get

？？？

# compose 函数合成

```javascript
const add10 = (x) => x + 10;
const mul10 = (x) => x * 10;
const add100 = (x) => x + 100;

const compose = (...fns) =>
  // 注意 f、g 的位置，如果实现从左到右计算，则置换顺序
  fns.reduce(
    (f, g) =>
      (...args) =>
        f(g(...args))
  );

// (10 + 100) * 10 + 10 = 1110
compose(add10, mul10, add100)(10);
// 从右往左依次执行
```

# shuffle 随机函数

打乱数组，对底数组无影响

```javascript
function shuffle(list) {
  list.sort((x, y) => Math.random() - 0.5);
}
shuffle([1, 2, 3, 4]);
//第 N 项数字与前 N 项数字随机选一相互交换
//第 N-1 项数字与前 N-1 项数字随机选一相互交换
//第 2 项数字与前 2 项数字随机选一相互交换
function shuffle2(list) {
  const l = list.length;
  let res = [...list];
  for (let i = l - 1; i > 0; i--) {
    const swapIndex = Math.floor(Math.random() * (i + 1));
    [res[i], res[swapIndex]] = [res[swapIndex], res[i]];
  }
  return res;
}
shuffle2([1, 2, 3, 4]);
```

# samepleSize 从数组中随机抽几个数字

```javascript
function samepleSize(list, n) {
  return shuffle(list).slice(0, n);
}
```

# sameple 从数组中随机抽一个数字

```javascript
function sameple(list) {
  return list[Math.floor(Math.random() * list.length)];
}
```

# maxBy 根据给定条件找到最大的数组项

若有多项返回多项

```javascript
const data = [{ value: 6 },{ value: 6 },{ value: 2 }, { value: 4 }]
function maxBy(list,by) {
	let max = list[0]
  let res = [max]
  for(let i = 1;i<list.length;i++){
    if(by(max) === by(list[i])) {
      res.push(list[i)
    }else if(by(max) <by(list[i])) {
      max = list[i]
      res = [max]
    }
  }
  if (res.length === 1) {
    return res[0];
  }
  return res;
}

maxBy(data, x=>x.value)
```

# keyBy 根据某项 key 排序数组

```javascript
const data = [
  { id: 1, name: "山月" },
  { id: 2, name: "shanyue" },
];
function keyBy(list, by) {
  return list.reduce((obj, item) => {
    obj[by(item)] = item;
    return obj;
  }, {});
}

keyBy(data, (x) => x.id);
```

# groupBy

# once 记忆返回结果只执行一次

```javascript
const f = (x) => x;

const onceF = once(f);
function once(fun) {
  let call = false;
  let res;
  return function () {
    if (!call) {
      call = true;
      res = fun.apply(this, arguments);
    }
    return res;
  };
}
//=> 3
onceF(3);

//=> 3
onceF(4);
```

# chunk 函数 根据传入的点对数组进行分组

```javascript
function chunk(list, size) {
  const res = [];
  for (let i = 0; i < list.length; i++) {
    const index = Math.floor(i / size);
    console.log(index, res[index]);
    if (!res[index]) {
      res[index] = [];
      res[index].push(list[i]);
    } else {
      res[index].push(list[i]);
    }
    // res[index] ??= []
    // res[index].push(list[i])
  }
  return res;
}
```

# 驼峰转下划线，下划线转驼峰

```javascript
console.log(toCamelCase("get_element_by_id"));
console.log(toKebabCase("GetElementById"));
function toCamelCase(str) {
  // 转驼峰
  return str.replace(/\_(\w)/g, function (all, letter) {
    return letter.toUpperCase();
  });
}
function toKebabCase(str) {
  // 转下划线
  let res = str.replace(/([A-Z])/g, "_$1").toLowerCase();
  if (res.slice(0, 1) === "_") {
    res = res.slice(1); //去除开头的-
  }
  return res;
}
```

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/dwvarg/hg03zmlfo6l6bqqa
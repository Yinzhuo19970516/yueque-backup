# Vue.js设计与实现 读书笔记
# 第一篇 框架设计概览

## 第一章 权衡的艺术

### 命令式 和 声明式

视图层的框架一般分为 命令式和声明式。  
**命令式框架**：如 Jquery 和原生 js，更关注更改视图，修改 dom 的过程。  
**声明式框架**：如 vue,更关注结果，操作 dom,修改 dom 的过程我们不用关心，vue 去帮我们我们完成，我们只需要告诉框架我们需要什么样的东西

#### 以一个操作 dom 的场景为例子

> 获取一个 dom  
> 设置文本内容 hello world  
> 为其绑定点击事件  
> 当点击时弹出 ok

```javascript
// jquery 实现
$('#app') // 获取div
    .text("hello world") // 设置文本内容
    .on("click",()=>{ alert('ok') }) // 增加监听事件

// 原生js实现
const div = document.querySelector('#app') // 获取div
div.innnerText = 'hello world' // 设置文本内容
div.addEventListner('clilk',() => { alert('ok') } })   // 增加监听事件

// vue 实现
<div @click="()=> alert('ok')">hello word</div>
```

命令式框架，更加符合人脑处理事情的思路，面向过程，一步一步去做。  
声明式框架，更加傻瓜式一些，只需要告诉框架，我需要的结果，但是如果告诉框架，有框架自定义的一套规范，有学习成本和使用成本。

### 性能与可维护的权衡

命令式框架的性能 优于 声明式的框架的性能  
简单来说，就是 jquery 性能优于 vue  
当我们需要更新 dom 时

- 对于 vue 框架来说，修改 dom 需要先找到真实 dom 和虚拟 dom 的差异，更新 dom 有差异的地方。
- 对于 jquery 框架来说，修改 dom 直接去修改就可以了。

**vue 等声明式框架的更新性能消耗 = 直接修改的性能消耗 + 找出差异的性能消耗**  
**jquery 等命令式框架的更新性能消耗 = 直接修改的性能消耗**  
所以说，**jquery 性能优于 vue**  
当然了 vue 的可维护性更强，所以我们需要做的是，努力减小找出差异的性能消耗，这样就可以使 vue 的性能无限接近 jquery,因此我们提出了**虚拟 dom,来最小化找出差异**

### 虚拟 DOM 的性能

虚拟 dom 简单来讲它就是将 HTML 的 DOM，用一套 JS 对象来表示。  
为了更好的说明虚拟 dom 的性能，我们用 innerHTML 来比较。  
innerHTML 是 html5 提出的一个新的获取操作 dom 的方法。

#### innerHTML

- 可以获取调用元素的所有子节点对应的 html 片段
- 可以是根据指定的值创建 DOM 树，替换原有的元素节点

一般我们插入大量新 HTML 标记时，使用 innerHTML 与 通过多次 DOM 操作先创建节点再指定它们之间的关系相比，效率更高，因为在设置 innerHTML 时就会先创建一个 HTML 解析器，这个解析是在浏览器级别的基础上代码（c++）运行的，因此比执行 js 快得多，当然也会带来 创建销毁 html 解析器也有性能消耗。所以控制使用次数。

#### 当新建页面时

```javascript
const html = `
  <div><span>...</span></div>
`;
div.innerHTML = html;
```

通过 innerHtml 创建页面的性能 = HTML 字符串拼接的计算量 + innerHTML 的 DOM 计算量  
通过虚拟 dom 创建页面的性能 = 创建虚拟 dom 的计算量 + 创建真实 DOM 的计算量

|                 | innerHTML         | 虚拟 DOM          |
| --------------- | ----------------- | ----------------- |
| JavaScript 运算 | 拼接 html 字符串  | 创建虚拟 dom      |
| DOM 构建        | 新建所有 dom 元素 | 新建所有 dom 元素 |

新建页面时，JavaScript 运算和 DOM 构建层面相差不大，innerHTML 性能甚至优于 虚拟 dom

#### 当更新页面时

|                 | innerHTML        | 虚拟 DOM                 |
| --------------- | ---------------- | ------------------------ |
| JavaScript 运算 | 拼接 html 字符串 | 创建虚拟 dom + diff 算法 |

| DOM 构建

性能因素 | 销毁旧 dom  
新建新 dom

与模版大小相关 | 必要的 dom 更新

与数据变化量相关 |

#### 修改 dom 的三个方法对比

| innerHTML | 虚拟 dom | 原生 js（createElement） |
| --------- | -------- | ------------------------ |

| 心智负担重等  
性能差 | 心智负担小  
可维护性高  
性能不错 | 心智负担大  
可维护性差  
性能高 |

### 运行时和编译时

设计一个框架时，一般有三种选择  
**纯运行时 **  
**我个人理解，纯运行时，就比如 jquery，或者和命令式框架写法相似**  
**纯编译时 **  
**比如 Svelte, 0 运行时，直接编译成可执行的 js 代码，因此性能更好。**  
**运行时+编译时**  
**vue react 都是运行时+编译时。<template><jsx> 代码都会在编译时被打包成被渲染成 树形结构。在运行时，执行 render 进行渲染。**

## 第二章 框架设计的核心要素

### 提升用户的开发体验

vue 源码中有大量的 warn()函数，提示开发者，错误警告信息

### 控制框架代码体积

warn() 函数在存在开发环境，生成环境不会打包这些代码

### 框架要做好良好的 Tree-Shaking

简单来说，tree-shaking 就是消除那些永远不会被执行的代码  
消除无用依赖之前，需要对 js 代码做静态分析，所以我们在源码中，很多 js 文件，，加上注释  
`/*#_PURE*/`  
表明此文件不会产生副作用，可以移除它

### 框架应该输出怎样的构建产物

比如 Vue 既能通过 script 标签引用，也能通过 npm 安装，还能在 node 中通过 require 方式引入  
所以它的代码一定遵守 匿名函数自执行，引入就能自执行，也满足 ESM 和 CommobJS 的规范。

### 错误处理

全局封装错误处理函数  
全局提供了 registerErrrorHandler 函数，用户可以使用它注册错误处理程序，然后 callWithErrorHanding 捕获错误之后，把错误传递给用户注册的错误处理程序。

用户既可以选择忽略错误，也可以调用上报程序将错误上报给监控系统

```javascript
//util.js
let handleError = null;
export default {
  foo(fn) {
    callWithErrorHanding(fn);
  },
  //用户可以调用这个函数注册统一的错误处理函数
  registerErrrorHandler(fn) {
    handleError = fn;
  },
};

function callWithErrorHanding(fn) {
  try {
    fn && fn();
  } catch (e) {
    // 将捕获到的错误传递给用户的错误处理程序
    handleError(e);
  }
}

// 用户调用
import utils from "utils.js";

utils.registerErrrorHandler((e) => {
  console.log(e);
});
utils.foo(() => {});
```

## 第三章 Vue.js 的设计思路

### 声明式地描述 UI

Vue 是一个声明式的 UI 框架。前端页面包括，DOM 元素，属性，事件，元素的层级结构。

- Vue.js 使用与 HTML 标签一样的方式来描述 DOM，
- 使用与 HTML 标签一样的方式来描述属性，
- 使用：或 v-bind 来描述动态绑定的属性，
- 使用@或者 v-on 来描述事件，
- 使用与 HTML 标签一致的方式来描述层级结构

也可以用 JavaScript 对象描述 UI,也就是虚拟 dom.

### 初识渲染器

渲染器的作用就是把虚拟 DOM 渲染成真实 DOM  
渲染器函数 renderer(vnode,container)

- 接受两个参数，一个虚拟 dom 对象，一个真实 dom 元素作为挂载点
- 渲染器执行总体分三步
  - 创建元素
  - 为事件添加属性和事件
  - 处理 children

renderer 函数精髓在于 dom 更新过程，后面我们会具体学习

### 组件的本质

组件就是一组 dom 的封装  
可以用 vnode 的 tag 属性来存储要渲染的 dom 节点  
如果 tag 类型是字符串 则说明是普通字符串  
如果 tag 类型是数组，则说明渲染的是组件，需要递归遍历渲染

### 模版的工作原理

声明式地描述 UI 有两种方式，一个为手写虚拟 dom，一个是使用模版，对于模板是怎么工作的，我们需要使用编译器。  
编译器的作用就是将模版编译为渲染函数  
以.vue 文件为例，一个.vue 文件就是一个组件,<template>标签内部的就是模版

### vue.js 是各个模块额度组合的有机整体

编译器 把模版编译成 虚拟 dom，虚拟 dom 在通过渲染函数，更新到真实 dom  
在编译成过程中会表示静态属性和动态属性，生成代码时附带这些信息。  
对渲染器来说省去了寻找变更点的工作量。

# 第二篇 响应系统

## 第四章 响应系统的作用与实现

### 什么是响应式数据

假设在一个副作用函数中读取了某个对象的属性，当对象的属性变化时，我们希望副作用函数重新执行，那么这个对象就是 响应式数据

### 响应式数据的基本实现

执行副作用函数 effect 触发 obj.text 的读取操作  
修改 obj.text 的值时，会触发 obj.text 的修改操作

```javascript
const obj = { text: "hello word" };
function effect() {
  document.body.innerText = obj.text;
}
effect();

setTimeout(() => {
  obj.text = "hello vue3";
}, 1000);
```

我们可以通过 proxy 拦截这个对象的读取和设置操作，当读取字段 obj.text 时，我们可以把 effect 函数存储到一个“桶”里,当设置 obj.text 时，再把副作用函数 effect 从“桶”里取出来并执行即可。

### 定义 activeEffect 来存储全局统一的的副作用函数

当调用 effect 注册副作用函数时，将副作用函数复制给 activeEffect

### 桶的数据结构

这段代码存在三个角色

- 被操作（读取）的代理对象 obj
- 被操作（读取）的字段名 text
- 使用 effect 函数注册的副作用函数 effectFn

三个角色对应关系为

- target
  - key
    - effectFn1
    - effectFn2

### 使用 weakMap 来作为桶的数据结构

WeakMap 由 target -> Map 构成  
Map 由 key -> Set 构成  
weakMap 对 key 是弱引用，不影响垃圾回收器的工作。如果 target 对象没有任何引用了，会被自动回收，如果使用 Map，即使没有任何引用，target 也不会被回收  
**单独封装 收集副作用函数 track (obj,key)**  
**单独封装 触发副作用函数 trigger(obj,key)**

```html
<body></body>
<script>
  // 存储副作用函数的桶
  const bucket = new WeakMap();

  // 原始数据
  const data = { text: "hello world" };
  // 对原始数据的代理
  const obj = new Proxy(data, {
    // 拦截读取操作
    get(target, key) {
      // 将副作用函数 activeEffect 添加到存储副作用函数的桶中
      track(target, key);
      // 返回属性值
      return target[key];
    },
    // 拦截设置操作
    set(target, key, newVal) {
      // 设置属性值
      target[key] = newVal;
      // 把副作用函数从桶里取出并执行
      trigger(target, key);
    },
  });
  // 在 get 拦截函数内调用 track 函数追踪变化
  function track(target, key) {
    if (!activeEffect) return;
    let depsMap = bucket.get(target);
    if (!depsMap) {
      bucket.set(target, (depsMap = new Map()));
    }
    let deps = depsMap.get(key);
    if (!deps) {
      depsMap.set(key, (deps = new Set()));
    }
    deps.add(activeEffect);
  }

  function trigger(target, key) {
    const depsMap = bucket.get(target);
    if (!depsMap) return;
    const effects = depsMap.get(key);
    effects && effects.forEach((fn) => fn());
  }

  // 用一个全局变量存储当前激活的 effect 函数
  let activeEffect;
  function effect(fn) {
    // 当调用 effect 注册副作用函数时，将副作用函数复制给 activeEffect
    activeEffect = fn;
    // 执行副作用函数
    fn();
  }

  effect(() => {
    console.log("effect run");
    document.body.innerText = obj.text;
  });

  setTimeout(() => {
    trigger(data, "text");
  }, 1000);
</script>
```

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/pn2vy1
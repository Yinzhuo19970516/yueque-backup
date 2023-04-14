# 十问babel，用最简单的话说清楚babel
## babel 是什么？

babel 在前端项目中充当的是一个代码转译工具的角色。

## 为什么要使用 babel ?

我们都知道 JS 的新的 API，语法糖层出不穷，在丰富了我们的知识库的同时，也极大的便利了我们的开发。  
但是由于 浏览器的版本标准不一致，尤其是移动端（各个手机有自带的默认浏览器，再加上 QQ，UC, 360，百度，夸克）浏览器众多，对我们前端开发的 H5 页面兼容性未知，当你使用了某个新的 API 时，有概率会直接在某些小众或者低版本浏览器上报错。  
当然，这些浏览器对 ES3,ES5 的语法肯定能够识别，如果这也不能识别，趁早放弃这款浏览器吧。  
天下苦秦久矣，如果能有一款工具能帮我们把高版本语法转化为浏览器可以兼容的低版本语法就好了。  
所以，babel 出现了！  
当然也可以不用。只要你写的语法版本足够低，没有转译的空间了。

## babel 的作用是什么？

转译 = 转化+编译  
借助各种插件实现对 JS 语法和新的 API 的转化编译成低版本兼容性更好的代码。

## 为什么我总是看不懂项目中 babe.config.js 的配置项？

当然不是你的问题  
他的配置项确实很复杂，且网上配置项众说纷纭，花里胡哨一大堆，各个插件名字相似，很容易搞混。  
况且，这只是一个工具，配成啥样，都能用，既然能用，凑活用呗。  
再者，要写那么多页面，babel 配置文件要么早都配置好了，哪有那么多时间折腾研究配置项呢？  
但是，我有时间，我争取用最简洁的语言说清楚，babel 这是个什么玩意。  
能达到面试标准即可。

## babel.config.js 常见的配置项有哪些？

preset = plugin 的集合  
plugin 插件  
preset 预设好的一堆插件  
可以理解成，转译 JS 要用到很多插件，所以先人们整理了一批插件做成了插件集合，可以放在 preset 里直接使用。  
压力骤减  
还有一些新语法，没有包括在 preset 的集合中，就需要额外引入 plugin  
如果你不用，那用不到 plugin，如果你用了，就得引入相关 plugin

## 怎么知道用不用 plugin，用哪些 plugin 呢？

我也不知道，实践出真知  
我一般方式是，先用，报错了，再具体分析需要引入哪个插件  
[https://github.com/Yinzhuo19970516/vue-template/blob/main/babel.config.js](https://github.com/Yinzhuo19970516/vue-template/blob/main/babel.config.js)  
比如我在项目中使用了可选链操作符?. 与双问号 ??  
其实也不是特别新，但是 google 浏览器打开页面直接报错，搜索引擎告诉我需要在 babel 中配置两个插件，专门进行解析  
配置好，无任何异常报错。  
这就说明我们这两个新语法没有在预设的集合中，所以需要额外引入插件

```javascript
module.exports = {
  presets: ["@vue/cli-plugin-babel/preset"],
  plugins: [
    // ？？
    "@babel/plugin-proposal-nullish-coalescing-operator",
    // 可选链
    "@babel/plugin-proposal-optional-chaining",
    [
      "import",
      {
        libraryName: "vant",
        libraryDirectory: "es",
        style: true,
      },
      "vant",
    ],
  ],
};
```

## 常见的 presets 有哪些呢

- @babel/preset-env

preset-env 内部集成立刻大部分我们日常开发需要的插件集合。

- **@vue/cli-plugin-babel**

一般来说，如果是 vue-cli 起的项目，就不需要再额外关系 babel 的配置了。。  
这就是 vue-cli 特有的 babel 插件，其中包括 babel7,babel-loader,@vue/babel-preset-app 等等  
其中也加载了@babel/preset-env，查看./node_modules/@vue/babel-preset-app，其中配置了

```javascript
presets: [
  [
    require("@babel/preset-env"),
    {
      useBuiltIns,
      corejs: 3,
    },
  ],
];
```

当然了还有其他一些的 react 项目 presets 集合，我没见过，所以没有写，欢迎大家补充。  
由此可见，babel/preset-env 才是核心，我们需要先掌握核心科技。

### babel/preset-env 是什么？

preset-env 集成了大部分的转译插件，会根据配置的参数进行转译  
主要参数有两个

- useBuiltIns
  - 有三个值 默认 false,entry,usage
  - 配合 @babel/polyfill 使用，我们先按下不表
- targets
  - 用来指定需要支持的浏览器范围

### babel/preset-env 的作用是什么？

例如下面一段代码  
转换前代码

```javascript
let array = [1, 2, 3, 4, 5, 6];
array.includes((item) => item > 2);
new Promise();
```

转译后代码

```javascript
var array = [1, 2, 3, 4, 5, 6];
array.includes(function (item) {
  return item > 2;
});
new Promise();
```

Babel 默认只是转换了 箭头函数 let ,Promise 和 includes 都没有转换 ,这是为什么?  
Babel 把 Javascript 语法 分为 syntax 和 api  
api 指那些我们可以通过 函数重新覆盖的语法 ，类似 includes,map,includes,Promise。  
syntax 像箭头函数，let const class  
babel/preset-env 仅仅会转译新的语法，不会处理 ES 新的 API 和 ES 实例方法。  
比如

1. 全局对象：Promise、WeakMap 等。
2. 全局静态函数：Array.from、Object.assign 等。
3. 实例方法：比如 Array.prototype.includes 等。

那新的 API 如何处理呢？  
这就是我们下面要提到的 polyfill

### @babel/polyfill 是什么？

先翻译翻译 polyfill 顾名思义是一种填充材料  
业内都叫做 垫片，就是名字，不用刻意去记忆，理解成补充材料就好。  
![image.png](https://cdn.nlark.com/yuque/0/2023/png/1572912/1678343944069-274eb9eb-6c16-4ac3-9b28-e563c6143ed8.png#averageHue=%23f8f7f7&clientId=u47a5954f-1646-4&from=paste&height=166&id=ub5de3ee8&name=image.png&originHeight=196&originWidth=450&originalType=binary&ratio=2&rotation=0&showTitle=false&size=18383&status=done&style=none&taskId=u0de335ae-9204-443a-bead-5e80bf30b52&title=&width=380)  
在 babel 中就是用来处理上面提到无法处理的 api。

## 如何配置@babel/polyfill 呢？

使用 preset-env 的 useBuiltIns 参数

- false 不使用 babel/polyfill
- entry 在入口处手动引入 polyfill
  - babel@7.4以前

```javascript
import '@babel/polyfill''
```

- babel@7.4 以后

```javascript
import "core-js/stable";
import "regenerator-runtime/runtime";
```

- 两个等效
- Babel 就会根据我们配置需要支持的浏览器列表，将目标浏览器中不支持的 polyfill 进行全量引入并且转译
- usage
  - 仅仅会为我们引入目标浏览器中不支持并且我们在代码中使用到的内容，会剔除没有使用到的 polyfill 内容。

### 两种配置方案优缺点对比

|      | 全部引入 entry | 按需引入 usage     |
| ---- | -------------- | ------------------ |
| 优点 | 完全兼容       | 轻量               |
| 缺点 | 代码体积大     | 无法处理三方依赖包 |

entry 会引入全部的 polyfill，导致代码体积大  
usage 无法处理三方依赖包中的新的 API

### babel/polyfill 的缺点

![image.png](https://cdn.nlark.com/yuque/0/2023/png/1572912/1678369072611-06a84342-d6f1-4ec0-8443-46da3ddc6e6e.png#averageHue=%2330302c&clientId=u630ae136-84e6-4&from=paste&height=205&id=uda8c7a9d&name=image.png&originHeight=410&originWidth=1661&originalType=binary&ratio=2&rotation=0&showTitle=false&size=127575&status=done&style=none&taskId=u8adaf33a-dac8-4885-bd80-0c3ff738158&title=&width=830.5)  
通过在线转译平台，转译这 ES6 类的声明语句，我们发现在当前文件多了 6 个 function  
[https://www.babeljs.cn/rep](https://www.babeljs.cn/rep)l  
转译后，相同的方法会被重复定义  
但是如果其他文件也使用了类声明语句，也会在当前文件生成类似的 6 个辅助函数 function  
如果我们能把相同的方法抽离出来，改成运行时引入，这样可以大大缩小代码体积  
所以@babel/runtime 就时来解决这个重复定义的问题，会提前好在一个地方定义好这些通用方法，其他地方用大直接引用即可，这样可以缩小编译后的代码体积

## babel/runtime 和 babel/plugin-transform-runtime 是什么关系？

babel 的 polyfill 机制是  
对于例如 Array.from 等静态方法，直接在 global.Array 上添加；对于例如 includes 等实例方法，直接在 global.Array.prototype 上添加。  
这样直接修改了全局变量的原型，有可能会带来意想不到的问题。这个问题在开发第三方库的时候尤其重要，因为我们开发的第三方库修改了全局变量，有可能和另一个也修改了全局变量的第三方库发生冲突，或者和使用我们的第三方库的使用者发生冲突。  
公认的较好的编程范式中，也不鼓励直接修改全局变量、全局变量原型。

于此同时 ，babel-runtime 无法做到智能化分析，需要我们手动引入。  
为了解决这个问题，babel/plugin-transform-runtime 出现了  
babel/plugin-transform-runtime 内部依赖了 babel/runtime

babel/plugin-transform-runtime 在 babel/runtime 的基础上解决了全局变量污染的问题，从一个统一的模块中引入，避免了对全局变量及其原型的污染。  
@babel/plugin-transform-runtime 插件会智能化的分析我们的项目中所使用到需要转译的 js 代码，从而实现模块化从 babel-runtime 中引入所需的 polyfill 实现。

## 最佳实践是什么？我到底该如何在项目中使用？

从来没有最佳，只有最适合的。  
stackoverflow 有一条很好的回答我来翻译翻译  
what-is-best-practice-for-babel-preset-env-usebuiltins-babel-runtime  
[https://stackoverflow.com/questions/63231564/what-is-best-practice-for-babel-preset-env-usebuiltins-babel-runtime](https://stackoverflow.com/questions/63231564/what-is-best-practice-for-babel-preset-env-usebuiltins-babel-runtime)

### 如果你是开发应用项目

建议在入口处引入 polyfill, 并将 useBuiltIns 设置为 entry  
配合@babel/transform-runtime 作为依赖性  
这样我们自己开发，可以接受全局环境被污染，也不用担心 node_modules 中有语法没有被转译，可以仅可能适应浏览器的环境  
babel/plugin-transform-runtime  
这个 helpers 参数开启后可以将上边提到编译阶段重复的工具函数提到公共部分  
想当于只使用 babel/transform-runtime

```javascript
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "useBuiltIns": "entry",
        "debug": true
      }
    ]
  ],
  "plugins": [
    [
      "@babel/plugin-transform-runtime",
      {
        "corejs": 3
        // 默认为true 表示将这些工具函数抽离成为工具包引入而不必在每个模块中单独定义
        "helpers": true,
        // 切换生成器函数是否污染全局
        // 为true时打包体积会稍微有些大 但生成器函数并不会污染全局作用域
        // 为false时体积小
        "regenerator": false,
      }
    ]
  ]
}

```

### 如果是开发类库

类库项目的构建如果需要注入 polyfill 的话，最好使用 @babel/transform-runtime，因为它提供了一种不污染全局作用域的方式。

```javascript
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "useBuiltIns": "usage",
        "debug": true
      }
    ]
  ],
  "plugins": [
    [
      "@babel/plugin-transform-runtime",
      {
        "corejs": 3
        "helpers": true,
      }
    ]
  ]
}

```

## 最后

这篇文章大量参考了 19 组清风的 babel 系列的内容  
[https://juejin.cn/column/7031914136783028237](https://juejin.cn/column/7031914136783028237)  
看他的文章内容很多，很细，知识量太密集，研究很深入，看起来就有些费劲了。  
我做了一些精简和理解，加上一些自己的理解和其他文章的积累，写成了此篇，希望对屏幕前的你有所帮助。

## 参考链接

[https://juejin.cn/post/7051355444341637128](https://juejin.cn/post/7051355444341637128)  
[https://www.cnblogs.com/qinyuandong/p/13649871.html](https://www.cnblogs.com/qinyuandong/p/13649871.html)  
[https://zhuanlan.zhihu.com/p/58624930](https://zhuanlan.zhihu.com/p/58624930)  
[https://juejin.cn/post/7033383643976630302](https://juejin.cn/post/7033383643976630302)

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/urr48581nad4kssh
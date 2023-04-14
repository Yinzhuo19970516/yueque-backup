# vue-cli 是怎么配置babel的？
## 前言

[https://juejin.cn/post/7208510421676982329](https://juejin.cn/post/7208510421676982329)  
上文的最后，我们提到了 babel 配置的最佳实践  
没有最佳，只有最适合  
那么业内常用的脚手架是怎么配置的呢？  
我们以一次线上白屏问题的排查为切入点一步步探讨。

## 问题描述

我们的项目是以 vue-cli3 脚手架生成的 vue2.6.11 的 SPA 项目  
某华为 P10 手机打开页面忽然白屏，由于是 app 内嵌页，且是线上环境，无法抓包  
我们找到同款测试机，通过 google 抓包工具发现 JS 代码在报错  
定位到代码行，是 crypto.js/enc-base64url.js 在报错

```javascript
parse: function (base64Str, urlSafe=true) {
	   var base64StrLength = base64Str.length;
	   var map = urlSafe ? this._safe_map : this._map;
	   var reverseMap = this._reverseMap;
     .......
},
```

## 问题排查

当看到低版本手机上，三方库报错时，第一反应是 babel 是不是配置的不对。  
当打开报错代码，定位到上述代码第一行，一时间竟没有发现哪里有错，朴实无华，平平无奇的代码怎么浏览器引擎就无法识别呢？  
后来看到 urlSafe = true 这段给函数参数默认值的写法。  
想起来这是 ES6 新增的语法，详情可以看下面的教程  
[https://es6.ruanyifeng.com/#docs/function](https://es6.ruanyifeng.com/#docs/function)  
![image.png](https://cdn.nlark.com/yuque/0/2023/png/1572912/1678434028760-46e29210-584d-46d1-b288-a6a17a56a0df.png#averageHue=%23c4c4c4&clientId=ub8bcbdc1-da49-4&from=paste&height=298&id=Gg9pB&name=image.png&originHeight=595&originWidth=742&originalType=binary&ratio=2&rotation=0&showTitle=false&size=98465&status=done&style=none&taskId=u1fd8f45c-ef59-4499-aaf7-5729f65c657&title=&width=371)  
理想状况应该是这样

```javascript
// 转译前
function print(str = "hello word") {
  console.log(str);
}
print();

// babel 转译后

function print() {
  var str =
    arguments.length > 0 && arguments[0] !== undefined
      ? arguments[0]
      : "hello word";
  console.log(str);
}
print();
```

那么问题来了。  
为什么这段代码没哟被转译成 ES3，ES5 的语法呢？  
为什么我业务代码中的函数参数默认值的写法就没有出现任何问题呢？  
vue-cli 脚手架是对 babel 怎么配置的呢？  
带着这三个问题，我打开了项目中的 babel.config.js

## vue/app

项目中的 babel.config.js 配置如下  
预设的插件集合是 @vue/app  
plugins 中是对两个组件库的自动引入

```javascript
module.exports = {
  presets: ["@vue/app"],
  plugins: [
    [
      "import",
      {
        libraryName: "mand-mobile",
        libraryDirectory: "components",
      },
    ],
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

### @vue/app 是什么？

@vue/app 是 @vue/babel-preset-app 的缩写  
一个默认的 Vue CLI 项目会使用 [@vue/babel-preset-app](https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/babel-preset-app)  
通过查看 node_modules 源码，我们在 readme 中发现  
这个插件内部引用的是经典插件库 babel/preset-env  
它通过 @babel/preset-env 和 browserslist 配置来决定项目需要的 polyfill。  
![image.png](https://cdn.nlark.com/yuque/0/2023/png/1572912/1678670227734-c4aba0a1-0981-4c35-bd1b-62087f0e46fb.png#averageHue=%232b2b2b&clientId=ue3491783-b650-4&from=paste&height=195&id=u2f11fc6c&name=image.png&originHeight=389&originWidth=792&originalType=binary&ratio=1&rotation=0&showTitle=false&size=84499&status=done&style=none&taskId=u454e3655-ae6e-4ac3-948e-3ec28bcb1af&title=&width=396)

### browserslist

配置源从以下位置读取

1. package.json 文件中的 browserslist 字段
2. .browserslistrc 配置文件
3. browserslist.config.js 配置文件
4. 运行环境变量 BROWSERSLIST
5. 默认如下

```javascript
"browserslist": [
   "defaults"
]
// defaults => > 0.5%, last 2 versions, Firefox ESR, not dead
```

### useBuiltIns

这个属性的配置的默认值是 usage  
它会根据源代码中出现的语言特性自动检测需要的 polyfill，确保了最终包里 polyfill 数量的最小化  
意思是仅仅会为我们引入目标浏览器中不支持并且我们在代码中使用到的内容，会剔除没有使用到的 polyfill 内容。  
但是并不会处理 我们的 npm 依赖包中的 不被浏览器识别的 ES6+ 的语法  
这就导致了 文章开头提到的问题， 三方库函数是 crypto.js/enc-base64url.js 中 ES6 语法在报错，直接导致页面白屏幕。

## 解决方案

### 思路一

修改 useBuiltIns 为 entry  
在入口文件引入 polyfill

```javascript
// babel.config.js
module.exports = {
  presets: [
    [
      "@vue/app",
      {
        useBuiltIns: "entry",
      },
    ],
  ],
};
//main.js
import "core-js/stable";
import "regenerator-runtime/runtime";
```

优点：一劳永逸，所有的 JS 新语法都会进行转译，再也不用担心浏览器兼容问题  
缺点：无论这个语法有没有被使用，相应的转译包都被引入了，导致代码体积变大

### 思路二

transpileDependencies: true  
这是 vue-cli 暴露给开发者的一个属性，默认值为 false  
![image.png](https://cdn.nlark.com/yuque/0/2023/png/1572912/1678671587794-237d6e98-d298-4d81-a8ae-5e728d4f599b.png#averageHue=%23fefefe&clientId=ud5231485-46aa-4&from=paste&height=137&id=u5faf7fc2&name=image.png&originHeight=274&originWidth=812&originalType=binary&ratio=2&rotation=0&showTitle=false&size=62481&status=done&style=none&taskId=u00936346-0de7-49ec-9236-fced5ae673c&title=&width=406)  
大意就是，如果配置为 true,会对 node_modules 在的包进行转译，但是会因为遍历了所有的 node_modules 会导致构建速度变慢

### 思路三

transpileDependencies: ['crypto.js']  
哪个有问题配置哪个  
兼顾兼容性和构建速度  
我们依赖的很多包，其实已经是使用 babel 编译之后的包了，所以不需要全量编译，如果能提前预知哪些包需要编译，我们提前配置进去就好。  
当然，这是一种最理想的状态，随着项目的迭代，我们也很难知道哪些包需要转译，哪些包不需要转译。

### 采纳方案

经过讨论，我们最终使用了思路二，对所有包进行遍历  
transpileDependencies: true  
对使用的 JS 语法进行转译

## 修复之后依旧白屏？

配置 transpileDependencies: true 之后  
我们在测试环境构建打包后，用有问题的机器再次打开，依然白屏  
难道是配置项没生效？  
我们在浏览器打开 console 平台，发现是 vConsole 在报错

### vConsole.log

这个工具库相信前端开发们都不陌生，测试环境调试抓包利器，每想到在这个包竟然翻了车。  
我们代码中引 vconsole.log 的方式是

```javascript
import VConsole from "vconsole";

const vConsole = new VConsole();
```

但是报错的地方是 vConsole 的三方依赖包  
经过调研，我们发现 transpileDependencies: true 只会广度遍历编译三方依赖  
对于依赖的依赖则不会处理  
这就导致了上述的问题  
对于此类问题，我们可以通过正则进行匹配

```javascript
module.exports = {
  transpileDependencies: [
    /[/\\]node_modules[/\\]test[/\\]/,
    /[/\\]node_modules[/\\][@\\]test2[/\\]test3[/\\]/,
  ],
};
```

但是依赖引用依赖，无穷无尽，配置的复杂度大大增加  
现在想来还不如一开始就全量入口引入，简单粗暴，无后顾之忧

### 解决方案

本着遇到问题解决问题的思路  
我们最后采用了在 html 入口引入 vConsole.min.js 文件的方式，来规避了这个问题  
这个文件是使用 babel 转译过的，不存在兼容性问题。  
详情可参考项目  
[https://github.com/Yinzhuo19970516/vue-template/tree/main/public](https://github.com/Yinzhuo19970516/vue-template/tree/main/public)

## 方案总结

最后我们项目中的 babel 配置如下  
使用 vue-cli 中对默认配置，加 transpileDependencies: true  
当然没有最佳的，只有最合适的  
如果项目对兼容性要求较高，多人维护，我更建议你配置，useBuiltIns 为 entry  
牺牲项目体积，保证兼容性。

最后这是我们一次线上问题排查的过程中，记录的 babel 在实际项目中的具体实践，如果想看原理可以看我上一篇文章  
十问 babel，用最简单的话说清楚 babel  
[https://juejin.cn/post/7208510421676982329](https://juejin.cn/post/7208510421676982329)  
如果您都读到这里了，请给个免费的赞吧！

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/la5ogvvwm58rxbyx
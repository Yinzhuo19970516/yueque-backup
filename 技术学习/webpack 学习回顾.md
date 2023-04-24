# webpack 学习回顾
# 打包器内部原理

## 最简运行时分析

### cjs es umd

cjs 一种同步的模块格式，专为服务器端 JavaScript 环境设计，如 Node.js。它使用 module.exports 语法导出模块，使用 require()函数导入模块。  
es es6 引入的一种新的模块方式，支持异步加载，浏览器服务端都能支持。使用 export import  
umd 是一种模块格式，试图将 CJS 和 ES 格式结合起来，以便可以在多种环境（浏览器和服务器）中使用相同的模块。它主要用于需要支持多个环境的第三方库。UMD 模块在导出模块之前会检查是否存在 CommonJS 或 AMD（异步模块定义）环境来决定如何导出模块。

CJS 专为服务器端 JavaScript 环境设计，而 ES 更适合浏览器环境，但也可以在服务器端使用。UMD 旨在在浏览器和服务器环境中工作。

### commonJS 模块运行时分析

1.使用**webpack_modules**维护一个所有模块的数组  
2.根据模块 id 返回模块，对模块的 exports 赋值  
3.对模块进行了缓存对象，优先从缓存中取（避免重复计算模块）  
4.加载执行入口函数

#### **CommonJS 中使用 module.exports 与 exports 有何区别 **

module.exports 与 exports 指向同一个空对象 {} - 且叫它 M。也就是说，不管是 exports.pi = 3.14 还是 module.exports.pi = 3.14，都是在操作 M 对象  
node 模块，要操作的是 module 对象的 exports 的属性

### AST 抽象语法树

代码经过词法分析转成 token 串再经过 语法分析转成 AST 树  
AST 应用：ts->js es6->es5 less->css  
词法分析应用：代码检查，语法高亮，模版语法

#### 如何根据入口文件搜索出所有需要打包的模块

- 获取入口文件，转成 AST，寻找所有 require()函数，获取其引用路径
- 深度优先遍历，每当找到一个依赖，moduleId++，确定所有的依赖关系。
- 在寻找依赖过程中，还会根据配置的 loader 对不同类型的模块文件进行预处理。

### 文件名上的 hash 值及优化

### 手写最简 webpack

###

## Javascript 资源处理

### ESM 与 CommonJS

### ESM 模块运行时分析

对**webpack_exports**导出对象，新增一个属性**esModule 设置为 true，进行标记为 esm 模块  
缓存对象 **webpack_module_cache\_\_

### import() 运行时分析

#### code spliting 分析 代码分割

- 如何实现代码分割
  - 首先确定要加载的模块
  - 根据 chunkId,生成目前文件的 url
  - 创建 script,加载资源
  - 添加 onload,onerror 事件，如果超时或者模块加载失败，会调用 project 返回模块加载失败异常
  - 加载完毕后，执行当前模块的 promise
- runtimeChunk: true
  - 修改异步 chunk 内容时，runtimeChunk 路径发生变化
  - 主 chunk 内容不变

#### prefetch

magic comment 魔法注释  
只加载不解析  
webpack 通过新建 <link rel="prefetch"> 标签实现的。  
当我们真正引入模块时，再创建 jsonp 标签。这样可以取缓存，直接解析  
使用完毕后再删除 <script>

#### chunkhash/contenthash 区别

同一个 chunk 内对应的 assets 文件 hash 名一样  
多个文件，对应一个 chunk  
contenthash 将根据资源内容创建出唯一 hash，也就是说文件内容不变，hash 就不变。

#### chunkFileName

指未被列在 entry 中，却又需要被打包出来的 chunk 文件的名称。一般来说，这个 chunk 文件指的就是要**懒加载**的代码。

#### deterministic

新模块的引入，如果导致已有模块的顺序发生变化，那么 moduleID 就会发生变化，chunkId 也就存在不确定性。  
chunkIds/moduleIds：deterministic  
**此算法采用确定性的方式将短数字 ID(3 或 4 个字符)短 hash 值分配给 modules 和 chunks。**这样就可以确保在不同的构建之间能够进行更有效地缓存和比较。

### 分包最佳实践

vue-cli5 通过使用 cacheGroups 属性、路由懒加载、拆分第三方库以及避免全局注册组件等方式

1. 用 cacheGroups 属性：在 vue.config.js 文件中的 splitChunks 属性中，可以使用 cacheGroups 属性来定义分组的规则和优先级。例如，可以将路由组件放入一个名为 router 的分组中，将公共组件放入一个名为 common 的分组中
2. 使用路由懒加载：在使用 Vue Router 时，可以使用路由懒加载来延迟加载路由组件。可以在路由配置中使用 Webpack 的 import()语法来实现：处理成一个 bundle 文件中。
3. 拆分第三方库：在 cacheGroups 属性中，可以通过正则表达式来匹配第三方库，并将其拆分为独立的 bundle 文件：将 Vue 和 Vue Router 库拆分为一个名为 vue 的 bundle 文件。
4. 非必要情况下避免全局注册组件：

## json 资源处理

### loader 简介

webpack 只能处理 javascript 的模块，如果要处理其他类型的文件，就需要使用 loader 来转换

### json-loader

```javascript
module.exports = function (source) {
  var value = typeof source === "string" ? JSON.parse(source) : source;
  value = JSON.stringify(value);
  return `module.exports = ${value}`;
};
```

### webpack 内部的 loader 与 parser

![image.png](https://cdn.nlark.com/yuque/0/2023/png/1572912/1682237223943-5bd86884-1f66-4ab1-b40c-773ac5dfafd6.png#averageHue=%23e0f4f3&clientId=uf1422204-e608-4&from=paste&height=224&id=uc1c3baaf&name=image.png&originHeight=448&originWidth=1280&originalType=binary&ratio=2&rotation=0&showTitle=false&size=234014&status=done&style=none&taskId=u33e7fba7-c06a-4580-8d44-ff9d7a11fdf&title=&width=640)

- 在 loader 处，将各种模块转化为 webpack 支持的内部模块，如 less-loader/babel-loader 等。
- 在 webpack 内部做的是内置模块的解析与生成，并最终生成运行时代码，如上图的 JavascriptParser，JSONParser

### yaml-loader

### import assertion

JSON modules 提案，允许在 ES module 中使用常规的 import 语句 导入 JSON 数据。

```javascript
import jsonContent from "./file.json" assert { type: "json" };
```

## css 资源处理

### inject css

### extract css

### loader pitch

## html 资源处理

### html 中变量注入

可以使用 html-webpack-plugin 插件来生成 HTML 文件，并且可以在生成的 HTML 文件中注入变量。  
在模板文件（例如 src/index.html）中可以通过 ejs 语法来使用这个变量  
<%= htmlWebpackPlugin.options.title %>

### html 中 js/css 注入

- 在回调函数中拿到 chunk.js css image
- 获取 publicPath
- 获取入口文件 entryName
- 根据入口文件获取所有的资源 assets
- 根据资源名和公共资源路径 拼接文件路径
- 将变量插入 html 模版中
- 生成资源文件

### html 中 publicPath

## 图片资源处理

### png/jpeg/webp 处理

file-loader

### svg 处理

svg 直接以 DOM 的形式嵌入 HTML 文档。**它不仅体积更小，而且拥有更好的易读性，而且很容易通过编程的形式控制样式。**  
**项目中使用，一般倾向于把 svg 图片转成组件处理**

### 小图片优化

url-loader asset/inline

### svg 优化

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/lc86ag0tkpqvltea
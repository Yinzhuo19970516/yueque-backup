# BOM_DOM
# 前言

ECMAScript，描述了该语言的语法和基本对象，如类型、运算、流程控制、面向对象、异常等。 文档对象模型（DOM），描述处理网页内容的方法和接口。  
浏览器对象模型（BOM），描述与浏览器进行交互的方法和接口。

# DOM/BOM 架构

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1655376758094-2ac016f5-54f8-4f56-ba21-0cc0f69f3bd5.png#averageHue=%23a6a6a5&clientId=u5213b72f-7fb8-4&from=paste&height=239&id=u72b418f2&originHeight=477&originWidth=1304&originalType=binary&ratio=1&rotation=0&showTitle=false&size=154317&status=done&style=none&taskId=ub7b0a289-cefd-41c6-8686-c67fcb26748&title=&width=652)  
JavaScript 运行在浏览器  
**BOM 就是连接 JavaScript 代码和浏览器的桥梁，而 DOM 就是用来操作各种标签元素的。**

BOM 包括 window、history、location、document ...  
DOM 包括 Document（整个文档）、Element（标签元素）、CharacterData（字符数据）、Attr（属性），这些元素又可以继续往下划分。  
Document 又可以分为**HTMLDocument**（就是我们定义的 html 文件）和**XMLDocument**(XML 文件，安卓会用，前端用不到)  
Element 可以划分为 **HTMLElement**（表示 HTML 中的一个元素，比如 div），HTMLElement 又可以划分为**HTMLDIvElement 和 HTMLImageElement** 故名思义  
CharacterData 可以划分为**text(text 标签)和 Comment(注释)**  
Attr 可以理解我们元素的**class 属性 id 属性以及值**

## 继承关系

- 其中 DOM 元素、window 对象都继承自 EventTarget，所以它们都有 EventTarget 上的实例方法而 document 是 HTMLDocument 的实例对象
- 所以 window 上是可以绑定事件，监听事件，分发事件的
- 其中 DOM 中的所有元素节点都继承自 EventTarget 接口，所以 DOM 中任意节点可以绑定事件，监听事件，分发事件

我们可以在浏览器中打印它的原型属性  
**通过下图我们可以看出，window 继承 Window，Window 继承自 EventTarget**  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1655384234688-87ecdda4-8c19-431e-8d0a-72181ab2ed83.png#averageHue=%23f8f4f3&clientId=uc3d64fda-a338-4&from=paste&height=218&id=u9846dd2e&originHeight=436&originWidth=1304&originalType=binary&ratio=1&rotation=0&showTitle=false&size=256045&status=done&style=none&taskId=u187fb4cd-d270-4a66-b389-23b00077148&title=&width=652)  
**通过下图我们可以看出，document 继承 HtmlDocument，HtmlDocument 继承自 Document,Document 继承自 Node 节点，Node 节点继承自 EventTarget**  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1655384248960-3cbe60a3-0be1-44aa-974f-ffa0354739d5.png#averageHue=%23f8f6f5&clientId=uc3d64fda-a338-4&from=paste&height=159&id=u1ad21972&originHeight=318&originWidth=1304&originalType=binary&ratio=1&rotation=0&showTitle=false&size=170307&status=done&style=none&taskId=ufb9ea1e5-6c22-48c3-b302-9bbfe8ea9d4&title=&width=652)  
顺便说一句：EventTarget 继承自 Object,Object 继承自 null

## EventTarget 接口

EventTarget 是一个 DOM 接口，由可以接收事件、并且可以创建侦听器的对象实现。  
[Element](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FElement)，[document](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FDocument) 和 [window](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FWindow) 是最常见的 event targets  
EventTarget 有三个原型方法，在 window 和 DOM 元素上都可以使用

- addEventListener 给元素绑定事件
- removeEventListener 移除元素绑定的事件
- dispatchEvent 派发事件

# BOM

## 认识 BOM

- JavaScript 有一个非常重要的运行环境就是浏览器，而且浏览器本身又作为一个应用程序需要对其本身进行操作，所以通常浏览器会有对 应的对象模型(BOM，Browser Object Model)。

- BOM 主要包括一下的对象模型:
  - window:包括全局属性、方法，控制浏览器窗口相关的属性、方法;
  - location:浏览器连接到的对象的位置(URL);
  - history:操作浏览器的历史;
  - document:当前窗口操作文档的对象;
- window 对象在浏览器中有两个身份:
  - 身份一:全局对象。我们知道 ECMAScript 其实是有一个全局对象的，这个全局对象在 Node 中是 global;在浏览器中就是 window 对象;
  - 身份二:浏览器窗口对象。作为浏览器窗口时，提供了对浏览器操作的相关的 API;

## Window 全局对象

在浏览器中，window 对象就是之前经常提到的全局对象

- 比如在全局通过 var 声明的变量，会被添加到全局环境变量中，也就是会被添加到 window 上;
- 比如 window 默认给我们提供了全局的函数和类:setTimeout、Math、Date、Object 等;

```javascript
var message = "hello";
function foo() {
  console.log("foo");
}
console.log(window.message);
window.foo();

window.setTimeout(() => {
  console.log("setTimout");
});

const obj = new window.Object();
console.log(obj);
```

## Window 窗口对象

window 是一个复杂的大对象，包含了大量的对象和方法

- 第一:包含大量的属性，localStorage、console、location、history、screenX、scrollX 等等(大概 60+个属性);
- 第二:包含大量的方法，alert、close、scrollTo、open 等等(大概 40+个方法);
- 第三:包含大量的事件，focus、blur、load、hashchange 等等(大概 30+个事件);
- 第四:包含从 EventTarget 继承过来的方法，addEventListener、removeEventListener、dispatchEvent 方法;

参考地址：MDN 文档:[developer.mozilla.org/zh-CN/docs/…](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FWindow)  
![](https://cdn.nlark.com/yuque/0/2022/webp/1572912/1655383297862-33448703-bb2a-4522-8cf4-c96e9cc984fc.webp#averageHue=%23f4f4f4&clientId=uc3d64fda-a338-4&from=paste&id=uba4d3255&originHeight=467&originWidth=1304&originalType=url&ratio=1&rotation=0&showTitle=false&status=done&style=none&taskId=u869267d9-aef1-450c-993f-e5a9fc0c479&title=)

### 常见的属性

```javascript
// screenX和screenY属性返回窗口相对于屏幕的X和Y坐标。
console.log(window.screenX);
console.log(window.screenY);

// 监听滚动
window.addEventListener("scroll", () => {
  console.log(window.scrollX, window.scrollY);
});
// 获取窗口的宽度与高度：
console.log(window.outerHeight);
console.log(window.innerHeight);
```

### 常见的方法

```javascript
const scrollBtn = document.querySelector("#scroll");
scrollBtn.onclick = function () {
  //1.scrollTo
  window.scrollTo({ top: 2000 });

  //2.close
  window.close();

  // 3.open
  window.open("http://www.baidu.com", "_self");
};
```

### 常见的事件

```javascript
// 整个页面以及所有资源加载完成
window.onload = function () {
  console.log("window窗口加载完毕~");
};

window.onfocus = function () {
  console.log("window窗口获取焦点~");
};

window.onblur = function () {
  console.log("window窗口失去焦点~");
};

//hash 改变
const hashChangeBtn = document.querySelector("#hashchange");
hashChangeBtn.onclick = function () {
  location.hash = "aaaa";
};
window.onhashchange = function () {
  console.log("hash发生了改变");
};
```

## Navigator 对象

Navigator 对象包含有关浏览器的信息。到底提供哪些信息很大程度取决于用户的浏览器，但是也有一些公共的属性，如 userAgent 存在所有的浏览器中

### Navigator 对象常见的属性

```javascript
window.navigator.appCodeName;
//返回浏览器的代码名   'Mozilla'
window.navigator.appName;
//返回浏览器的名称 'Netscape'
window.navigator.appVersion;
//返回浏览器的平台和版本信息
//'5.0 (iPhone; CPU iPhone OS 13_2_3 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/13.0.3 Mobile/15E148 Safari/604.1'
window.navigator.cookieEnabled;
//返回指明浏览器中是否启用 cookie 的布尔值 true
window.navigator.platform;
//返回运行浏览器的操作系统平台 'MacIntel'
window.navigator.userAgent;
//返回由客户机发送服务器的user-agent 头部的值
//'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/101.0.4951.64 Safari/537.36'
```

### 使用场景

#### 检测插件

可以检测浏览器内是否安装了特定的插件  
navigator.plugins

#### 判断系统类型，版本信息

## location 对象

location 对象 是最有用的 BOM 对象之一，它既是 window 对象的属性，也是 document 对象的属性，window.location 和 document.location 引用的是同一个对象。

### 常见属性

- href: 当前 window 对应的超链接 URL, 整个 URL;
- protocol: 当前的协议;
- host: 主机地址;
- hostname: 主机地址(不带端口);
- port: 端口;
- pathname: 路径;
- search: 查询字符串;
- hash: 哈希值;
- username:URL 中的 username(很多浏览器已经禁用);
- password:URL 中的 password(很多浏览器已经禁用);

### 常用方法

- assign:赋值一个新的 URL，并且跳转到该 URL 中;
- replace:打开一个新的 URL，并且跳转到该 URL 中(不同的是不会在浏览记录中留下之前的记录);
- reload:重新加载页面，可以传入一个 Boolean 类型;如果把该方法的参数设置为 true，那么无论文档的最后修改日期是什么，它都会绕过缓存，从服务器上重新下载该文档。

```javascript
location.assign("http://www.baidu.com");
location.href = "http://www.baidu.com";

location.replace("http://www.baidu.com");
location.reload(false);
```

## Screen 对象

在编程中使用不多，只用来表明客户端的能力，其中包括浏览器窗口外部的显示器的信息，如像素的宽度和高度，每个浏览器的 screen 对象都包含着不同的属性。根据具体业务场景去查询就好。

## history 对象

history 对象允许我们访问浏览器曾经的会话历史记录。

### history 对象有两个属性

- length:会话中的记录条数;
- state:当前保留的状态值;

### history 对象有五个方法:

- back():返回上一页，等价于 history.go(-1);
- forward():前进下一页，等价于 history.go(1);
- go():加载历史中的某一页;
- pushState():打开一个指定的地址;
- replaceState():打开一个新的地址，并且使用 replace;

## 总结

BOM 以 window 对象为依托，表示浏览器窗口以及页面可见区域

# DOM

document Object Model 文档对象模型

## 节点层次

DOM 可以把任何 HTML 和 XML 文档描绘成一个由多层节点构成的树形结构。

```html
<html>
  <head>
    <meta charset="UTF-8">
    <title>测试</title>
  </head>
  <body>
    <h1>标题</h1>
    <ul>
      <1i>
        <a href="#">链接</a>
      </1i>
    </ul>
  </body>
</html>
```

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1655647422547-3d058a55-75f4-4c1d-b64a-59083d2fd1ec.png#averageHue=%23d4d4d4&clientId=u1e7bbe51-ee6b-4&from=paste&height=365&id=u39cbab32&originHeight=567&originWidth=656&originalType=binary&ratio=1&rotation=0&showTitle=false&size=166861&status=done&style=none&taskId=udbe24baa-bbdc-4d80-a35f-7dfd2a5d62d&title=&width=422)

上图中就包括 DOM 的主要节点  
**Document 文档节点**  
表示整个 HTML 页面(相当于 document 对象)  
当需要访问任何标签、属性或文本时，都可以通过文档节点进行导航  
**Element 元素节点 ul h1 li**  
表示 HTML 页面中的标签(即 HTML 页面的结构)  
当访问 DOM 树时，需要从查找元素节点开始  
**Attr 属性节点 href**  
表示 HTML 页面中的开始标签包含的属性  
**Text 文本节点 比如 title 的内容**

## Node 类型

所有的 DOM 节点类型都继承自 Node 接口，每个节点都有一个 nodeType 属性，用于表明节点的类型。  
对于 HTML 文档，节点主要有以下六种类型：**Document 节点、DocumentType 节点、Element 节点、Attribute 节点、Text 节点和 DocumentFragment 节点。**

### 分类

| 节点             | 名称         | 含义                                  |
| ---------------- | ------------ | ------------------------------------- |
| Document         | 文档节点     | 整个文档（window.document）           |
| DocumentType     | 文档类型节点 | 文档的类型（比如 'DOCTYPE html'）     |
| Element          | 元素节点     | HTML 元素（比如 body、a 等）          |
| Attribute        | 属性节点     | HTML 元素的属性（比如 class="right"） |
| Text             | 文本节点     | HTML 文档中出现的文本                 |
| DocumentFragment | 文档碎片节点 | 文档的片段                            |

### 常用属性

Node 有几个非常用且重要的属性

- **nodeName**:node 节点的名称;
- **nodeType**:可以区分节点的类型;
- **nodeValue**:node 节点的值;
- **childNodes**:所有的子节点;

## Document 类型

JavaScript 通过 Document 类型表示整个文档。  
Document 类型可以表示 HTML 页面或者 其他基于 XML 的文档。不过最常用的应用还是作为 HTMLDocument 实例的 document 对象。  
在浏览器中 document 对象是 HTMLDocument 的一个实例，表示整个 HTML 页面。而且，document 对象也是 window 对象的一个属性，因此可以作为全局对象来访问。

### 常见属性

- document.body 返回文档的 body 元素
- document.title 返回当前文档的标题
- document.head 返回当前文档的 head 内容
- document.children[0] 返回当前文档 html 内容

### 常见方法

- 创建元素
- 获取标签元素

```javascript
// 创建元素
const imageEl = document.createElement("img");
const imageEl2 = new HTMLImageElement();

// 获取元素
const divEl1 = document.getElementById("box");
const divEl2 = document.getElementsByTagName("div");
const divEl3 = document.getElementsByName("title");
```

- 文档写入

将输出流写入到网页的能力。

```javascript
document.write();
document.writeln();
document.open();
document.close();
```

## Element 类型

Element 类型用于表现 XML 和 HTML 元素，提供了对元素标签名，子节点及特性的访问  
我们平时创建的 div、p、span 等元素在 DOM 中表示为 Element 元素

### 常见属性

- 子元素 children childNodes
- tagName
- id/class
- clientWidth/clientHeight
- clientLeft/clientTop
- offsetLeft/offsetTop

### 常见方法

获取特性 getAttribute 修改某个特性 setAttribute  
创建元素 document.createElement()

## Text 类型

文本节点由 Text 类型表示，包含的是可以照字面解释的纯文本内容

- 创建文本节点：document.createTextNode()

## Comment 类型

注释

## DocumentFragment 类型

DOM 规定文档片段是一种轻量级的文档，不会像完整的文档那样占有额外的资源

可以在里面保存将来会添加到文档的节点。要创建文档片段，可以使用 document.createDocumentFragment()  
文档片段本身永远不会成为文档树的一部分

## Attr 类型

元素的特性在 DOM 中用 Attr 类型来表示。很少使用。

## 总结

DOM1 级将 HTML 和 XML 文档看作一个层次化的节点树，方便 js 来直接操作。  
DOM 是由各种节点构成的

- 最基本节点是 Node,所有的节点都继承自 Node
- Document 表示整个文档
- Element 表示文档中的 HTML 或者 XML 元素

# DOM 扩展

对 DOM 的扩展主要包括 选择符 API 和 HTML5

## 选择符 API

选择符，最核心的两个 API 就是 querySelector() 和 querySelectorAll()  
可以通过 Document 和 Element 类型的实例调用

```javascript
const divEl1 = document.querySelector(".content");
const divEl2 = document.querySelectorAll(".content");
```

## HTML5

html5 涉及的面非常广，我们这里这讨论与 DOM 节点相关的内容

### 获取 dom 元素

- getElementsByClassName() 可以通过 document 对象和所有 HTML 元素调用该方法，查询一个或者包含类名的字符串

### 自定义数据属性

可以为元素添加非标准的属性，但是要添加前缀 data-  
通过元素的 dataset 属性来访问自定义的属性的值

```javascript
<div id="myDiv" data-appId="12345" data-name="yz"></div>;

var div = document.getElementById("myDiv");

var appId = div.dataset.appId;
var name = div.dataset.name;
```

### 插入标记

innerHTML 属性

- 可以获取调用元素的所有子节点对应的 html 片段
- 可以是根据指定的值创建 DOM 树，替换原有的元素节点

一般我们插入大量新 HTML 标记时，使用 innerHTML 与通过多次 DOM 操作先创建节点再指定它们之间的关系相比，效率更高，因为在设置 innerHTML 时就会先创建一个 HTML 解析器，这个解析是在浏览器级别的基础上代码（c++）运行的，因此比执行 js 快得多，当然也会带来 创建销毁 html 解析器也有性能消耗。所以控制次数。

# DOM2 和 DOM3

DOM1 级主要定义的是 HTML 和 XML 文档的底层结构  
DOM2 级和 DOM3 级是在这个结构基础上引入了更多的交互能力和特性。扩展了 DOM API,以满足操作 DOM 的所有需求，同时提供更好的错误处理和特性监测能力

## DOM 变化

- Node 类型 Document 类型 Element 类型 增加了命名空间的概念
- DOM3 引入 两个辅助比较节点的方法 isSameNode() 和 isEqualNode( )
  - 一个判断相同 两个节点引用的是同一个对象
  - 一个判断相等 两个节点是同类型的对象，具有相等的属性（nodeName,nodeValue 等等），而且他们的 attributes 和 childNodes 属性也相等（相同位置包含相同的）

```javascript
var div1 = document.createElement("div");
div1.setAttribute("class", "box");

var div1 = document.createElement("div");
div1.setAttribute("class", "box");

alert(div1.isSameNode(div1)); // true
alert(div1.isEqualNode(div1)); // true
alert(div1.isSameNode(div2)); // false
```

- iframe 中的文档对象可以通过 contentDocument 访问 所有浏览器都支持

```javascript
var iframe = document.getElementById("myIframe");
var iframe = iframe.contentDocuemnt || iframe.contentWindow.document;
```

## 样式变化

### 访问元素的样式

- 新增 style 属性 可以通过 style 对象访问所有样式信息，也可以进行修改
- document.default.getComputedStyle(dom) 可以返回整个 dom 的样式

### 操作样式表

- 大多数情况下，使用 style 属性就可以满足所有操作样式柜子的需求

### 元素大小

- 偏移量
  - offsetHeight offsetWdith offsetLeft offsetTop
  - 可以通过以上四个属性计算
- 客户区大小
  - clientHeight clientWidth
- 滚动大小
  - scrollHeight scrollWidth scrollLeft scrollTop
  - scrollLeft scrollTop 可以用来确定当前元素滚动状态

![未命名文件 (4).png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1657074872108-b6b8c636-a84e-49a0-9472-00d7b29e19b6.png#averageHue=%234c4c4c&clientId=u36c96a3e-0849-4&from=ui&id=u7a100413&originHeight=854&originWidth=1209&originalType=binary&ratio=1&rotation=0&showTitle=false&size=57165&status=done&style=none&taskId=u39319cc8-680b-47b1-9c3a-47f49a4d142&title=)

- 确定元素大小
  - getBoundClientRect()返回一个矩形对象，包含四个属性 left top right bottom,给出了元素在页面中相对于视口的位置。
  - top/y：元素上边到视窗上边的距离;
  - right：元素右边到视窗左边的距离;
  - bottom：元素下边到视窗上边的距离;
  - left/x：元素左边到视窗左边的距离;
  - width：元素的宽度;
  - height：元素的高度;

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1657074815517-883b2353-9948-42e9-9eef-861097f24146.png#averageHue=%23bfbfbe&clientId=u36c96a3e-0849-4&from=paste&height=550&id=u6a3f876e&originHeight=1099&originWidth=1466&originalType=binary&ratio=1&rotation=0&showTitle=false&size=69731&status=done&style=none&taskId=ud839abe3-2b2e-412a-b6df-4b3999bfd71&title=&width=733)

## 范围

为了让开发人员更方便得控制页面，dom2 定义了范围 range 接口。  
document.createRange()

- 用 dom 范围实现简单选择

```javascript
var range1 = document.createRange();
var p1 = document.getElementById("p1");

range1.selcetNode(p1);
```

- 也可以操作修改 dom 范围中的内容

## 总结

dom2 级主要新增了操作样式的能力和操作范围内 dom 的能力

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/dwvarg/fupno2
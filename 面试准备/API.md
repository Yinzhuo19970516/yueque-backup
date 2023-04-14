# API
## 类型

#### 基本类型

number string boolean null undefined bigInt symbol

#### 复杂类型

object

## 垃圾回收机制

### 自动垃圾回收算法

- 标记清除 从全局对象出发，能找的的定义为活动对象，找不到的定期删除，容易产生内存随便

![image.png](https://cdn.nlark.com/yuque/0/2023/png/1572912/1679985968960-df33e95e-5208-4b11-bfe2-61e95f81d576.png#averageHue=%23ace5a2&clientId=u152d6541-44ff-4&from=paste&height=540&id=u630345c6&name=image.png&originHeight=540&originWidth=1556&originalType=binary&ratio=1&rotation=0&showTitle=false&size=300340&status=done&style=none&taskId=ufa901aa0-62f2-4735-9835-b72cb9eec12&title=&width=1556)

- c 整个空间平均分为 from 和 to ，优先使用 from，当 from 占满，标记活动对象

，复制到 to 空间。from 和 to 互换  
![image.png](https://cdn.nlark.com/yuque/0/2023/png/1572912/1679985976688-c86ab4d8-b989-441b-845c-75040202881d.png#averageHue=%23e8f0e8&clientId=u152d6541-44ff-4&from=paste&height=1056&id=u8fcc4db1&name=image.png&originHeight=1056&originWidth=1394&originalType=binary&ratio=1&rotation=0&showTitle=false&size=575368&status=done&style=none&taskId=ufd4b8d54-1e36-4dd5-accc-8a00384a717&title=&width=1394)

- 引用计数 实时统计对象的引用书，当引用数为 0 时，回收对象
- 标记压缩 从全局对象出发，标记所有的活动对象，讲活动对象移到内存的一端，集中一起，直接清理到边界以外的内存，释放连续空间

![image.png](https://cdn.nlark.com/yuque/0/2023/png/1572912/1679985943541-712aaa6f-45ad-4d49-9b1a-24140c564fd6.png#averageHue=%23ace5a2&clientId=u152d6541-44ff-4&from=paste&height=522&id=u7404c8d7&name=image.png&originHeight=522&originWidth=1734&originalType=binary&ratio=1&rotation=0&showTitle=false&size=302673&status=done&style=none&taskId=u74d08b35-1317-451d-a46e-7f701c8d4e3&title=&width=1734)

- 代际假设和分代收集
  - 大多数对象的生命周期很短，很快就不再被需要了
  - 那些一直存活的对象通常会存在很久

### js 的垃圾回收

栈中垃圾回收  
**ESP** 指针下移，直接覆盖  
堆中垃圾回收  
新生代 1-8M

- 新生代用于存放生存时间短的对象，大多数新创建的小的对象都会被分配到该区域，该区域的垃圾回收会比较频繁
- 复制算法

老生代

- 即一些大的对象会被直接分配到老生代空间
- 新生代中经过两次垃圾回收后仍然存活的对象，会晋升到老生代空间
- **标记-压缩算法**
- 首先对活动对象进行标记，标记完成后，将所有存活对象移到内存的一段，然后清理掉边界外的内存。

**增量标记算法**，把一个完整的垃圾回收任务 **拆分为很多小任务**，这些小任务执行时间比较短，可以 **穿插** 在其他的 **JavaScript** 任务中间执行，这样当执行上述动画效果时，就不会让用户因为垃圾回收任务占用主线程而感受到页面的卡顿。

## csrf 跨域伪造请求

#### 同源检测

- Origin Header
- Referer Header

#### Samesite Cookie 属性

#### Token

#### 双重 cookies

## XSS 跨站脚本攻击

输入过滤  
转义 html  
避免使用 v-html  
httpOnly

## V8 如何执行一段代码

js 是动态语言 再运行时编译执行，这个过程通过解释器完成  
具体过程如下：

1. 解析：V8 首先会对输入的代码进行词法分析和语法分析，生成抽象语法树（AST）。
2. 编译：V8 将 AST 转换为字节码，并对字节码进行优化，以提高执行效率。
3. 解释/执行：V8 基于字节码执行程序，同时还有 JIT（即时编译器）技术，可以在运行时将热点代码转换为本地机器代码，进一步提高性能。
4. 垃圾回收：V8 使用自动垃圾回收器来管理内存，定期清理不再使用的对象，以避免内存泄漏和空间浪费。

## 块级作用域

## 闭包

闭包是一个函数，可以访问外部遍历

- 封装私有变量
- 处理回调函数 防抖节流等等

## 暂时性死区

暂时性死区是指在 ES6 中引入的一种变量声明行为，它使得在变量声明之前访问该变量会导致 ReferenceError。

1. const let
2. 函数参数中，使用默认值表达式时，如果表达式中引用了同名变量

## 词法作用域

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/dwvarg/mr938g0sebmvf5o5
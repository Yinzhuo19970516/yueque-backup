# 包管理工具pnpm
## 概述

pnpm - 速度快、节省磁盘空间的软件包管理器  
**perfomance npm，即 pnpm（高性能 npm）**

## 优势

- 快速
  - pnpm 是同类工具速度的将近 2 倍
- 高效
  - node_modules 中的所有文件均链接自单一存储位置
- 支持 monorepos

  > 注：这个东西这么读 monorepos = Monolithic repository /ˌmänəˈliTHik/ /rəˈpäzəˌtôrē/

  - pnpm 内置了对单个源码仓库中包含多个软件包的支持

- 严格
  - pnpm 创建的 node_modules 默认并非扁平结构，因此代码无法对任意软件包进行访问

以上是 4 条优势是官网的说明和宣传，后面我们会针对 npm 的发展历史中存在的问题，  
来对比说明，**pnpm 的提出动机，pnpm 的优势在哪里，为什么具备这些优势。**

## npm 发展历程中存在的问题

npm 全称，**Node Package Manager** node 包管理工具  
执行 npm install 之后。npm 帮我们下载对应的依赖包并解压到本地缓存，然后构造 node_modules 目录结构，写入依赖文件，对应的 node_modules 内部结构也经历了几个版本的变化。

### npm v1/v2 嵌套依赖

最开始其实没有注重 npm 包的管理，只是简单的嵌套依赖，这种方式层级依赖结构清晰  
但是随着 npm 包的增多，项目的迭代扩展，重复包越下载越多，造成了空间浪费，导致前端本地项目 node_modules 动辄上百 M  
在业务开发中，安装几个项目，项目体积好几 G，对使用者们极其不友好。

入下图所示，依赖包 C 在 AB 中都被引用了, 被重复下载了两次，其实是两个完全相同的东西。  
**从我们现在的角度看，完全没有必要。**  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660705882312-9cef4c07-08e7-4104-aefd-cd52301356be.png#averageHue=%23ececea&clientId=u3e5f15a6-a738-4&from=paste&height=430&id=ua6e714f4&originHeight=826&originWidth=946&originalType=binary&ratio=1&rotation=0&showTitle=false&size=33739&status=done&style=none&taskId=ue8b35505-e34e-4641-8c20-485d64b126f&title=&width=493)

### npm v3 扁平化

**node_modules 体积过大，嵌套过深**  
npm 团队也意识到这个问题，通过**扁平化**的方式，将子依赖安装到了主依赖所在项目中，以减少依赖嵌套太深，和重复下载安装的问题。

如下图所示，A 的依赖项 C 被提升到了顶层，如果后续有安装包，也依赖 C，会去上一级的 node_modules 查找，如果有相同版本的包，则不会再去重复下载，直接从上一层拿到需要的依赖包 C

> 说明：为什么自己的 node_modules 没有 C,也能在上层访问到 C 呢？
> require 寻找第三方包,会每层级依次去寻找 node_modules，所以即便本层级没有 node_moudles，上层有，也能找到

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660706126178-2db82d75-f7a4-40d3-9574-8b992a36732c.png#averageHue=%23ecebe9&clientId=u3e5f15a6-a738-4&from=paste&height=414&id=ua4c49b70&originHeight=834&originWidth=950&originalType=binary&ratio=1&rotation=0&showTitle=false&size=37940&status=done&style=none&taskId=u5bc6baa4-15ab-419e-a0ab-c9c3c2555a3&title=&width=472)

扁平化方式解决了相同包重复安装的问题，也**一定程度上**解决了依赖层级太深的问题。

**为什么说是一定程度上？**  
因为如上图所示，B 依赖的 C v2.0.0，并没有提升，依然是嵌套依赖。

因为在两个依赖包 C 的版本号不一致，只能保证一个在顶层，上图所示 C v1.0.0 被提升了，v2.0.0 没有被提升，后续 v2.0.0 还是会被重复下载，所**以当出现多重依赖时，依然会出现重复安装的问题。**

而且这个提升的顺序，也不是根据使用量优先提升，而是根据先来先服务原则，先安装的先提升。**这会导致不确定性问题**，随着项目迭代，npm i 之后得到的 node_modules 目录结构，有可能不一样。

与此同时，我们把 C，提升到了顶层，即使项目 package.json，没有声明过 C，但是也可以在项目中引用到 C,**这就是幽灵依赖问题。**

**可以说 npm v3 在解决嵌套依赖，重复安装问题的同时，又带来了新的问题。**

### npm v5 lock

npm v5 借鉴 yarn 的思想，**新增了 package-lock.json**。  
该文件里面记录了 package.json 依赖的模块，以及模块的子依赖。并且给每个依赖标明了版本、获取地址和验证模块完整性哈希值。  
通过 package-lock.json，保障了依赖包安装的确定性与兼容性，使得每次安装都会出现相同的结果。  
**这个就解决了不确定性的问题**  
**多重依赖和幽灵依赖**并没有好的解决方式。

## pnpm

综上，基于**npm 扁平化 node_modules**的结构下，虽然解决了依赖嵌套、重复安装的问题，但**多重依赖和幽灵依赖**并没有好的解决方式。  
pnpm 出现就是为了解决现在 npm 存在的问题，正如官网 pnpm 所形容自己的是一款**速度快，节省磁盘空间**的软件包管理器。  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660716784161-b9c9fc99-5d69-45fb-8b57-873af3476de3.png#averageHue=%23f1cda5&clientId=u86f5a14a-0ca4-4&from=paste&height=1332&id=uc65085f6&originHeight=1332&originWidth=2668&originalType=binary&ratio=1&rotation=0&showTitle=false&size=506978&status=done&style=none&taskId=u5a5f7671-3a72-4729-a3ea-71de34b3b13&title=&width=2668)

### 前置知识 软链接&硬链接

#### 简单理解

硬链接就是多个文件名指向了同一个文件，这多个文件互为硬链接。  
像是 JS 中的两个相同的对象，a 和 b 的真实内容指向堆中同一个地址，修改一个，同时改变，一荣俱荣，一损俱损。删除一个，并不影响另一个。

```javascript
let a = { test: 1 };
let b = a;
a.test = 2;
console.log(b); // {test:2}
```

软链接就是快捷方式，是一个单独文件。  
就像我们电脑桌面上的快捷方式，大小只有几字节，指向源文件，点击快捷方式，其实执行的就是源文件。

#### 专业理解

在 Linux 的文件系统中，保存在磁盘分区中的文件不管是什么类型都给它分配一个编号，称为索引节点号(Inode Index)。A 是 B 的硬链接（A 和 B 都是文件名）则 A 文件中的 inode 节点号与 B 文件的 inode 节点号相同，即一个 inode 节点对应两个不同的文件名，两个文件名指向同一个文件。  
软链接可以认为是指向文件地址指针的指针，即它本身拥有一个新的 inode 索引，但文件内容仅包含指向的文件路径。  
源文件被删除时，软链接也会失效，但硬链接不会，软链接可以对文件夹生效。  
软硬链接 是 linux 中**解决文件的共享使用问题的两个方式，目的也是为了节省磁盘空间。**

### node_modules 的层级结构

比如某项目中，package.json 里声明了 A 和 B，  
A 的 package.json 里声明了 C v1.0.0,B 的 package.json 里声明了 C v2.0.0  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660723182675-50d6a27b-f03c-465e-bee5-8ff32c65205b.png#averageHue=%23ececea&clientId=u86f5a14a-0ca4-4&from=paste&height=442&id=u5c3c3a67&originHeight=838&originWidth=948&originalType=binary&ratio=1&rotation=0&showTitle=false&size=39766&status=done&style=none&taskId=u65367aaf-d2d2-421b-8042-5abee2da341&title=&width=500)  
进行 pnpm i 之后，node_modules 的层级结构如下  
双键头代表硬链接  
单箭头代表软链接

```bash
node_modules
|_ A -> .pnpm/A@1.0.0/node_modules/A
|_ B -> .pnpm/B@1.0.0/node_modules/B
|_ .pnpm
  |_ A@1.0.0
    |_ node_modules
      |_ A => pnpm/store/A
      |_ C -> ../../C@1.0.0/node_modules/C
  |_ B@1.0.0
    |_ node_modules
      |_ B => pnpm/store/B
      |_ C -> ../../C@2.0.0/node_modules/C
  |_ C@1.0.0
    |_ node_modules
      |_ C => pnpm/store/C
  |_ C@2.0.0
    |_ node_modules
      |_ C => pnpm/store/C
```

以 A 包为例，A 的目录下并没有 node_modules，是一个软链接，真正的文件位于**.pnpm/A@1.0.0/node_modules/A 并硬链接到全局 store**中。

A 和 B 是我们在项目 package.json 中声明的依赖包，node_modules 除了 A，B 没有其他包，说明不是扁平化结构。也就不存在** 幽灵依赖的问题**。

.pnpm 中存放着所有的包。最终硬链接指向指向全局 pnpm 仓库里的 store 目录下。  
也就是说，我们所有的包，最终都以硬链接的形式，最终都在全局 pnpm/store 中，可以使得不同的项目从全局 store 寻找到同一个依赖，**大大节省了磁盘空间**。

如果上面这个文件列表不够直观，大家也可以看我参考官网画的结构图  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660727198627-51e004d7-d565-44d4-a010-abdec313311c.png#averageHue=%23e5eae5&clientId=u86f5a14a-0ca4-4&from=paste&height=1106&id=u6fa8da09&originHeight=1106&originWidth=1634&originalType=binary&ratio=1&rotation=0&showTitle=false&size=391907&status=done&style=none&taskId=u6d2e5afd-5877-4f47-82b7-6a55d713d0a&title=&width=1634)

## pnpm 验证

全局安装 brew install pnpm  
以我自己基于 vue-cli 封装的一个移动端项目 vue-template 为例  
github 地址如下  
[基于 vue-cli 二次封装的移动端框架，vue3 +vue-cli4 + webpack5 + 多入口打包 + 自动生成项目模版 + pinia + 数据持久化 + 路由动画 + axios 二次封装](https://github.com/Yinzhuo19970516/vue-template)

### npm i 之后

查看 node_modules **体积 293M**

> du -h -s node_modules  
> 293M node_modules

查看 package-lcok.json 中重复文件，以 postcss 为例，一眼就看到了两个版本的 postcss 版本，  
查看 node_modules 只有一个版本的 postcss 包会被提升，其他版本的就会被重复下载  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660744791278-e6a82632-351d-40f9-8be1-d87e51c37aca.png#averageHue=%232b2b2b&clientId=u86f5a14a-0ca4-4&from=paste&height=755&id=u79a01fba&originHeight=755&originWidth=1011&originalType=binary&ratio=1&rotation=0&showTitle=false&size=119632&status=done&style=none&taskId=u55af99a8-58a1-4bee-9da8-5c389d81a59&title=&width=1011)

### pnpm i 之后

查看 node_modules **体积 251M**

> du -h -s node_modules  
> 251M node_modules

切换到 node_modules 目录下，查看所有文件信息

> cd node_modules
> ls -alh

以 axios 库为例，只有 37B，只是一个快捷方式，axios 软链接指向  
这里的 37 字节其实是axios@0.26.1文件名的大小。  
**.pnpm/axios@0.26.1/node_modules/axios**  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660729895252-8bd4af53-87be-4b43-87b1-9a7d4dc1e8e7.png#averageHue=%230e0c0c&clientId=u86f5a14a-0ca4-4&from=paste&height=732&id=u1e7159ef&originHeight=732&originWidth=2880&originalType=binary&ratio=1&rotation=0&showTitle=false&size=322391&status=done&style=none&taskId=u8ced590a-063a-4d8e-bf6f-48d3e0f0a8c&title=&width=2880)  
切换到.pnpm 目录下，查看所有文件信息

> cd .pnpm
> ls -alh

我们看到 postcss 三个版本文件夹，说明现在项目里依赖三个版本的 postcss  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660730016170-b0658864-a999-4b21-8669-99d7b280fa8d.png#averageHue=%230f0d0c&clientId=u86f5a14a-0ca4-4&from=paste&height=482&id=u4ad3ef0b&originHeight=482&originWidth=2202&originalType=binary&ratio=1&rotation=0&showTitle=false&size=235330&status=done&style=none&taskId=u2350f2d4-a796-4f88-95a4-0d11ef15731&title=&width=2202)  
切换到postcss@7.0.39目录，查看文件信息

> cd postcss@7.0.39/node_modules/postcss
> stat -x package.json

值得关注的属性有两个，一个是 Links，表示硬链接个数，一个是 Inode  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660734461444-da19af4e-ffbf-4d23-8102-0d9a9d049131.png#averageHue=%236a3831&clientId=u86f5a14a-0ca4-4&from=paste&height=430&id=u2f23138d&originHeight=430&originWidth=1846&originalType=binary&ratio=1&rotation=0&showTitle=false&size=347467&status=done&style=none&taskId=uaa5ff136-d22e-4a16-aa41-71938cf285b&title=&width=1846)  
我们可以通过 Inode 去查询所有的硬链接

> find . -inum 8177610

可以看到，在全局 Library/pnpm/store/下对应的文件目录  
4 条记录 也对应了 links：4  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660734635787-052c1a29-bdd3-44e1-a4e4-48677e837161.png#averageHue=%23221716&clientId=u86f5a14a-0ca4-4&from=paste&height=232&id=ubbf1073e&originHeight=232&originWidth=2088&originalType=binary&ratio=1&rotation=0&showTitle=false&size=86296&status=done&style=none&taskId=u1dbf21b4-a7c9-444b-ab16-0358b79439c&title=&width=2088)

### 对比

当只一个项目时，node_modules 体积两者差距不大。  
我们使用两个相同项目，npm 的 node_modules 将达到近 600M,  
pnpm 依旧能保持 第二个相同项目 node_modules 的体积只有 54M,因为软链接和硬链接也会占用一部分体积。所以两个项目总体积在 54 + 253 = 300M。  
此时优势已经很明显了。  
举一个极端的例子，当有 10 个相同项目时，npm 的 node_modules 将达到 2930M，将近 3 个 G，而 pnpm 依旧能保持 住一个相对不大的体积。  
我们在业务开发时，其实一般都通用的模版，所以项目的依赖基本上一致，我觉得 pnpm 还是非常好的。

### 全局安装目录 pnpm-store 的目录结构

```bash
pnpm
└── store
    └── v3
        └── files
            ├── 00
              - cd3e571524c095736
              - 02a74db92f0368580
            ├── 01
            ├── 02
```

上图是我们全局目录下 pnpm 的目录结构。  
我们在全局目录里存放的不是 npm 包的源码，而是 hash 值，这里采用了基于文件内容寻址方案。  
简单来说就是文件内容被加密成了 64 位 hash 值，hash 值都是唯一的，如果文件内容不变，hash 值也不会变。  
这个非常适合 npm 的安装包，一般来说，依赖包的更新都是向下兼容的，两个版本的包差别只是部分，而我们使用 hash 存储，会根据文件内容变化，只会存储变化的部分，相同的部分，生成的 hash 不会变，只存储一份就够了，**一定程度上，也节省了磁盘空间。**

## pnpm 常见问题

### 为什么使用硬链接？ 为什么不直接创建到全局存储的软链接？

![image.png](https://cdn.nlark.com/yuque/0/2023/png/1572912/1692068612565-c1a78075-fad1-4869-a548-d205ed72e88f.png#averageHue=%23e4e4e4&clientId=uac2a5ad3-edb6-4&from=paste&height=1242&id=u819653ef&originHeight=2556&originWidth=1179&originalType=binary&ratio=2&rotation=0&showTitle=false&size=451715&status=done&style=none&taskId=uec2675e5-eca6-4bc1-bed7-8fd1f0e1b6a&title=&width=573)  
这个问题非常复杂，说来话长，我一点点分析，花了很多功夫在这个问题上，和大家分享一下调研结果。

#### 首先，pnpm 官网如此解释

> 直接软链至全局存储与 Node 的 --preserve-symlinks 标志一起使用是可行的，但是，该方法附带了个自己的问题，因此我们决定使用硬链接。

**大意就是可以做，但我们不想，因为会引发新的问题。**

#### require 直接引入软链接

软链接的文件中，使用 require 直接引用的包会报错，软链接会从文件原始位置开始查找依赖。  
我们希望的是软链可以将其他地方的目录增加到依赖查找路径中。

> 有兴趣可以去看 github 关于软链接引用报错的讨论，这时已经有人提出使用硬链接[https://github.com/nodejs/node/issues/3402](https://github.com/nodejs/node/issues/3402)

我们实验一下  
如下图，建立两个文件夹 a,b  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660799092118-405a8ce4-0ede-4f31-be8f-ab177b4ea517.png#averageHue=%23373e42&clientId=u86f5a14a-0ca4-4&from=paste&height=161&id=u9e03da40&originHeight=161&originWidth=508&originalType=binary&ratio=1&rotation=0&showTitle=false&size=31714&status=done&style=none&taskId=u14a0ffce-d300-4cfe-97a7-aac508cd15e&title=&width=508)  
a/index.js 中写入,b 中安装 qs 库

```bash
const test = require('qs')
console.log(test)
```

b 中建立 index.js 的软链接 index-s.js  
执行 node index-s.js 发现找不到模块  
因为软链接中的 require 软链接会从文件原始位置开始查找依赖，a 中没有 node_modules，直接报错了，但是如果是硬链接则不存在这样的问题  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660799369817-15839c78-260f-4308-a4a2-adbf383a06a6.png#averageHue=%23080808&clientId=u86f5a14a-0ca4-4&from=paste&height=412&id=ue94a83ce&originHeight=412&originWidth=1296&originalType=binary&ratio=1&rotation=0&showTitle=false&size=94564&status=done&style=none&taskId=u727817b2-0e43-4546-a723-95f75cf0d29&title=&width=1296)

#### --preserve-symlinks

最后 node 官方，增加了--preserve-symlinks 来专门处理软链接的引用路径问题。  
Node.js 有这样一个选项：–preserve-symlinks，可以设置成按照软链所在的位置查找依赖。

#### 新的问题

–preserve-symlinks 会引发新的问题，但是我查阅了 github 的 issues，有好几百条的讨论，没有看到有详细解释清楚这个问题的，我现在大概的理解就是 node 官方对软链接支持的不够好，即使提出了–preserve-symlinks，也有问题，所以 pnpm 团队不用了。

> 有兴趣可以看看老外们的讨论

> [https://github.com/npm/npm/issues/15133](https://github.com/npm/npm/issues/15133)

后来，我在 node.js 中文文档里找到着这么一句，但是自己没有验证  
使用 --preserve-symlinks 会有其他方面的影响。 比如，如果符号连接的原生模块在依赖树里来自超过一个位置，它们会加载失败。 （Node.js 会将它们视为两个独立的模块，且会试图多次加载模块，造成抛出异常。）  
[https://www.nodeapp.cn/cli.html#cli_preserve_symlinks](https://www.nodeapp.cn/cli.html#cli_preserve_symlinks)  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660800069035-c93208d0-53e2-46b3-9063-feb517c2fd81.png#averageHue=%23cecdcd&clientId=u86f5a14a-0ca4-4&from=paste&height=1472&id=uc0ed43a9&originHeight=1472&originWidth=2946&originalType=binary&ratio=1&rotation=0&showTitle=false&size=426557&status=done&style=none&taskId=u4e551644-eed2-4f1d-9e76-479c31610b0&title=&width=2946)

#### 最终作者抛弃了这个方案

![Xnip2022-08-18_10-05-59.jpg](https://cdn.nlark.com/yuque/0/2022/jpeg/1572912/1660788471184-9b7f219e-ca9f-44fc-b984-898af5e7b4f1.jpeg#averageHue=%23e7e2d7&clientId=u86f5a14a-0ca4-4&from=ui&id=u6017a8e0&originHeight=652&originWidth=1860&originalType=binary&ratio=1&rotation=0&showTitle=false&size=176791&status=done&style=none&taskId=u864c6fb9-17df-4e9d-ac3d-030ff913acb&title=)  
**通过和大佬们讨论分析后总结出两点原因**

- **如果全部使用软链接，删除一个软链接，导致就找不到解析目录了**
- **如果全部使用软链接，resolve 解析文件路径过长，不利于路径解析和文件管理**

### pnpm 弊端

#### 调试问题

所有项目引用的包都在全局一个地方，如果想对某个包进行调试，其他项目正好引用了，本地运行也会收到影响。

#### 兼容问题

> **symlink **即软连接的方式可能会在 windows 存在一些兼容的问题，但是针对这个问题，pnpm 也提供了对应的解决方案：在 win 系统上使用一个叫做 **junctions **的特性来替代软连接，这个方案在 window 上的兼容性要好于 symlink

我没有 windows 电脑，没有实验过，这条是从官网挪过来了。  
我理解的是 window 下也是可以使用的，pnpm 已经帮我们做了兼容，只是没有使用软链接的方案。

## 总结

最后我们再翻译翻译，pnpm 官网的这些话

**节省磁盘空间**  
pnpm 通过**hard link（硬连接）**机制，把包都存储在全局的 pnpm/store/目录下。当安装软件包时，其包含的所有文件都会硬链接自此位置，而不会占用额外的硬盘空间。  
pnpm 对于同一个包不同的版本也仅存储其增量改动的部分。

**快速**  
安装包之前，如果已经在全局安装过，就不会被再次下载了，节省了安装时间。随着项目增多，效果会越来越明显。

**支持单体仓库**  
pnpm 提供工作空间 workspace 能力,就是保证一个仓库内多个项目的 package.json 有自己生效的范围。  
这个 yarn npm 也支持，不算 pnpm 的突出点。  
我对 monorepos 也没有研究过，这块等后续有时间了，可以对比三个工具的 workspace 专题讨论。

**严格**  
pnpm 默认创建了一个非扁平化的 node_modules，因此代码无法访问未声明的包，解决了 npm 存在的幽灵依赖问题。

> 使用 pnpm 替代 yarn/npm，了解 pnpm 原理，降低了 node_modules 体积，并提升了装包以及部署速度，部署时间从 3 分钟优化到 2.5 分钟。

## 待研究的问题

- [ ] **pnpm-lock.yaml 文件里的属性和生成过程**
- [ ] pnpm 对 peerDependencies 的处理
- [ ] 老项目使用 yarn 或者 npm 如何迁移至 pnpm
- [ ] pnpm npm yarn 工作空间 workspace 的研究
- [x] Java 的 meavns 是怎么管理依赖包的，和前端有什么区别

Maven 是什么？ 读音是**['meɪv(ə)n]或['mevn]**，不要读作“妈文”。  
Maven 是一款自动化构建工具，专注服务于 Java 平台的项目构建和依赖管理，相当于前端的 npm。  
java 启动项目，需要在编译器里先配置本地的一个公共路径，公共路径存放全局所有项目依赖的 jar 包。  
jar 包和前端的 npm 包原理一样，对于嵌套依赖的问题，maven 的处理方式是对所有的 jar 包扁平化处理，因为存放到了全局一个自定义的目录下，**所以不存在重复下载的问题。但是存在幽灵依赖的问题。 java 开发们习以为常，如果严格控制，配置文件会有一堆**  
**java 引用包的时候是严格的**，需要声明包所属公司，包名，包版本，依赖是明确的，可以全部扁平化处理。  
**但是前端包管理是松散**，只有一行引用 require/import，并不知道具体要引用哪个版本的包，node_modues 里有哪个版本引用哪个版本。  
所以 npm 在扁平化处理的时候，做不到把两个相同包，不同版本全部扁平化，因为会代码导致引用出现紊乱。

## npm

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1662371558824-2cbec796-822f-4f22-be63-55724d42e82a.png#averageHue=%23f9f3f2&clientId=u41692371-e9e8-4&from=paste&height=1200&id=Xy7ga&originHeight=1200&originWidth=2292&originalType=binary&ratio=1&rotation=0&showTitle=false&size=292519&status=done&style=none&taskId=ue2bc282b-08a8-47c3-beaa-4a053a768ab&title=&width=2292)  
2010 年的产物，至今存在已经 10 多年  
模块数量和依赖关系日趋复杂化：

- 模块数量众多，截止 2022 年，[npm 包数量已经超过 200 万](https://link.zhihu.com/?target=http%3A//www.modulecounts.com/)，数倍于其他语言的模块数量。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1662456890614-24b0343a-763e-42e4-a226-843a8027b572.png#averageHue=%23f6f5f3&clientId=uc56bf6dd-f72f-4&from=paste&height=412&id=ucdbad73d&originHeight=426&originWidth=619&originalType=binary&ratio=1&rotation=0&showTitle=false&size=46330&status=done&style=none&taskId=ue1b2cbd7-cc3b-476d-a829-1506eb6be14&title=&width=598.5)

- 模块关系错综复杂，存在重复依赖，小文件很多，浪费磁盘空间并拖慢写入速度。**文件 IO，尤其是海量小文件的读写是非常耗时的。**

### npm install 过程

总结一下 npm install 的全过程  
首先检查 config，获取 npm 配置，项目级的.nmprc > 用户级的 .npmrcc > 全局.npmrc > npm 内置的.npmrc   
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1662386214886-55e749ed-000d-4a08-90ff-5355dc570d61.png#averageHue=%23313131&clientId=u55a5bcea-a50e-4&from=paste&height=312&id=ub157d5ee&originHeight=312&originWidth=1148&originalType=binary&ratio=1&rotation=0&showTitle=false&size=54713&status=done&style=none&taskId=ud6d9a620-6379-4090-8a7d-04c81bd1e0b&title=&width=1148)  
npm install 先检测是有 package-lock.json 文件:

- 没有 package-lock.json 文件
  - 根据 pacakage.json 递归构建依赖树，分析依赖关系，这是因为我们可能包会依赖其他的包，并且多个包之间会产生相同依赖的情况;
  - 下载时会检查是否有相关缓存
    - 有，将完整的内容解压到 nodee_modules
    - 没有，从 registry 仓库中下载压缩包(如果我们设置了镜像，那么会从镜像服务器下载压缩包);
      - **检查包的完整性**
      - 获取到压缩包后会对压缩包进行缓存(**从 npm5 开始有的, npm config get cache 可以查看地址**)
      - 将压缩包解压到项目的 node_modules 文件夹中
  - 最后生成 package-lock.json 文件
- 有 package-lock.json 文件
  - 检测 lock 中包的版本是否和 package.json 中一致
    - 不一致，那么会重新构建依赖关系，直接会走上面的流程;
  - 一致的情况下，会去优先查找缓存
    - 缓存没有找到，从 registry 仓库下载，直接走上面流程;
    - 命中缓存会获取缓存中的压缩文件
  - 将压缩文件解压到 node_modules 文件夹中;

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1662386002716-281a8ce4-7828-42f8-b08d-54b206910203.png#averageHue=%23f3f2f3&clientId=u55a5bcea-a50e-4&from=paste&height=710&id=u17b1995f&originHeight=710&originWidth=1302&originalType=binary&ratio=1&rotation=0&showTitle=false&size=84459&status=done&style=none&taskId=uf178984a-b8b7-4155-be21-6f41135d069&title=&width=1302)

使用 npm i --timing=true --loglevel=verbose 命令可以看到 npm install 的完整过程

### package-lock.json

package-lock.json 的作用是锁定依赖结构，即只要你目录下有 package-lock.json 文件，那么你每次执行 npm install 后生成的 node_modules 目录结构一定是完全相同的。  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660714954070-b626c074-7310-414a-9e04-d73614e14fa7.png#averageHue=%232b2b2b&clientId=u86f5a14a-0ca4-4&from=paste&height=850&id=VwAep&originHeight=850&originWidth=2016&originalType=binary&ratio=1&rotation=0&showTitle=false&size=463808&status=done&style=none&taskId=u4444c3bd-8d16-4559-8b28-f471db5b2b1&title=&width=2016)

- name:项目的名称;
- version:项目的版本;
- lockfileVersion:lock 文件的版本;
- requires:使用 requires 来跟踪模块的依赖关系;
- dependencies:项目的依赖
  - version 表示实际安装的版本;
  - resolved 用来记录下载的地址，registry 仓库中的位置;
  - requires 记录当前模块的依赖;
  - integrity 用来从缓存中获取索引，再通过索引去获取压缩包文件

### npm 缓存机制

```bash
npm config get cache
```

打开 /Users/yinzhuo/.npm/\_cacache

- content-v2

存放二进制文件，其实是 npm tar 包资源

- index-v5

是 content-v2 的文件索引信息

npm 在执行安装时，可以根据 package-lock.json 中存储的 integrity、version、name 生成一个唯一的 key 对应到 index-v5 目录下的缓存记录，从而找到 tar 包的 hash，然后根据 hash 再去找缓存的 tar 包直接使用。

```json
"lodash": {
  "version": "4.17.11",
  "resolved": "https://registry.npmjs.org/lodash/-/lodash-4.17.11.tgz",
  "integrity": "sha512-cQKh8igo5QUhZ7lg38DYWAxMvjSAKG0A8wGSVimP07SIUEK2UO+arSRKbRZWtelMtN5V0Hkwh5ryOto/SshYIg=="
}
```

### 包的完整性

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1662472333144-7b48480b-6921-40df-84ba-9f876497a080.png#averageHue=%23020202&clientId=uedf6f6a8-8725-4&from=paste&height=568&id=u5f94869f&originHeight=568&originWidth=1732&originalType=binary&ratio=1&rotation=0&showTitle=false&size=133974&status=done&style=none&taskId=uffee2876-608b-4a0e-9cef-fe536c1d847&title=&width=1732)   
例如我们执行 npm info 命令，shasum 值就是 hash 值，用户下载依赖包到本地后，需要确定在下载过程中没有出现错误，所以在下载完成之后需要在本地在计算一次文件的 hash 值，如果两个 hash 值是相同的，则确保下载的依赖是完整的，如果不同，则进行重新下载。

## yarn

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1662370264803-ddbe58bd-b835-4925-9d16-1269899558a1.png#averageHue=%23f8f5f1&clientId=u41692371-e9e8-4&from=paste&height=345&id=ue1e4027f&originHeight=722&originWidth=928&originalType=binary&ratio=1&rotation=0&showTitle=false&size=419233&status=done&style=none&taskId=uabc58ee5-2150-4364-bf03-04ab2e182ad&title=&width=444)

### 概述

yarn 是在 2016 年发布的，那时 npm 还处于 V3 时期，那时候还没有 package-lock.json 文件，就像上面我们提到的：不稳定性、安装速度慢等缺点经常会受到广大开发者吐槽。此时，yarn 诞生：  
Yarn 被定义为快速、安全、可靠的依赖管理。

- Yarn 缓存了每个下载过的包，所以再次使用时无需重复下载。 同时利用并行下载以最大化资源利用率，因此安装速度更快。
- 在执行代码之前，Yarn 会通过算法校验每个安装包的完整性。
- 使用详细、简洁的锁文件格式和明确的安装算法，Yarn 能够保证在不同系统上无差异的工作。

### 优势

- 离线模式

如果你以前安装过某个包，再次安装时可以在没有任何互联网连接的情况下进行。  
**（本地有缓存，优先取缓存）**

- 确定性

  不管安装顺序如何，相同的依赖关系将在每台机器上以相同的方式安装。  
  **（lock.file 控制）**

- 网络性能

Yarn 有效地对请求进行排队处理，避免发起的请求如瀑布般倾泻，以便最大限度地利用网络资源。  
**（可并行请求，可通过--network-concurrency 自定义上限，维护了一个请求队列）**

- 相同的软件包

从 npm 安装软件包并保持相同的包管理流程。  
**（迁移简单）**

- 网络弹性

重试机制确保单个请求失败并不会导致整个安装失败。  
**（自动重发策略（请求失败尝试重发，单请求重发上限为 5 次），实现思路为维护一个离线队列存放失败未重发请求，每 3s 出队一个进行重发。当超过重发上限，输出“info There appears to be trouble with your network connection. Retrying...”提示，表示放弃该请求。）**

- 扁平模式

将依赖包的不同版本归结为单个版本，以避免创建多个副本。  
**（扁平化处理）**

### lockfile

yarn 也是采用的是 npm v3 的扁平结构来管理依赖，安装依赖后默认会生成一个 yarn.lock 文件  
yarn.lock 锁文件把所有的依赖包都扁平化的展示了出来，对于同名包但是 semver 不兼容的作为不同的字段放在了 yarn.lock 的同一级结构中。

```javascript
package-1@^1.0.0:
  version "1.0.3"
  resolved "https://registry.npmjs.org/package-1/-/package-1-1.0.3.tgz#a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0"
package-2@^2.0.0:
  version "2.0.1"
  resolved "https://registry.npmjs.org/package-2/-/package-2-2.0.1.tgz#a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0"
  dependencies:
    package-4 "^4.0.0"
package-3@^3.0.0:
  version "3.1.9"
  resolved "https://registry.npmjs.org/package-3/-/package-3-3.1.9.tgz#a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0"
  dependencies:
    package-4 "^4.5.0"
package-4@^4.0.0, package-4@^4.5.0:
  version "4.6.3"
  resolved "https://registry.npmjs.org/package-4/-/package-4-2.6.3.tgz#a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6q7r8s9t0"
```

yarn.lock 中并没有记录确定的 node_modules 层级结构，依赖信息在 yarn.lock 中扁平记录，需要 package.json 提供首层依赖做为入口递归构建提来树结构结构

### yarn 的缓存

```javascript
yarn cache dir
```

yarn 的缓策略，每个缓存的模块被存放在独立的文件夹，文件夹名称包含了模块名称、版本号等信息  
命名方式：npm-[package name]-[version]-[shasum]  
npm-axios-0.26.1-1ede41c51fcf51bbbd6fd43669caaa4f0495aaa9-integrity  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1662470048541-0c19b0e9-49be-4797-9596-1e37b3e9e1b7.png#averageHue=%23f4f4f4&clientId=uedf6f6a8-8725-4&from=paste&height=410&id=u3f13b08f&originHeight=410&originWidth=785&originalType=binary&ratio=1&rotation=0&showTitle=false&size=56545&status=done&style=none&taskId=u01394d38-d4a4-4d39-9561-1c8118895e5&title=&width=785)

### yarn install

**检测 -> 解析 -> 获取 -> 链接 -> 构建**

- 检测包
  - 检测是否有 npm 相关文件文件,比如 package-lock.json 等;如果有,就会有相关的提示用户注意：这些文件可能会存在冲突。
  - ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1662467650622-9cbd25db-9c2b-4ce6-9636-e8b394f36e89.png#averageHue=%231d1d1d&clientId=uedf6f6a8-8725-4&from=paste&height=70&id=u596992b1&originHeight=70&originWidth=586&originalType=binary&ratio=1&rotation=0&showTitle=false&size=17335&status=done&style=none&taskId=u1802e533-82fb-4454-8c26-49ef31d457c&title=&width=586)
  - 检查 os cpu 等信息，部分包会设置
- 解析包
  - 获取 package.json 中依赖 遍历首层依赖获取依赖包版本信息(dependencies 和 devDependencies) ,
  - 遍历首层依赖，调用 find 方法获取依赖包的版本信息，然后递归调用 find，查找每个依赖下的 dependence 中依赖的版本信息。在解析包的同时使用一个 Set（fetchingPatterns）来保存已经解析和正在解析的 package。
  - 对每个包会先判断是否被解析过
    - 对于已经解析过的包会放到一个队列中先不处理
    - 对于没解析过的包 A , 会尝试 yarn.lock 中获取版本信息,并且标记为已解析
      - 如果在 yarn-lock 中没有找到包 A , 则向 registry 发起请求 , 获取满足版本范围的最高版本的包信息 获取后 标记成已解析
  - 遍历队列在已经解析过的包信息中，找到最合适的可用版本信息
  - 这个流程结束后 得到了所有依赖的版本信息 和 下载地址

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1662468419455-6c301c1c-2ab8-4c00-8a47-addcb2cb850b.png#averageHue=%23efefee&clientId=uedf6f6a8-8725-4&from=paste&height=964&id=u5afdd59a&originHeight=964&originWidth=1974&originalType=binary&ratio=1&rotation=0&showTitle=false&size=128064&status=done&style=none&taskId=u81de0cd4-df45-43e5-86d5-3c459bfb460&title=&width=1974)

- 获取包
  - 检查缓存中是否存在当前依赖包 , 不存在的包下载到缓存目录，yarn 根据包信息，生成一个 path 判断系统中是否存在该 path 证明是否有缓存
  - 对于没有命中缓存的包 yarn 会维护一个 fetch 队列 按照下载地址去获取包
  - 下载每个包的时候，会先在缓存目录下创建其对应的缓存目录，然后对包的 reference 进行机解析
  - 如果 reference 是相对路径或者 file 说明是本地，直接从缓存中获取包，否则下载
  - 更新 lockfile
- 链接包
  - 解析 peerDependencies ，如果发现冲突 给 warning 提示
  - 扁平化依赖树
    - **yarn 是在扁平化依赖树阶段分析同一依赖包不同版本的使用频率，选择利用率最大的版本放置在顶层。这一过程称为 dedupe。在 npm 中需要通过 npm dedupe 手动进行这一操作**
  - 执行拷贝任务
  - 解压到 node_modules
- 构建包
  - 如果有 2 进制包等需要构建的包 , 在这一步进行构建

## 展望

cnpm/tnpm -- rapid 模式最近刚刚开源，自己模拟了一个文件系统，还挺复杂的  
[FUSE](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Filesystem_in_Userspace) (FileSystem in Userspace)，即 **用户态文件系统**。  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1662517940647-8f3375c9-1ed8-470f-8b2b-04b5768ad8e2.png#averageHue=%23dbedc7&clientId=u646af24f-630a-4&from=paste&height=212&id=u0d26f491&originHeight=283&originWidth=720&originalType=binary&ratio=1&rotation=0&showTitle=false&size=128287&status=done&style=none&taskId=ud9ec198c-840e-4e58-a04d-5a662bee8bf&title=&width=539)  
deno -- import url  
yarn v2 -- 升级成本过大

## 参考链接

> pnpm 官网 [https://pnpm.io/zh/](https://pnpm.io/zh/)

> 软链、硬链对 Node.js 包寻找的影响 [https://meixg.cn/2021/01/25/ln-nodejs/#%E8%BD%AF%E9%93%BE%E4%BE%9D%E8%B5%96%E7%9B%AE%E5%BD%95](https://meixg.cn/2021/01/25/ln-nodejs/#%E8%BD%AF%E9%93%BE%E4%BE%9D%E8%B5%96%E7%9B%AE%E5%BD%95)
> 前端包管理工具 npm yarn cnpm npx [https://juejin.cn/post/7102442481920425997](https://juejin.cn/post/7102442481920425997)  
> pnpm 有什么优势 [https://q.shanyue.tech/engineering/751.html#pnpm](https://q.shanyue.tech/engineering/751.html#pnpm)
> 前端工程化 - 剖析 npm 的包管理机制
> [https://juejin.cn/post/6844904022080667661](https://juejin.cn/post/6844904022080667661)
> 从源码角度分析 yarn 安装依赖的过程 [https://juejin.cn/post/6847902225809801229](https://juejin.cn/post/6847902225809801229) 

###

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/fcqe0z
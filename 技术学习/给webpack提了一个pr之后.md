# 给webpack提了一个pr之后......
我不是标题党啊，是真的给 webpack 提了一个 pr，提交之后，脑子里就是一句话：“纸上学来终觉浅，绝知此事要躬行”。  
欲知来龙去脉，听我娓娓道来。

pr 如下，[https://github.com/webpack/webpack/pull/16292](https://github.com/webpack/webpack/pull/16292)，目前还是 unreviewed 状态。  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1664185943801-58ced821-26ee-4ff9-9d46-17cbe473df3d.png#averageHue=%23fefefd&clientId=u78d8965e-9acf-4&from=paste&height=437&id=u42628ab5&name=image.png&originHeight=873&originWidth=1562&originalType=binary&ratio=1&rotation=0&showTitle=false&size=202658&status=done&style=none&taskId=u3a9f5881-5b39-4ae0-bda9-ed9e758d7cb&title=&width=781)

阅读此文章你将会看到以下知识点，

- webstrom 调试 webpack 源码过程
- webpack 优化->deterministic 属性作用
- 如何提给开源仓库 pr
- 如何修改 commit 信息
- 如何合并 commit 信息
- EasyCLA 开源协议签署遇到的问题

看到 = 学会，如果对屏幕前的大帅比大漂亮有帮助的话，点个赞什么的就太好了！

## 背景

那是一个周五的晚上，11.左右，大部分人都准备休息了，我正在做山月的**linux 训练营**，赶巧，山月在 webpack 训练营里，圈出一处 webpack 的源码，8 行左右吧。**说到，此处有优化空间，可以提 pr。**  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1664162807282-a406f1e2-247f-4f80-b73d-ec1ca6a7e7d0.png#averageHue=%23faf3e0&clientId=ub82f7642-0bd4-4&from=paste&height=559&id=u519590b4&name=image.png&originHeight=1248&originWidth=1492&originalType=binary&ratio=1&rotation=0&showTitle=false&size=253626&status=done&style=none&taskId=u8949e207-91d3-49e1-8ea7-20cdab0a2fa&title=&width=668)  
盯着这段代码，我看了半天，没有上下文，一脸懵圈。大部分人，没有看过源码，直接看肯定看不出毛病，我也是这部分人的一部分。

后来找了一个做前端的朋友一起看，寻取帮助，他刚刚团建完到家，毫无学习状态。

我知道有些路注定要一个人走，自己调试 webpack 源码吧。

## 调试

我是用的工具是 webstorm。  
首先我在 node_modules 中 webpack 目录下，全局搜索，迅速定位到图示函数。  
**不得不说，webstorm 的搜索功能真是嘎嘎强！**  
定位到文件，lib/ids/DeterministicModuleIdsPlugin.js 下，看代码不一定知道是什么逻辑，不过看名字却很清楚了，是处理 DeterministicModuleIds 的一个插件函数。  
先简单说说 Deterministic

### deterministic

正巧，最近刚刚在 webpack 训练营学习了 Deterministic  
正好复习一遍。  
这是一个 webpack 优化项

```javascript
optimization: {
    moduleIds: 'deterministic',
    chunkIds: 'deterministic'
}
```

在生产环境下，二者将被 webpack 默认配置为 deterministic。  
这说明这个配置项非常 nice，webpack 已经帮我们用了，那聪明的你肯定要问了，它是干什么的呢？  
告知 webpack 当选择 moduleId 和 chunkId 时需要使用哪种算法  
deterministic 在不同的编译中不变的短数字 id(最少三位)。有益于长期缓存。

**_翻译翻译就是=>_**

生成确认的 id，这样可以有效避免由于模块引入的顺序改变而导致的产物大面积更改的问题，每个 module/chunk 都有自己确定的 id。

### 举例说明

比如在某项目某文件中，引入 A 模块，第一行 import(A)过了一段时间，需要引入 B，新的模块，一般来说我们会放在模块引入最下面，但是有个菜鸟，他在第一行增加 import(B)，import(A）就放在了第 2 行，并提交构建。  
那么新增 B，导致 A，以及以前原有模块（我叫它们 A+），引入顺序都发生了变化，导致模块 id 发生变化，进而导致文件打包出来的文件名发生变化。  
聪明的你，肯定要问了，这有问题吗。其实没啥大问题！不会有任何异常。

但是有没有更好的解决方案，有！

deterministic 这个配置项就可以帮助我们，无论新增或者减少模块，把原有的模块对应的 moduleId 和 chunkId 每次打包出来都一样。

这样我们就可以有效的利用浏览器缓存了。

当然了，不做也可以，大不了新增模块，所有 chunk 都发现了变化，打开页面慢一点而已。

现在其实也已经可以不关心了，因为 webpack 已经是默认配置了。  
我只是大概说了下 deterministic 的作用，关于 deterministic 的原理，等后面有余力了，再整一篇。

### 开始调试

说了这么多，就是为了铺垫，现在我们开始调试。  
在 webpack 训练营的 demo 中，有这么一个例子，正好用到了 deterministic。

```javascript
//build.js
const path = require("path");
const webpack = require("webpack");
const normalConfig = {
  entry: "./index.js",
  mode: "none",
  output: {
    filename: "[name].[id].[contenthash].js",
    chunkFilename: "[name].[id].[contenthash].chunk.js",
    path: path.resolve(__dirname, "dist/normal"),
  },
  optimization: {
    runtimeChunk: {
      name: (entrypoint) => `runtime-${entrypoint.name}`,
    },
    chunkIds: "deterministic",
    moduleIds: "deterministic",
  },
};

f1().run((err, stat) => {
  console.log(JSON.stringify(stat.toJson(), null, 2));
});
```

我使用的开发工具是 webstorm，调试代码特别的方便。  
在 f1 函数处，点击一下打上断点。  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1664174619753-19442a31-3ab7-4fe8-9746-dfef04d06234.png#averageHue=%23312c2b&clientId=ubc8aec3d-0935-4&from=paste&height=65&id=uba06ebe7&name=image.png&originHeight=129&originWidth=616&originalType=binary&ratio=1&rotation=0&showTitle=false&size=15871&status=done&style=none&taskId=u3fc100b3-7e3e-4a32-8635-122370a3e8c&title=&width=308)  
进入到 node_modules 的 webpack/lib/ids/DeterministicModuleIdsPlugin.js  
打上断点  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1664176705942-7173a782-242a-48ad-92b9-3a784aa417ba.png#averageHue=%232e2a2a&clientId=ubc8aec3d-0935-4&from=paste&height=304&id=u8bf37774&name=image.png&originHeight=608&originWidth=1009&originalType=binary&ratio=1&rotation=0&showTitle=false&size=84115&status=done&style=none&taskId=ube8ccc4e-06cf-47cd-9567-1d53b0d55e7&title=&width=504.5)  
右键 build.js，选择调试 build.js，打开 webpack 调试界面，代码此时已经运行到我们的我们设置的第一个断点处  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1664175172814-aff509c5-5e81-4239-8421-d4490dde7b59.png#averageHue=%23464a4b&clientId=ubc8aec3d-0935-4&from=paste&height=326&id=uc5b8c30f&name=image.png&originHeight=651&originWidth=495&originalType=binary&ratio=1&rotation=0&showTitle=false&size=179500&status=done&style=none&taskId=ud7888cd7-56b1-4aba-b892-2e241339a81&title=&width=247.5)

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1664175207224-3f0e852b-2cb4-4d43-b1a8-c86d7aa3dc3d.png#averageHue=%235e6f5c&clientId=ubc8aec3d-0935-4&from=paste&height=460&id=uafccd61c&name=image.png&originHeight=920&originWidth=1902&originalType=binary&ratio=1&rotation=0&showTitle=false&size=305982&status=done&style=none&taskId=u8a3f69d3-d199-4bb3-80c5-6f629100f9b&title=&width=951)  
点击 **>>|** 按钮，直接运行到下一个断点处，可以看到圈出来的代码，usedIds 是一个 set，用来存放 moduleId  
原代码逻辑是，先获取了原 usedIds 的长度 size，插入新的 id，如果发现 size 没变，说明 id 重复了，如果 size 变了，则说明 id 没有重复。  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1664176861312-ee925043-2329-4116-871c-852296f6f43a.png#averageHue=%232e2b2a&clientId=ubc8aec3d-0935-4&from=paste&height=134&id=uf44330e5&name=image.png&originHeight=268&originWidth=1330&originalType=binary&ratio=1&rotation=0&showTitle=false&size=50386&status=done&style=none&taskId=ud5d7695d-f80d-49e4-b77e-3655313341c&title=&width=665)  
其实就是想判断 usedIs 中有没有 id,没有必要绕这么一大圈用 size 来判断，可以直接用 has 来判断 id 在不在 usedIds 中。时间复杂度一样，还不用额外声明变量。  
所以对上述代码进行改写  
改写前

```javascript
(module, id) => {
							const size = usedIds.size;
							usedIds.add(`${id}`);
							if (size === usedIds.size) {
								conflicts++;
								return false;
							}
							chunkGraph.setModuleId(module, id);
							return true;
},
```

改写后

```javascript
(module, id) => {
							if (usedIds.has(`${id}`) {
								conflicts++;
								return false;
							}
							usedIds.add(`${id}`);
							chunkGraph.setModuleId(module, id);
							return true;
},
```

## 创建提交 pr

- fork，webpack 仓库到自己 github 仓库
- git clone 到本地
- 从 main 分支拉过来，新建一个靠谱的分支 feature
- 修改代码
- git add . 提交信息
- git commit -m "XXXX" 填写靠谱的描述信息
- git push --set-upstream origin feature
- 此时登陆 github 就会看到自己 fork 的仓库出现一个 Compare & pull request  
  ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1664181266091-cced8cb0-d9f9-4c75-b52c-8d03f0232238.png#averageHue=%23fbf1c2&clientId=ubc8aec3d-0935-4&from=paste&height=384&id=u8c7a0c51&name=image.png&originHeight=767&originWidth=1037&originalType=binary&ratio=1&rotation=0&showTitle=false&size=185054&status=done&style=none&taskId=u04706099-a506-4c85-a5ca-4f83f04be26&title=&width=518.5)
- 点击，进入 webpack 仓库代码提交合并页面，仿照以前的提交的 git log 格式，填写信息，之后，点击 create pull request  
  ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1664181404902-fc8cbb51-6613-42fe-9d21-70f9499d3f28.png#averageHue=%2347a85e&clientId=ubc8aec3d-0935-4&from=paste&height=420&id=u97136c5e&name=image.png&originHeight=839&originWidth=1074&originalType=binary&ratio=1&rotation=0&showTitle=false&size=164535&status=done&style=none&taskId=u745ced81-eaee-41e9-9f23-218e3e4851f&title=&width=537)
- 创建 pr 之后，就成功了，我们就可以在 webpack 源码仓库看到提交的 pr 了。

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1664181915450-4dc69e0d-42cf-4c5d-a222-f47e4e76d148.png#averageHue=%23fefefd&clientId=ubc8aec3d-0935-4&from=paste&height=406&id=u99c06cbc&name=image.png&originHeight=812&originWidth=1334&originalType=binary&ratio=1&rotation=0&showTitle=false&size=183911&status=done&style=none&taskId=uf44c78b4-5c9e-4b7c-9077-e65ba8780e1&title=&width=667)

### 如何修改 commit

当我们提交之后，发现自己 commit 信息提交的不合适，或者有歧义，如何修改呢?

- git log 之后，可以看到你之前提交过的 git 历史：
- 接下来，在 bash 里输入 wq 退出 log 状态，执行：

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1664182533827-815408fd-6f7e-4cbb-9947-579db9e970dd.png#averageHue=%23060606&clientId=u1818fa0c-8089-4&from=paste&height=341&id=u4ef0b4a4&name=image.png&originHeight=682&originWidth=1140&originalType=binary&ratio=1&rotation=0&showTitle=false&size=129591&status=done&style=none&taskId=u3416ca0f-aa43-45e1-87eb-b6d7539748c&title=&width=570)

- git commit --amend

       	这时bash里会出现以下内容：

  ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1664182582585-aed907bd-8e7a-42e1-925c-dc96c274dc0b.png#averageHue=%230b0b0b&clientId=u1818fa0c-8089-4&from=paste&height=383&id=u342eb5cc&name=image.png&originHeight=766&originWidth=1126&originalType=binary&ratio=1&rotation=0&showTitle=false&size=129711&status=done&style=none&taskId=u5a2c0789-082c-4f6a-9df6-9660df11ca5&title=&width=563)

- 键入 i 进入输出模式。修改 commit 信息
- 修改完毕之后，:wq 保持修改
- 此时 git log 已经能看到我们的修改了

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1664182737337-05812cd2-4a15-4a82-8ce4-adbc0e9b09e5.png#averageHue=%23080606&clientId=u1818fa0c-8089-4&from=paste&height=356&id=u4e11402c&name=image.png&originHeight=712&originWidth=1106&originalType=binary&ratio=1&rotation=0&showTitle=false&size=127437&status=done&style=none&taskId=uccbdcc43-c793-4fa9-8787-9e0dfb9afed&title=&width=553)

- git push -f 提交修改

### 如何合并 commit

因为是给源码提 pr，所以当时很慎重，我分几次提交，导致有 3 次 commit，山月老师建议合并成一次，自己开发随心所欲惯了，忽然要合并几次提交，就非常生疏，特意查了一下，所以记录一下。

- git log，获取 commit id
- 如果需要合并最近两个，需要获取倒数第 3 个 commitId  
  ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1664184743619-fd39f3fa-eccd-4299-9bbf-a2d7c554991f.png#averageHue=%230b0808&clientId=u78d8965e-9acf-4&from=paste&height=353&id=u80ddcf60&name=image.png&originHeight=706&originWidth=1104&originalType=binary&ratio=1&rotation=0&showTitle=false&size=140234&status=done&style=none&taskId=u89298687-5a49-4a93-8f15-4e26d89581b&title=&width=552)
- git rebase -i e0108eeb2972553d

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1664184839460-6455a0b7-4a07-4cfd-bde6-2c3c71d77237.png#averageHue=%23151515&clientId=u78d8965e-9acf-4&from=paste&height=371&id=uf45888e0&name=image.png&originHeight=742&originWidth=1142&originalType=binary&ratio=1&rotation=0&showTitle=false&size=198849&status=done&style=none&taskId=uf8a3fb8c-a4f2-48e6-90a5-3b6b0f4ab53&title=&width=571)

- 按照默认顺序，可以将除第一个提交外，都使用 fixup 或 squash 进行标记，最终你将得到这些 commit 向上合并，最终变成一个，提交信息是 pick 标记的信息。
  - pick：使用此提交不做操作
  - squash：将指定的提交合并到上边的 pick 请求中，保留提交信息
  - fixup：与 squash 类似，但不保留提交信息
- 把不需要的记录 改成 fixup ，:wq 保存

git push origin --force  
完成上述操作之后，整个 git 提交记录就会完全改变了，而且是不可逆操作，当然这也意味着 rebase 操作是有一定风险的，如果你不太清楚需要做什么的话就不要做。

### 签署 EasyCLA

一个开源协议，承诺自己的代码可供开源使用  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1664185254907-e80b4b9a-926e-43e9-bc67-4e6b7b878bf8.png#averageHue=%23f3edf0&clientId=u78d8965e-9acf-4&from=paste&height=124&id=u44136a96&name=image.png&originHeight=247&originWidth=898&originalType=binary&ratio=1&rotation=0&showTitle=false&size=37969&status=done&style=none&taskId=u078bd1e5-7d98-4575-9d2e-fbe5f1c35f7&title=&width=449)  
这里需要注意，自己提交信息中的邮箱作者信息，需要和签署的邮箱一致，如果不一致，即使签署依然显示无效。  
而我这里遇到的问题就是邮箱作者信息用的是公司 gitLab 的邮箱信息，所以 github 识别不到。

### 如何修改邮箱作者信息

```bash
git commit --amend --author="otheruser <otheremail@qq.com>"
git rebase --continue
git push -f origin
```

## 总结

以上就是我提交 pr 的来龙去脉，至于提交 pr 的意义大家自己体会。  
无论 pr 会不会被 merge，这都是 webpack 团队事情了，对于我而言，从这个过程中，其实用到的知识点很多，都会细碎，也很简单，一但连贯起来。我遇到了很多很多问题，所以才深深感觉到“**纸上学来终觉浅，绝知此事要躬行**”。  
看再多博客知识视频，不如自己动手操作一次！

## 参考

[https://zhuanlan.zhihu.com/p/100243017](https://zhuanlan.zhihu.com/p/100243017)  
[https://wangbjun.site/2022/coding/git-rebase.html](https://wangbjun.site/2022/coding/git-rebase.html)  
[https://oschina.gitee.io/opensource-guide/](https://oschina.gitee.io/opensource-guide/)

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/gefmn9
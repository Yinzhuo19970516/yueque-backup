# day8 ln
1. 熟练使用 ln 命令

ln，在两个文件间创建链接，默认为硬链接。  
ln -s，创建硬链接

2. 软链接文件的文件大小是如何得到的

**软链接是个快捷方式，大小是的文件名的字节数**

3. npm link 的工作原理是什么

npm link 一般用于在项目中本地调试自己开发的包。  
我们先试试 npm link 的使用，我们在自己的包中执行 npm link  
用 npm list -g 查看全局安装的包  
发现全局目录 opt/homebrew/lib 多了一个包，是一个软链接，指向我们自己的包的真实路径  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1663924456569-62efabd0-df89-402d-a216-d8be47909673.png#clientId=ue85ef6f1-715f-4&from=paste&height=244&id=u93236775&name=image.png&originHeight=488&originWidth=1490&originalType=binary&ratio=1&rotation=0&showTitle=false&size=123183&status=done&style=none&taskId=ue5fef7fe-7f72-4c73-92e0-7bec6d0f13a&title=&width=745)  
我们在项目中执行 npm link A  
就会在项目 node_modules 中建立一个指向 A 的软链接  
**所以 npm link 如果后面不跟文件名，会把当前目录当作一个包，在全局创建一个软链接  
npm link + 包名，会去全局目录寻找这个包，找到真实地址后，在当前目录的安装路径里建立一个软链接指真实地址。**

4. 在 pnpm 中，为什么不全部使用软链接

如果全部使用软链接，删除源文件，对应的软链接就找不到了，导致就找不到解析目录了。  
如果全部使用，resolve 解析文件路径过长，不利于管理和解析。

5. 在 Node.js 或其它语言中如何执行 ln

fs.symlinkSync('./a.js','./b.js');

## 作业

1. 熟练使用 ln 命令

ln，在两个文件间创建链接，默认为硬链接。  
 ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1658022344261-76b6aead-d531-4add-b299-887db53f6bbc.png#clientId=u5c63fa0f-145b-4&from=paste&height=379&id=u5c12367d&name=image.png&originHeight=758&originWidth=1158&originalType=binary&ratio=1&rotation=0&showTitle=false&size=172054&status=done&style=none&taskId=u7bafa233-cffe-4c5c-af82-2f8f3239f29&title=&width=579)  
ln -s  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1658022649965-7defd809-07ee-4c3a-98a2-da2e1b32025e.png#clientId=u5c63fa0f-145b-4&from=paste&height=355&id=u104ef42f&name=image.png&originHeight=710&originWidth=1152&originalType=binary&ratio=1&rotation=0&showTitle=false&size=149952&status=done&style=none&taskId=ued46f7d6-7100-484e-8e63-796742ede33&title=&width=576)

1. 我们修改了文件的 mode，在 git 中是否有更改操作

git diff 没有区别 但是命令行提示有修改

2. 我们修改了文件的 mtime，在 git 中是否有更改操作

touch xxx  
可以直接更新一个文件的 atime 和 mtime，改变到当前的系统时间。  
git diff 没有区别

3. 在 pnpm 中，为什么不全部使用软链接

其实都可以采用软链接找到依赖，nodejs 本身有提供一个叫做 --preserve-symlinks 的参数来支持 symlink，但实际上这个参数实际上对于 symlink 的支持并不好导致作者放弃了该方案从而采用 hard links 的方式：  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1658029671691-b03ed678-bf90-47f5-bb14-3f1154acd4d1.png#clientId=u5c63fa0f-145b-4&from=paste&height=194&id=u5887472d&name=image.png&originHeight=388&originWidth=1200&originalType=binary&ratio=1&rotation=0&showTitle=false&size=188658&status=done&style=none&taskId=uf634d25b-f4fa-4cb7-8b1d-09980aa1ab6&title=&width=600)

4. 在 Node.js 或其它语言中如何执行 ln

fs.symlinkSync('./a.js','./b.js');  
没有直接的方法建立硬链接

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/dc0rus
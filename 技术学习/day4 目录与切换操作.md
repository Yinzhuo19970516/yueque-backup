# day4 目录与切换操作
## cd

切换目录经常用，无压力学习  
cd - 进入上一次工作目录

## autojump

- Mac 安装：brew install autojump
- zsh 添加
- [[-s ~/.autojump/etc/profile.d/autojump.sh]] && ~/.autojump/etc/profile.d/autojump.sh
- autojump --version 检测安装成功
- j jc jo

## pwd

查看路径，经常用，无压力学习

## ls

查看字目录，经常用，无压力学习  
在日常工作中，常使用 ls -lah 列出工作目录内容。

- -a 列出所有文件 包含隐藏文件 a = all
- -l 列表形式 l = list
- -h 展示文件大小

## exa

好靓  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1657679112964-e266a8eb-d2f8-400c-88d5-7fec49e9ce84.png#clientId=u28ba5d50-31d7-4&from=paste&height=292&id=u910fe9d9&name=image.png&originHeight=584&originWidth=962&originalType=binary&ratio=1&rotation=0&showTitle=false&size=211468&status=done&style=none&taskId=u2f556c1f-bb74-4ed2-9a5e-9ea8ee78f21&title=&width=481)

## tree

非常适合展示项目文件  
tree xxx

## 作业

1. 熟悉 cd、pwd、ls、exa、tree 等命令

- done

2. 如何使用 cd 快速回到上次的工作目录

- cd -

3. 在 ls 单指令列出文件列表时为何不显示 .git 目录，应如何显示

- ls -a

4. 使用 tree/exa 列出目录树时，如何忽略 .gitignore 中文件内容

- tree --gitignore
- exa -alh -T -L 2 --git-ignore

5. 在 Node.js 或其它语言中如何获得 pwd

let path = require('path')  
let tmpPath = path.resolve(\_\_dirname,'./1.text)

const shell = require('shell.js')  
shell.pwd()

6. 在 Node.js 或其它语言中如何获得 ls 子文件列表。参考 [fsp.readdir](https://nodejs.org/api/fs.html#fspromisesreaddirpath-options) 及 [readdir](https://man7.org/linux/man-pages/man3/readdir.3.html)

fs.readdirSync("./");  
shell.js

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/oagtd0
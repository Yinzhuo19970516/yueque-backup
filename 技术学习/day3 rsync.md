# day3 rsync
## 拷贝本地文件 到 远端服务器

rsync -lahzv ~/Desktop/《老板管理手册》.pdf train:/home/train/Documents  
中文名文件上传之后发现会变成转义自符  
mac zsh 用户 修改 vim ~/.zshrc  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1657603196552-956f5e7c-31eb-401b-b1ee-81d405814635.png#averageHue=%230b0b0b&clientId=ub1ba4615-d825-4&from=paste&height=139&id=u476ab4ed&name=image.png&originHeight=278&originWidth=1138&originalType=binary&ratio=1&rotation=0&showTitle=false&size=56341&status=done&style=none&taskId=u1f5f33e8-03bc-4ea9-b702-5919698754f&title=&width=569)  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1657607309946-21b45909-3914-4039-9c43-464e157751a2.png#averageHue=%23030303&clientId=ub1ba4615-d825-4&from=paste&height=277&id=u7bc87d53&name=image.png&originHeight=554&originWidth=1016&originalType=binary&ratio=1&rotation=0&showTitle=false&size=86644&status=done&style=none&taskId=ub806fdd2-3253-44d8-b79d-07af45a7499&title=&width=508)

## 查看文件

ls -lah | grep 《老板管理手册》.pdf  
-r--r--r--@ 1 yinzhuo staff 1.7M 6 29 18:04 《老板管理手册》.pdf  
查阅了山月老师后面的文档

- ls -lah 意思为列出工作目录内容
- grep 命令用于查找文件里符合条件的字符串

## 拷贝文件

rsync -lahz 1.html 2.html  
cp 1.html 2.html

## 拷贝目录

rsync -lahz 1 2  
把 1 整个文件夹及其内容复制到 2  
 rsync -lahz 1/ 2  
把 1 文件夹中内容复制到 2  
 rsync -lahz 1 2/  
把 1 整个文件夹及其内容复制到 2  
 rsync -lahz 1/ 2/  
把 1 文件夹中内容复制到 2

## 作业

1. 熟悉 rsync 在本地及远程服务器间拷贝文件

都在本地执行  
拷贝本地到远端：rsync -lahzv ~/Desktop/work/test.js train: /home/train/Documents/student/yinzhuo  
拷贝远端到本地：rsync -lahzv train:/home/train/Documents/test.js ~/Desktop/work

2. 在 Node.js 或其它语言中如何实现 cp。参考 [fsp.cp](https://nodejs.org/api/fs.html#fspromisescpsrc-dest-options)。(cp 实际上是通过库函数 open/write 模拟实现)

- 复制单个的文件可以直接用 readFile、writeFile
- copyFileSync
- **const**fsPromises = **require**('fs').promises fsPromises.**copyFile**

3. 为何说保留复制文件时的元属性，对静态资源服务器有益

静态文件一般走浏览器缓存策略，Last-Modified 为上次修改时间，和文件元属性 mtime 相关

4. 在使用 rsync 传输前端项目时，如何忽略 node_modules 目录

rsync -lahzv ~/Desktop/work/hb-work/act-v3 train:/home/train/Documents/student/yinzhuo --exclude node_modules

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/vxgs9t
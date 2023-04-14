# day6 stat 查看文件系统信息
## stat

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1657855494206-60ecc928-470c-4874-83c6-a57d687d48ab.png#clientId=u6c84435f-2671-4&from=paste&height=154&id=ua24db2e4&name=image.png&originHeight=308&originWidth=1120&originalType=binary&ratio=1&rotation=0&showTitle=false&size=216064&status=done&style=none&taskId=u0966de34-0eab-4a96-a2de-4b820076cf9&title=&width=560)

- File Size 顾名思义
- 文件拥有者的 User ID
- 文件的 Group ID
- 文件的读、写、执行权限 Access
- ctime 是 Inode 上一变动时间，mtime 是上次内容变动大时间

## 使用 stat -c，可指定文件某个属性进行输出。

很难记，我感觉硬记也不好，需要的时候再去查吧。  
%F = File type 文件类型

## 文件类型

不好记啊，枚举值太多了  
目前记住了 regular file 和 directory 普通文件和文件夹

## 作业

1. 尝试说出四种以上文件类型

- regular file 和 directory 普通文件和文件夹
- symbolic link。符号链接,软链接
- block special file。块设备文件。
- character special file。字符设备文件。

2. 尝试说出四种以上文件元属性

- Size, 文件大小
- Inode, 文件的 Inode 编号
- Links, 文件的硬链接个数
- Access mode, 文件的访问权限
- Access time, atime, 文件的访问时间
- Modify time, mtime, 文件的修改时间
- Change time, ctime, 文件的修改时间，包括文件的属性修改时间

3. 如何查看某个文件的文件类型

- stat -c "%F" XXX
- ls -alh
  - -: regular file
  - d: directory
  - l: symbolic link
  - s: socket

4. 如何判断某个文件是一个软链接及硬链接

硬链接（Hard Link）符号链接（Symbolic Link）  
软链接可以理解成快捷方式，指向指针的指针，硬链接是指针  
stat XXX  
links 大于 1 就是硬链接  
软链接 stat 可以看到类型  
5.我们修改了文件的 mode，在 git 中是否有更改操作  
没有  
6.我们仅仅修改了文件的 mtime，在 git 中是否有更改操作  
没有  
7.在 Node.js 或其它语言中如何获取 stat 信息  
fs.stat()

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/pls1b1
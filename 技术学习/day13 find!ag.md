# day13 find/ag
## find

- find . -name '\*.json'
- find . -name '_1_'

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1658454066183-5d93196f-2984-4a16-be12-966a1fd04bb3.png#clientId=ued944f5c-2e29-4&from=paste&height=109&id=u210fcd49&name=image.png&originHeight=218&originWidth=460&originalType=binary&ratio=1&rotation=0&showTitle=false&size=22447&status=done&style=none&taskId=ua9647572-4e47-4557-8af3-94e4b7a29fa&title=&width=230)  
寻找硬链接的文件  
得先知道当前文件是硬链接，才能找到源文件  
和 stat 搭配使用

- find . -inum 1209947
- find . -samefile 1.hard.txt

![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1658455521941-ff137688-b3c4-4a4c-85ee-4f498d006fa2.png#clientId=ued944f5c-2e29-4&from=paste&height=207&id=ue5d2c640&name=image.png&originHeight=414&originWidth=1234&originalType=binary&ratio=1&rotation=0&showTitle=false&size=83892&status=done&style=none&taskId=u9eb68e15-5bad-40d0-a6ec-4fdbd2718c4&title=&width=617)  
git grep page  
这个命令比 webstorm 的搜索要快

## 作业

1. 如何找到当前目录及所有子目录下文件名包含 hello 的文件

find . -name '_hello_'

2. 如何找到当前目录及所有子目录下文件内容包含 hello 的文件

ag hello

3. 如何列出当前目录（不包含子目录）下的所有目录

find . -type d

4. 如果一个连接为硬链接，那如何在全局目录中找到该文件

find . -inum InodeXXX  
find . -samefile XXXX

5. 如何删掉当前目录中最近修改时间大于一年的全部文件

find . -mtime +365 -delete

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/aly2t0
# day37 sed
sed -n '3,5p' file # 打印文件第三行到第五行  
sed -i '2d' file # 删除文件第二行  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660546535685-6d6b91aa-da68-46dd-826b-2b694068bd4c.png#clientId=udbf27204-1273-4&from=paste&height=322&id=u2bc3576a&name=image.png&originHeight=644&originWidth=1256&originalType=binary&ratio=1&rotation=0&showTitle=false&size=86545&status=done&style=none&taskId=ueba72a6a-2341-4591-8d2b-defec3410bd&title=&width=628)  
sed -n 1p 1.md 打印第一行  
sed -n '$p' 1.md 打印最后一行  
sed /2/d 1.md 删除匹配字符串的行  
sed s/a/cc/ 1.md 把文件中的 a 换成 cc  
sed -e 'a hello append' 1.md 下一行添加  
sed -e 'i hello insert' 1.md 上一行添加

作业

- 如何使用 sed 替换文字

sed -i s/1/2/ 1.md 把 1 换成 2

- 如何使用 sed 替换文本每行的首空格  
  sed 's/\s\*//' 1.md

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/zkcsb6
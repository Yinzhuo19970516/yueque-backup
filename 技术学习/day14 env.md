# day14 env
environment variables 环境变量

## printenv

echo ${}

1. 你接触过那些系统环境变量

PATH  
USER  
HOME  
PWD  
SHELL  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1667961539269-36cf9a52-5db1-46dc-8bcc-ae09587b6fb6.png#averageHue=%23070707&clientId=u333f924f-6488-4&from=paste&height=163&id=ua6dca8f1&name=image.png&originHeight=163&originWidth=1031&originalType=binary&ratio=1&rotation=0&showTitle=false&size=58404&status=done&style=none&taskId=uaa43c8ec-01cc-4fdb-980a-7d94482bc17&title=&width=1031)  
mac 和 linux 大小写敏感  
但是 ${path}却可以拿到

2. 如何获取当前用户名

echo "$USER"  
 echo `who am i`  
 echo `whoami`

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/gs4qxy
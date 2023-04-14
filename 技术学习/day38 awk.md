# day38 awk
# 打印第九列

$ ls -lah | awk '{ print $9 }'

# $0 即打印全部文本

- 双引号：对命令直接输出，对变量名进行替换
- 单引号：全部原样输出

## 作业

1. 如何打印第十行数据  
   ls -lah | awk 'NR==10'  
   ls -lah | sed -n 10p
2. 如何将文本以 , 分割并列出第三列

ls -alh | awk '{FS=","}{print $3}'  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660614232519-420fdffa-fd95-44f1-b837-f964bae75892.png#clientId=u46a3925f-a627-4&from=paste&height=122&id=uf1543c13&name=image.png&originHeight=244&originWidth=1062&originalType=binary&ratio=1&rotation=0&showTitle=false&size=32503&status=done&style=none&taskId=ubfc36f7a-a45a-44dc-a47e-63ff3411b48&title=&width=531)

3. 找到所有包含 react 的行  
   ls -lah | awk '/React/'

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/ipg1m9
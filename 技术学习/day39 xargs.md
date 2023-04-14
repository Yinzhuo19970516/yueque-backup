# day39 xargs
## 并不是所有命令都支持 pipe 流式输入

对于不支持的，而我们又需要写命令行或者脚步命令时就需要用到 xargs  
xargs 就是可以将管道或标准输入的数据转换成命令行参数

## 作业

1. 如何将标准输入作为命令行参数  
   echo demo.json | xargs cat

等价于 cat demo.json  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660811582062-7a5b621d-b40a-4df1-bf49-228a4ef5bd47.png#clientId=ue2af8a3a-bdbe-4&from=paste&height=87&id=u09317973&name=image.png&originHeight=174&originWidth=1010&originalType=binary&ratio=1&rotation=0&showTitle=false&size=29514&status=done&style=none&taskId=uf1f10dea-c65e-44c7-a684-439b6b7fa72&title=&width=505)  
echo 2 | xargs -I {} head -n {} demo.json  
-I {} 相当于声明有多个参数，使用{} 来接收参数  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660812065800-bdf916ae-6c08-4836-b136-c76d665371d1.png#clientId=ue2af8a3a-bdbe-4&from=paste&height=70&id=u4b358c7a&name=image.png&originHeight=140&originWidth=1108&originalType=binary&ratio=1&rotation=0&showTitle=false&size=36223&status=done&style=none&taskId=u5dab12ba-4e2f-4e3a-8891-7760f02b7d5&title=&width=554)

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/nfowu2
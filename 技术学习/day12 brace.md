# day12 brace
## 作业

1. **如何列出当前目录下所有的 json 与 md 文件 **

现在有两种方法了  
 ls -lah _.(json|md)  
ls -lah {_.json,_.md}  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1658368719466-1826b1eb-5f67-49dc-871b-2ef6a0526c83.png#clientId=u1871abc8-3888-4&from=paste&height=86&id=u5b81f043&name=image.png&originHeight=172&originWidth=734&originalType=binary&ratio=1&rotation=0&showTitle=false&size=34667&status=done&style=none&taskId=u825353cc-4df6-468d-86d7-0f0d71aa1cc&title=&width=367)  
**2.如何创建 test000 到 test099.json 100 个 json 文件**  
**touch test{000..099}.json**  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1658368936549-2f672588-8366-4fde-99b3-59d61381e9b4.png#clientId=u1871abc8-3888-4&from=paste&height=414&id=u16f0106c&name=image.png&originHeight=828&originWidth=1176&originalType=binary&ratio=1&rotation=0&showTitle=false&size=188488&status=done&style=none&taskId=ube6e0a81-3567-49bd-8075-ed68a531c1f&title=&width=588)  
3.如何列出所有带有数字 258 的文件  
ls -lah _[258]\*.json  
4.怎么删除新增的** test000 到 test099.json 100 个 json 文件**  
rm test{000..099}.json

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/tslv99
# day5 用户管理
现在有 2 个小可爱和我一起学习  
w 命令确实更清晰，更语义化一些  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1657765220309-bb29ca0c-3e01-46cb-961d-ffe36b0530fb.png#clientId=uc82d0a01-6f86-4&from=paste&height=339&id=u5ece8b79&name=image.png&originHeight=678&originWidth=1164&originalType=binary&ratio=1&rotation=0&showTitle=false&size=127624&status=done&style=none&taskId=ueccf38f8-145d-4a74-a89c-d1f17a21c95&title=&width=582)  
还是很多小可爱在熬夜啊  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1657765350936-448a4970-6772-4df4-bc01-d312a4a6b28c.png#clientId=uc82d0a01-6f86-4&from=paste&height=227&id=ubf546329&name=image.png&originHeight=454&originWidth=1146&originalType=binary&ratio=1&rotation=0&showTitle=false&size=96093&status=done&style=none&taskId=u1d942960-1ca4-4680-88bb-78f5620f0c4&title=&width=573)  
做作业

1. 如何查出当前服务器上有多少个登录用户

w who

2. 如何查出某天服务器上有多少个登录用户

last -s 2022-9-21 -t 2022-9-22  
 3.1000 与 501 的用户 ID 代表什么  
Linux 第一个用户 id 默认为 1000，而在 MacOS 第一个用户默认为 501  
那么我此时是 1001，登陆也只有我一个，1000 是谁  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1663918226836-d712fe13-809d-4461-9738-db0e9d819ea9.png#clientId=ueb02d52f-e689-4&from=paste&height=55&id=uc08c4949&name=image.png&originHeight=110&originWidth=1100&originalType=binary&ratio=1&rotation=0&showTitle=false&size=37411&status=done&style=none&taskId=u51111eaf-ef7e-45c7-8804-5cef867cd55&title=&width=550)  
 4.如何打印你自己服务器的 user id 及 user name  
id  
whoami =(who am i)

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/srqwdr
# day2 ssh 隧道
服务端执行：npx serve . -p 5000  
本地执行：ssh -NL 5000:localhost:5000 train  
本地打开：localhost:5000  
可以在浏览器看到 tarin 文件夹所有文件  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1657466897503-f30dacac-3201-4879-91bb-570ca598de06.png#clientId=ubfcf6f9f-cb98-4&from=paste&height=257&id=u30ab78fa&name=image.png&originHeight=513&originWidth=934&originalType=binary&ratio=1&rotation=0&showTitle=false&size=52954&status=done&style=none&taskId=ucbbe3b85-9ac8-425c-880b-4acbfe1eee9&title=&width=467)  
本地启动： npx serve . -p 5000  
本地执行：ssh -NR 5001:localhost:5000 train  
服务端执行:curl locahost:5001  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1657504636492-e5eef121-01b6-4681-877a-d318a4b781c6.png#clientId=u05927aae-55ea-4&from=paste&height=681&id=u72fee919&name=image.png&originHeight=1362&originWidth=2804&originalType=binary&ratio=1&rotation=0&showTitle=false&size=234337&status=done&style=none&taskId=u943fb512-5085-43ad-9051-48e9a896cc1&title=&width=1402)  
L = local  
R = remote

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/lefrre
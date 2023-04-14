# day40 curl
curl 相当于 fetch  
这个之前工作中有使用过

curl 不加参数直接发送 get 请求

- --include
  - 打印 request Header reponse Body
  - curl [https://jsonplaceholder.typicode.com/todos/1](https://jsonplaceholder.typicode.com/todos/1) -i
- --verbose
  - 查看发送报文及 TLS handshake 的详细
- --location  
  ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660814103038-d1440286-5283-4b47-9f7f-f7d1b4be4674.png#averageHue=%23f5f6f7&clientId=u59edfac5-23a1-4&from=paste&height=143&id=uf9a0c889&name=image.png&originHeight=286&originWidth=815&originalType=binary&ratio=1&rotation=0&showTitle=false&size=33363&status=done&style=none&taskId=ua83aaba4-7b83-42fa-ad10-88dd4c16ec3&title=&width=407.5)

简单解释一下这个例子，乍一看挺费解的。  
直接 curl [http://zhihu.com](http://zhihu.com) 返回 301 说明重定向  
 浏览器请求：[http://zhihu.com](http://zhihu.com) 会重定向至：[https://www.zhihu.com/signin?next=%2F](https://www.zhihu.com/signin?next=%2F)

    curl --head --location http://zhihu.com

第一次响应永久重定向 301 重定向至 [https://www.zhihu.com](https://www.zhihu.com)  
 第二次响应暂时重定向 302 重定向至 //www.zhihu.com/signin?next=%2F  
 第三次响应 200 成功

所以是三次响应，两次重定向  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660813727390-079e7cbe-1193-4881-a66a-4bcf8ad8e2aa.png#averageHue=%23120d0d&clientId=u59edfac5-23a1-4&from=paste&height=241&id=u72ab6cb3&name=image.png&originHeight=482&originWidth=1156&originalType=binary&ratio=1&rotation=0&showTitle=false&size=106124&status=done&style=none&taskId=uea31303d-7b6c-4f2e-b939-c572dd0d67d&title=&width=578)  
 ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660813926099-0e73fb01-36f6-464d-932a-7eb54df74d39.png#averageHue=%23131212&clientId=u59edfac5-23a1-4&from=paste&height=763&id=ue6f94ad9&name=image.png&originHeight=1526&originWidth=2634&originalType=binary&ratio=1&rotation=0&showTitle=false&size=696049&status=done&style=none&taskId=uf58dc0fc-6eb0-4761-b699-4512b0bfa1b&title=&width=1317)  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660814040077-d3e6e28d-2bb9-4f27-a04a-0f06375ffccd.png#averageHue=%23121111&clientId=u59edfac5-23a1-4&from=paste&height=246&id=udb0f2c03&name=image.png&originHeight=492&originWidth=1610&originalType=binary&ratio=1&rotation=0&showTitle=false&size=152504&status=done&style=none&taskId=ufa123b54-f35f-400f-8d67-ff8d7b4a4c3&title=&width=805)

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/xkfygi
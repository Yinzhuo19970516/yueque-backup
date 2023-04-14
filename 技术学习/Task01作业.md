# Task01作业
### 断点调试 webpack 源码，了解其编译时间（startTime/endTime）是如何计算的

看了大家的答案，我有如神助  
我用的 webstorm 进行调试的，倒着进入  
首先从 startTime 处打断点，然后进行调试  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660877758518-bacf7a46-0190-481f-9f26-7eb098328943.png#clientId=u7cb200ea-5f2c-4&from=paste&height=304&id=ub82590d9&name=image.png&originHeight=304&originWidth=1094&originalType=binary&ratio=1&rotation=0&showTitle=false&size=46725&status=done&style=none&taskId=u34694d2f-2402-4207-ae3f-e70a1e93399&title=&width=1094)  
点击堆栈中第二行  
定位到 webpack/lib/compile.js 的 run 方法  
进到 finalCallback，继续找到调用这个函数的位置  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660879032816-35cb5cc9-8ee4-4500-8a2a-c8f57dd34f6b.png#clientId=u7cb200ea-5f2c-4&from=paste&height=359&id=u0c0e9033&name=image.png&originHeight=359&originWidth=1164&originalType=binary&ratio=1&rotation=0&showTitle=false&size=80792&status=done&style=none&taskId=u5b945e36-6dc2-434e-998b-a6c1ef93125&title=&width=1164)  
发现 finalCallback 下面有 startTime 和 endTime 进行了定义  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660878850324-6e1d23f5-5755-4225-a835-90bfce82a8ee.png#clientId=u7cb200ea-5f2c-4&from=paste&height=475&id=u1b3fd151&name=image.png&originHeight=475&originWidth=748&originalType=binary&ratio=1&rotation=0&showTitle=false&size=60548&status=done&style=none&taskId=uc4e01764-50c0-468e-8c66-f9a30c35441&title=&width=748)  
总结就是 执行 onCompiled 之前定义开始时间，执行成功之后获取结束时间，然后定义到 stat 对象，输出

## 断点调试执行 webpack 命令时的流程，体验它是如何在 webpack/webpack-cli 间相互调用的

直接调试 webpack/bin/webpack.js 文件。  
webpack.js 的 runCLI(cli) 调用了 webpack-cli/bin/cli.js  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1660889912146-7ad0055f-d796-4c54-9f94-3a0c24bfc7e3.png#clientId=u7cb200ea-5f2c-4&from=paste&height=514&id=u0243f062&name=image.png&originHeight=514&originWidth=1267&originalType=binary&ratio=1&rotation=0&showTitle=false&size=64032&status=done&style=none&taskId=ue1b0dc2a-54ee-4933-97a4-eb37677f6e1&title=&width=1267)

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/gezgsx
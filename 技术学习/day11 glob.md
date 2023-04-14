# day11 glob
glob 是一种匹配模式，已经被整合到了 shell 之中，叫做 shell 通配符

- **/\*  
  ** 匹配目录 *匹配文件  
  ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1658283753831-36d55903-0259-4d6d-bc5e-0c298c6d0171.png#clientId=uce9dccbf-6313-4&from=paste&height=158&id=ue59c7a7e&name=image.png&originHeight=316&originWidth=906&originalType=binary&ratio=1&rotation=0&showTitle=false&size=71344&status=done&style=none&taskId=u85edbf47-ecf0-4ef9-9f68-6dd2598cc70&title=&width=453)  
  ？匹配字符个数  
  ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1658283905952-1be852e9-21b0-46c9-893e-62ddb330d988.png#clientId=uce9dccbf-6313-4&from=paste&height=37&id=u8d38afa0&name=image.png&originHeight=74&originWidth=722&originalType=binary&ratio=1&rotation=0&showTitle=false&size=16756&status=done&style=none&taskId=uf8d2e8aa-31f7-45e3-9676-96130d2ef65&title=&width=361)  
  [...]：range，匹配方括号内所有字符  
  列出所有以 json/md 结尾的文件  
  ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1658284798973-eab83e44-479c-405b-8259-2a76d1d14d82.png#clientId=uce9dccbf-6313-4&from=paste&height=45&id=u6f198711&name=image.png&originHeight=90&originWidth=726&originalType=binary&ratio=1&rotation=0&showTitle=false&size=19474&status=done&style=none&taskId=ub7edc469-f889-4cba-ae75-03bc36163b0&title=&width=363)  
  假设有 test000.json 到 test099.json 100 个文件，如何列出所有带有数字 258 的文件  
  ls -alh *258\*.json

## 作业

1. 如何列出当前目录下所有的 js 文件

ls -lah \*.js

2. 如何列出当前目录下所有的 js 文件和 json 文件  
   ls -lah \*.(js|json)  
   ![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1664117803635-7400af7e-0d8e-447e-9b0a-3c09cc6fe450.png#clientId=ub9320600-78f8-4&from=paste&height=396&id=uc293bd33&name=image.png&originHeight=396&originWidth=976&originalType=binary&ratio=1&rotation=0&showTitle=false&size=72200&status=done&style=none&taskId=ud5ec0fdd-8cee-463f-81ac-7621d7e30bb&title=&width=976)
3. 假设有 test000.json 到 test099.json 100 个文件，如何列出所有带有数字 258 的文件  
   ls -alh _258_.json

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/trb5s1
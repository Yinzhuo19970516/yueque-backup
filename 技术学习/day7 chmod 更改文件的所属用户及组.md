# day7 chmod 更改文件的所属用户及组
通过 chown -R，可一并将子文件所属用户及用户组进行修改。  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1657964705795-631a14cd-8603-4b1a-91d6-f7a0b347e293.png#clientId=uec9bdfe0-54b4-4&from=paste&height=80&id=ub0ce1bf4&name=image.png&originHeight=160&originWidth=524&originalType=binary&ratio=1&rotation=0&showTitle=false&size=28252&status=done&style=none&taskId=u8ace68f7-3b7e-4835-b9cd-205ff91978c&title=&width=262)

# %A：获得可读化的 mode

stat -c %A README.md  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1657964855377-11fd3217-5b6a-4ba2-9041-0ad1afdd3a24.png#clientId=uec9bdfe0-54b4-4&from=paste&height=26&id=ua70cd9a1&name=image.png&originHeight=52&originWidth=578&originalType=binary&ratio=1&rotation=0&showTitle=false&size=8792&status=done&style=none&taskId=u4edea3b0-2f97-4123-9d4e-26fdbaccc83&title=&width=289)

# rw-：当前用户可写可读

# rw-：当前用户组可写可读

# r--：其它用户可读

chmod 777 a.txt  
开通了所有的权限  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1657965007776-7f4c2b5b-6904-41c1-a055-763287f6e072.png#clientId=uec9bdfe0-54b4-4&from=paste&height=55&id=ucb213425&name=image.png&originHeight=109&originWidth=332&originalType=binary&ratio=1&rotation=0&showTitle=false&size=13134&status=done&style=none&taskId=u4691df2f-14ab-45a8-8684-a7128eb84c4&title=&width=166)

## 作业

1. 给某一个文件的所有用户（ugo）都移除 Read 权限

chmod a-r xxx

2. 给某一个文件的所有用户（ugo）都添加 Read 权限

chmod a+r xxx

3. 为文件添加权限时，数字 600 代表什么意思

   -rw-------  
    只有当前用户可读可写，当前用户组不可读不可写，其他用户不可读不可写

4. 当我们新建了一个文件时，他默认的 mode 是多少

   664 = -rw-rw-r--  
    当前用户组可读可写，当前用户组可读可写，其他用户可读

5. 如何获取一个文件的 username 与 groupname  
   stat -c %G a.txt

stat -c %U a.txt

6. 在 Node.js 或其它语言中如何修改 user 及 mode

   fs.chown()方法用于异步更改给定路径的所有者和组  
    fs.chmod()方法用于更改给定路径的权限

1. 给某一个文件的所有用户（ugo）都添加 R 权限

chmod a+r xxx

2. 如何获取一个文件的 username 与 groupname

答案在上一节  
stat -c %G a.txt  
stat -c %U a.txt

3. 在 Node.js 或其它语言中如何修改 user 及 mode

fs.chown

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/bzxugu
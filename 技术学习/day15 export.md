# day15 export
## 作业

1. 如何配置环境变量

- export NODE_ENV=production 仅仅在当前 shell 窗口有效
- 修改**~/.bashrc 或者 ~/.zshrc，修改后执行 source xxx  
  重启 shell 永久生效**
- **执行命令前置环境变量，仅仅这次生效**

2. ${var:=word} 是什么意思

如果 var 不存在，则使用默认值 word。并赋值 $var - word  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1658632855688-c21a912f-9336-4958-b30b-ceef518f34fe.png#clientId=ufc95c67c-0423-4&from=paste&height=34&id=u8e90ab17&name=image.png&originHeight=67&originWidth=302&originalType=binary&ratio=1&rotation=0&showTitle=false&size=8108&status=done&style=none&taskId=uddfb16db-3a76-4623-887a-0ab6eda592a&title=&width=151)

3. 使用 export 配置的环境变量如何永久生效

修改**~/.bashrc 或者 ~/.zshrc，修改后执行 source xxx**

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/oiovcx
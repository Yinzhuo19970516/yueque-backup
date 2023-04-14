# day1 免密登陆
1.客户端生成密钥,连续点击三次确认，在.ssh 目录下生成下生成密钥文件，其中，id_rsa 为私钥 id_rsa.pub 为公钥。  
ssh-keygen -t rsa  
2.在.ssh 目录新建 config 文件，增加配置文件  
touch config

```bash
# 修改 ssh 配置文件 ~/.ssh/config

Host shanyue
  HostName 172.16.3.2
  User root
  Host train
HostName 121.37.177.179
  User train
  IdentityFile ~/.ssh/id_rsa
```

3.密码登陆服务器  
在终端输入 ssh train@121.37.177.179  
密码输入 xunlianying10086  
4.进入服务器 .ssh/  
cd ~/.ssh  
然后把自己的公钥 id_rsa.pub 加入到 authorized_keys 文件中  
vim authorized_keys  
i  
esc  
:wq;  
5.退出服务器 logout  
6.重新登陆 ssh train  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1657004531539-eeb5bb58-a0c0-44aa-96b7-6d3fb85487a0.png#clientId=uca1bf72b-5d90-4&from=paste&height=94&id=u0fb47df0&name=image.png&originHeight=188&originWidth=966&originalType=binary&ratio=1&rotation=0&showTitle=false&size=28798&status=done&style=none&taskId=u0ee1fece-066e-4250-865d-1f7c7aa54c6&title=&width=483)

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/mm3pif
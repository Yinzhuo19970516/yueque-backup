# day17 zsh/ohmyzsh
1. 如何判断当前处在哪个 shell 下

echo $SHELL  
echo $0  
ps 查看当前运行的 shell 是什么  
输一条不存在的指令，根据报错信息开头判断  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1667898592412-2cd588a9-c1ca-4c59-979b-cd95e9c311a1.png#averageHue=%23050505&clientId=u0884da04-4556-4&from=paste&height=494&id=u6e704058&name=image.png&originHeight=494&originWidth=1046&originalType=binary&ratio=1&rotation=0&showTitle=false&size=89658&status=done&style=none&taskId=u40a7c61b-8528-49da-9e9c-01cbe28027b&title=&width=1046)

2. 你喜欢 zsh 下哪个主题

ZSH_THEME="random"  
用了两个月，还是停不下来  
这就是随机主题的魅力吗，根本停不来  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1658801722706-8adfc8cb-55fa-4f7c-96f7-f84f5b1832e1.png#averageHue=%23060505&clientId=u443cff74-1721-4&from=paste&height=606&id=oKlB3&name=image.png&originHeight=1212&originWidth=1428&originalType=binary&ratio=1&rotation=0&showTitle=false&size=340577&status=done&style=none&taskId=u912df6e4-47b8-4905-a757-4af5e3dbf1e&title=&width=714)

3. zsh 有哪些比较有意思的 plugin

git zsh-syntax-highlighting  
我目前只用了这两个，等大家的答案

## 作业

1. sh 与 bash 的区别是什么。见 [Difference between sh and Bash](https://stackoverflow.com/questions/5725296/difference-between-sh-and-bash)

sh，即 Shell Command Language 的简称，是由 [Shell Command Language](https://pubs.opengroup.org/onlinepubs/009695399/utilities/xcu_chap02.html) 表述的一份规范。  
而 bash/zsh/fish/dash 等是基于该规范的实现。  
通常脚本文件开头 #!后面是脚本的解释器程序路径  
在 Linux 系统上/bin/sh 往往是指向/bin/bash 的符号链接。

```
#!/bin/bash
#!/bin/sh
```

2. 如何找到 sh 真正的应用程序是哪个，比如 bash 还是 dash

cat /etc/shells 可以查看系统可使用的 shell 类型  
 ll /bin/sh 查看当前 sh 状态  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1667898110878-4775c9d0-ada8-4244-acd1-c24ad5853f9b.png#averageHue=%23040404&clientId=u0884da04-4556-4&from=paste&height=364&id=ub1bf675a&name=image.png&originHeight=364&originWidth=1136&originalType=binary&ratio=1&rotation=0&showTitle=false&size=63625&status=done&style=none&taskId=u30ae6f4c-b62f-4646-b285-e76b4949611&title=&width=1136)

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/zmluh7
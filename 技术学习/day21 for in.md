# day21 for in
建立一个数组，进行遍利  
 for element in ${list[@]}  
 do  
echo $element  
done  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1659315787022-0774db11-8086-43fd-9dfb-7bc287146e30.png#clientId=u25276e40-03db-4&from=paste&height=248&id=ue121f0a6&name=image.png&originHeight=248&originWidth=924&originalType=binary&ratio=1&rotation=0&showTitle=false&size=26955&status=done&style=none&taskId=u539f582e-a66a-43f6-8831-54231ee2969&title=&width=924)  
for (( i = 0; i < ${#list[@]}; i++ )); do echo $i; done  
![image.png](https://cdn.nlark.com/yuque/0/2022/png/1572912/1659315835269-a7a7eaa3-a70f-40cc-994f-fb9fe2dfe702.png#clientId=u25276e40-03db-4&from=paste&height=190&id=u1924a01e&name=image.png&originHeight=190&originWidth=972&originalType=binary&ratio=1&rotation=0&showTitle=false&size=23891&status=done&style=none&taskId=u6c760246-bffd-46a3-b2fb-c13739a2fb8&title=&width=972)  
作业

1. 如何遍历输出 1 至 100

for (( i = 1; i <=100; i++ )); do echo $i; done  
for i in {1..100}; do echo $i; done

2. 判断某个命令是否存在，存在输出 ok，不存在输出 not ok、

if [[-n $(command -v xxx)]]; then echo ok; else echo not ok; fi  
command -v xxx 用以判断 xxx 命令是否存在

<br>
  
> 语雀地址 https://www.yuque.com/yuqueyonghuyv23kd/xebk9e/zwbl6h